# Apache HTTP-server

Asensin apachen version 2.4 web-serverin linux:lle, joka pyörii virutaalikoneella VirtualBox:n päällä.

### Alusta

 ***linux***:
 ```bash
 sudo cat /etc/os-release;
 ```
- PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
- NAME="Debian GNU/Linux"
- VERSION_ID="13"
- VERSION="13 (trixie)"
- VERSION_CODENAME=trixie
- DEBIAN_VERSION_FULL=13.3

### a) Asennus
Asensin ensiksi itse apachen käyttäen debian:n paketinhallintaa ***apt-get**
https://httpd.apache.org/docs/2.4/install.html
```bash
sudo apt-get update;
sudo apt-get install apache2 -y;
```

***Apache serveri***:
```bash
sudo apache2 -v;
```
- Server version: Apache/2.4.66 (Debian)
- Server built:   2025-12-05T18:52:34


### a) Serverin testaaminen
 #### main-server
Serveri suoraan palveli apachen main serverin sivua osoitteessa http://localhost:80/ ja tietokoneen kansiosta /var/www/, johon tarvitaan root-oikeudet.

#### oma serveri
Loin apache asetukset ja oikeudet niin, että virtuaalinen host käyttää kotihakemiston kansiota juurenaan.
Pystytään tekemään ja muokkaamaan omilla oikeuksilla tiedostoja.

```bash
mkdir -p ~/publicsite/nothing/;
# -p luo isäntäkansiot, jos niitä ei ole olemassa.

sudo setfacl -m u:www-data:x /home/$(users)/;
# (-m) muokataan kulkuoikeudet(x) kansioon acpache:n(www-data) "käyttäjälle.
# Voi myös tehdä ***sudo chmod ugo+x /home/;*** Hankalampi, kun kaikilla muilla samat oikeudet, jos useampi käyttäjä järjestelmässä.
sudo setfacl -R -m u:www-data:rX /home/$(users)/publicsite;
sudo setfacl -R -d -m u:www-data:rX /home/$(users)/publicsite;
# (-R) oikeudet olemassa oleville tiedostoille/alikansioille ja niiden tiedostoille.
# (-d) default oikeudet kansiolle/sisällölle ja alikansioille(ei niiden tiedostoille). -R, perii myös alikansioiden tiedostot.
sudo a2dissite /etc/apache2/sites-available
# poistetaan main-serveri käytöstä ja luodaan uusi tiedosto kansioon. sudo vi /etc/apache2/sites-available/nothing.conf
```
Sisältö:
```sirnothing@housename:/etc/apache2/sites-available$ cat nothing.conf 
<VirtualHost *:80>
	ServerName nothing.example.com
	ServerAlias www.nothing.example.com

	DocumentRoot "/home/sirnothing/publicsite/"
	<Directory "/home/sirnothing/publicsite/">
		require all granted
	</Directory>
</VirtualHost>
```
Toin uudet asetukset apachelle, loin index.html:n kotihakemiston publicsite-kansioon ja käynnistin palvelimen uudelleen.
```bash
sudo a2ensite /etc/apache2/sites-available/nothing.conf;
echo hello local >> ~/publicsite/index.html;
sudo systemctl restart apache2;
```
http://localhost/ näytti index.html:n omasta kansiosta.
<img width="391" height="90" alt="localhost_own" src="https://github.com/user-attachments/assets/5b52d230-e64b-4503-9790-e8de0feaf5d0" />

### Lokien selaaminen

Sivulla onnistuneesti käytyjen yritysten lokitiedoston selaaminen, jos ei ole erikseen luoto vhost:lle omia lokitiedostoja.
```bash
sudo tail -f /var/log/apache2/other_vhosts_access.log;
```
<img width="1336" height="187" alt="tailLog" src="https://github.com/user-attachments/assets/c5260bac-781f-47b2-bb91-8fb7a6eb28a3" />

- Ensimmäinen sarake näyttää mistä pyyntö on tullut, joka on ipv6 loopback(localhost) osoite ```::1```.
- Sen jälkeen näytetään päivämäärä, kellonaika ja aikavyöhyke. ```[27/Jan/2026:14:20:59 +0200]```
- Itse http-kyselyn sisältö jossa pyydetään index-tiedostoa juuresta käyttäen http:n versiota 1.1. ```"GET / HTTP/1.1"```
- Serverin vastauskoodi, OK=```200```
- Vastauksen koko = ```3383```kB
- Mitä domainia kysyttiin. ```http://localhost/```
- Headerista selaimen lisäämä ***user-agent***-kenttä, jossa selaimen/järjestelmän tietoja: ```"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like - Gecko) Chrome/144.0.0.0 Safari/537.36"```

