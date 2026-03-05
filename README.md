# Tux going to cloud

> https://terokarvinen.com/linux-palvelimet/

> 6.2.2026

Ostin oman Virtual Private Serverin(vps), johon valikoitui euroopasta > https://www.hetzner.com.
Valintaan vaikutti sijainti turvallisuuden takia, hinta lompakon takia ja maine molempien takia. Serverit sijaitsevat väitetysti Saksassa, Suomessa, Singaporessa, yhdysvalloissa. Yritys operoi saksasta, joten heihin vaikuttavat saksan/euroopan lait. Servereihin vaikuttaa vielä paikalliset sääntelyt, joten vailtsin omille servereille joko Suomen tai Saksan sijainniksi.

Muuten valitsin itse tietokoneeksi halvimman vaihtoehdon mitä oli saatavailla "regular performance" sarjassa.
Käyttöjärjestelmäksi linux debian v.13. 

<img width="1047" height="968" alt="createdHertzner" src="https://github.com/user-attachments/assets/c82bacc5-7c15-4758-b886-ad4458c3702a" />
Palvelimen määrittelyssä lisäsin enään vain ssh-avaimen, jotta pääsin sillä suoraan sisään.
Omalla koneella komento:
```bash
ssh-keygen # enter,enter,enter...defaults->~/.ssh/keys..
```
***ssh-keygen*** loi kotihakemiston kansioon ```~/.ssh/``` julkisen ja yksityisen avaimen.
Kopioin julkisen avaimen hetzner:n sivustolle koneen luomisen yhteydessä, ssh-osioon. ```cat ~/.ssh/id_jdwioq28.pub```
<img width="634" height="489" alt="ssh" src="https://github.com/user-attachments/assets/ea887954-f5ba-4109-a448-c6d1e4b40d18" />

## Alkutoimet

Ensimmäinen yhteys serverille:
hertzner lisännyt antamani julkisen avaimen serverin /root/.ssh/auhtorized_keys tiedostoon.
```ssh root@<ip>``` ja pääsin sisään ssh-avaimella, jota oman koneen ssh-client käytti default ~/.ssh/ hakemistosta. Muuten avaimen joutuu hakea erikseen ```ssh -i polku/avaimelle/key.pub root@<ip>```

Ensimmäiseksi loin uuden käyttäjän, josta tein admin-käyttäjän lisäämällä sudo-ryhmään.
Tämän kautta hallitsin ja kirjauduin jatkossa järjestelmään.
```adduser nothing```
```usermod -aG sudo nothing```

Testasin, että kirjautuminen onnistui salasanalla. ```ssh nothing@<ip>```
¨Jonka jälkeen siirsin omalta koneelta julkisen avaimen nothing-käyttäjän haltuun palvelimelle ja testasin uudestaan kirjautumisen avaimen avulla, joka onnistui.
```bash
ssh-copy-id -i polku/avaimelle/id-dwj.pub nothing@<ip>
# Ilman -i kopioi kaikki löytyvät avaimet serverille
```

Tämän jälkeen lukitsin root-käyttäjän, poistin avaimet root-käyttäjältä ja poistin ssh:n asetuksista kirjautumisen root-oikeuksilla.

```bash
su - nothing;
# nothing käyttäjälle.
sudo usermod -l root;
sudo mv -nv /root/.ssh/ /root/DISABLED-ssh/;
#  siirretään root:n avaimet pois käytöstä default-kansiosta.
sudo vi /etc/ssh/ssh_config
----
PermitRootLogin no
LogLevel VERBOSE
----
sudo service ssh restart;
```

Palomuurin asetukset.

Nopeat palomuuriasetukset sai tehtyä ufw:lla, jotta vielä ssh-yhteys pelaa.
```bash
sudo apt-get update;
sudo apt-get install ufw;
sudo ufw enable;
sudo ufw allow 22/tcp;
```