### Sivut uusiksi
Tein uuden käyttäjän(hattu) ja tälle uuden publicsite kansion kotihakemistoon.
Poistin vanhan sivun pois päältä ```sudo s2dissite /etc/apache2/sites-available/nothing.conf``` ja loin uudet asetukset ```sudo vi /etc/apache2/sites-available/hattu.conf```.
Tein www-data käyttäjälle uudelleen oikeudet hattu käyttäjän kotihakemiston kansioon publicsite. Tämän jälkeen otin asetukset käyttöön ```sudo s2ensite /etc/apache2/sites-available/hattu.conf```

<img width="954" height="1226" alt="curlit" src="https://github.com/user-attachments/assets/5cefcc4f-a18d-486b-a731-6f2cd0344ffd" />

```curl -I http://localhost``` vastauksen headeri:
- Protokollan versio ja vastauksen status-koodi, joka on onnistunut: ```HTTP/1.1 200 OK```.
- Päivämäärä, kellon aika ja vyöhyke. ```Date: Thu, 29 Jan 2026 16:03:25 GMT```
- Palvelimen user-agen: ```Server: Apache/2.4.66 (Debian)```
- Sivun päivämäärä koska viimeksi muokattu: ```Last-Modified: Thu, 29 Jan 2026 14:50:46 GMT```
- Resurssin tunnistava tagi, joka auttaa pitämään tiedon eheänä muokkausten aikana.
  > https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/ETag
  
  ```ETag: "1b-64987fb552433"```
- Palvelimen hyväksymä siirtoraja. Pelkästään byte, tai none. Pääasiassa voiko latausta jatkaa keskeytyksen sattuessa.
	> https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Accept-Ranges
 
```Accept-Ranges: bytes```
 - Vastauksen sisällön koko tavuina. ```Content-Length: 27```
 - Vastauksen sisällön tyyppi: ```Content-Type: text/html```

### Monta web-serveriä koneella.
Useammalle käyttäjälle kotihakemistoon omat www-sivut. Luodaan käyttäen apachen virtualhost:sta domain nimen perusteella eriteltyjä servereitä, jotka jakavat saman serveri prosessin keskenään.

Ensiksi lisäsin testiksi seuraavat domainit /etc/hosts tiedoston loppuun.
```
127.0.0.1 hattu.example.com
127.0.0.1 sirnothing.example.com
```
```sudo vi /etc/hosts```

Sen jälkeen poistin edelliset apachen asetukset ```sudo a2dissite /etc/apache2/sites-available/hattu.conf```
Lisäsin uuden multiUser.conf tiedoston samaan kansioon alla olevalla sisällöllä ja otin asetukset käyttöön ```sudo a2ensite /etc/apache2/sites-available/multiUser.conf```
> sirnothing@housename:/etc/apache2/sites-available$ sudo cat multiUser.conf

```
<VirtualHost *:80>
    ServerName default
    DocumentRoot /var/www/empty
    <Directory /var/www/empty>
        Require all denied
    </Directory>
</VirtualHost>
<VirtualHost *:80>
	ServerName hattu.example.com
	ServerAlias www.hattu.example.com

	DocumentRoot "/home/hattu/publicsite/"
	<Directory "/home/hattu/publicsite/">
		require all granted
	</Directory>
</VirtualHost>
<VirtualHost *:80>
	ServerName sirnothing.example.com
	ServerAlias www.sirnothing.example.com

	DocumentRoot "/home/sirnothing/publicsite/"
	<Directory "/home/sirnothing/publicsite/">
		require all granted
	</Directory>
</VirtualHost>

```
Asetuksissa oli tärkeintä antaa jokaiselle VirtualHost:lle ip-osoite ja portti, joita kuuntelevat. Tässä *:80 kuuntelee kaikkia osoitteita portissa 80. 
Toinen tärkeä on ServerName ja ServerAlias, jonka avulla apache pystyy käyttämään domain nimistä erottelua kenen "palveluun" kysely ohjataan.
> Näin voidaan käyttää samaa ip-osoitetta ja porttia monen palvelun välillä.
Ilman ip-osoitteita palvelin joutuu tekemään reverse dns lookup:n saadakseen ip-osoitteen selville, ja samoin ilman domainia, niin palvelin joutuu tekemään dns-kyselin ip:lle.
Serveri ei käynnisty, jos dns-palvelu ei ole toiminnassa sillä hetkellä.
Hyvä vielä tehdä default VH tiedoston alkuun, joka ohjaa muut yhteydet tyhjään resurssiin, jos mikään VH ei vastaa kyselyyn.
> ***Tämä pitää olla ensimmäisenä, koska ensimmäinen valitaan, jos mikään ei vastaa. Estää pääsyn main-serverille.***



<img width="1491" height="1194" alt="multiuserApache" src="https://github.com/user-attachments/assets/d063b453-9452-44c3-9723-31d6dae6be21" />