Serveriä varten tarkemmat palomuuriasetukset saa tehtyä ***nftabkes***:lla asetustiedostoon(nftables.conf), jonka jälkeen käynnistin palvelun uudestaan ```sudo systemctl restart nftables```
> https://stosb.com/blog/explaining-my-configs-nftables/#Set
sudo vi /etc/nftables.conf:
``` #!/usr/sbin/nft -f
###############################################################################
# HARDENED SERVER FIREWALL
# Filosofia:
#  - Zero Trust
#  - Default deny everywhere
#  - Minimize attack surface
#  - Control egress to prevent exfiltration
###############################################################################

flush ruleset

############################
# MUUTTUJAT
############################
# Samat kuin järjestelmässä. Muuten ei toimi DNS. Tai pelkästään udp/tcp DPORT arvoilla.
define dns_v4 = { 8.8.8.8, 8.8.4.4 }
define dns_v6 = { 2001:4860:4860::8888, 2001:4860:4860::8844 }

############################
# TAULU: inet
############################

table inet filter {

###############################################################################
# EARLY DROP – Tiputetaan heti
###############################################################################
  chain early_drop {
    # Vialliset/epäkelvot paketit tiputetaan.
    ct state invalid drop

    # TCP paketit ilman SYN:iä uudessa yhteydessä → skannaus / fuzz
    tcp flags & (syn|ack) == ack ct state new drop

    # Xmas / NULL / outo lippukombo
    tcp flags & (fin|syn|rst|psh|ack|urg) == 0 drop
    tcp flags & (fin|syn) == (fin|syn) drop
  }

###############################################################################
# INPUT – SISÄÄNTULEVA
###############################################################################
  chain input {
    type filter hook input priority filter;
    policy drop;

    # Koneen sisäiset palvelut toimintaan.
    iif lo accept

    # Ajetaan varhainen siivous
    jump early_drop

    # Olemassa olevat yhteydet
    ct state established,related accept

    # ICMP / ICMPv6 !! - Verkon toiminnan kannalta tärkeä! ping, traceroute, neighbour disc..
    # ```meta l4proto { icmp, ipv6-icmp } accept``` molemmille.
    # Muuten erikseen: ip4 ja ip6 icmp erilaisia.
    icmp type { echo-request } limit rate 4/second accept
    icmpv6 type { echo-request } limit rate 4/second accept
    
    # SSH – yksi portti, rajoitettu
    tcp dport 22 ct state new limit rate 3/minute accept

    # WEB
    tcp dport { 80, 443 } ct state new accept

    # Loput: loki + drop
    log prefix "INPUT DROP: " flags all counter drop
  }

###############################################################################
# OUTPUT – ULOSMENEVA
###############################################################################
  chain output {
    type filter hook output priority filter;
    policy drop;

    # Valmiiksi aktiiviset sessiot.
    ct state established,related accept

    # DNS
    ip daddr $dns_v4 udp dport 53 accept
    ip daddr $dns_v4 tcp dport 53 accept
    ip6 daddr $dns_v6 udp dport 53 accept
    ip6 daddr $dns_v6 tcp dport 53 accept

    # NTP-serveri aikaa varten
    udp dport 123 accept

    # Uudet yhteydet sallitaan, jotta päivitykset ym. toimii
    tcp dport { 80, 443 } ct state new accept

    # Loput logiin -> sudo journalctl -u ssh
    log prefix "OUTPUT DROP: " flags all counter drop
  }

###############################################################################
# FORWARD – EI IKINÄ
###############################################################################
  chain forward {
    type filter hook forwards priority filter;
    policy drop;
  }
}
```

Tämän jälkeen asensin uudestaan apache2:n weppipalvelimen vps koneelle samalla tavalla kuin aikaisemmassa.

uusi käyttäjä, asetukset /etc/apache2/sites-available/user.conf
www-data:lle reitit. /home/käyttäjä:x ja /home/käyttäjä/public_html/:rX
serverin restart ja uudella käyttäjälle tekemään hakemistot public_html/index.html
```bash
sudo adduser kayttaja
sudo vi /etc/apache2/sites-available/100-kayttaja.conf
sudo a2ensite /etc/apache2/sites-available/100-kayttaja.conf
sudo systemctl restart apache2
su - kayttaja
mkdir ~/public_html
setfacl -m u:www-data:x /home/kayttaja
setfacl -m u:www-data:rX /home/kayttaja/public_html/
setfacl -d -m u:www-data:rX /home/kayttaja/public_html/
echo homepages >> ~/public_html/index.html
```

- ***default***-sivu: Loin uuden asetustiedoston(alla) /etc/apache2/sites-available/900-def.conf
- Tein asetukset(def.conf) www-datalle kansioon /var/www/default-www/ ja tein kansion omistajaksi www-data:n
- Otin uuden asetustiedoston käyttöön ja restarttasin serverin.
  
```bash
sudo vi /etc/apache2/sites-available/900-def.conf
sudo a2ensite /etc/apache2/sites-available/900-def.conf
sudo systemctl restart apache2
sudo chown -R www-data /var/www/
echo default | sudo tee /var/www/default-www/index.html
```
```
sirnothing@debian-4gb-hel1-1:/var$ cat /etc/apache2/sites-available/100-ivhuolto.conf 
<VirtualHost *:80>
	ServerName www.ivhuolto.example.com
	ServerAlias ivhuolto.example.com
	DocumentRoot "/home/ivhuolto/public_html"
	<Directory "/home/ivhuolto/public_html/">
		require all granted
	</Directory>
</VirtualHost>

		
sirnothing@debian-4gb-hel1-1:/var$ cat /etc/apache2/sites-available/900-def.conf 
<VirtualHost *:80>
	ServerName _
	ServerAlias _

	DocumentRoot "/var/www/default-www/"
	<Directory "/var/www/default-www/">

	require all granted
	</Directory>
</VirtualHost>
```

<img width="938" height="387" alt="curlitApache" src="https://github.com/user-attachments/assets/a2e892fb-0504-48a4-b951-033400ed7b41" />


Ongelmaksi tuli ensiksi def.conf-tiedoston nimessä, kun apache2 valitsee ensimmäisen VH/tiedoston, jos mikään ei vastaa kyselyä. Tässä def.conf oli ivhuolto.conf tiedostoa edellä aakkosissa, joten def.conf VH valittiin ensimmäiseksi.
Vaihto 900-def.conf ja 100.ivhuolto.conf korjasi tilanteen ja ivhuolto näytetään ensiksi. Tässä tapauksessa, kun näytetään kaikilla domaineilla ivhuolto ja vasta, jos se on pois päältä, niin default-sivut.








