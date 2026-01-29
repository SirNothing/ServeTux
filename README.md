# Apache HTTP-server

Asensin apachen version 2.4 web-serverin linux:lle, joka pyörii virutaalikoneella VirtualBox:n päällä.

### Alusta

>> ***linux***:
>> '''bash
>> sudo cat /etc/os-release;
>> '''
> PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
> NAME="Debian GNU/Linux"
> VERSION_ID="13"
> VERSION="13 (trixie)"
> VERSION_CODENAME=trixie
> DEBIAN_VERSION_FULL=13.3

### a) Asennus
Asensin ensiksi itse apachen käyttäen debian:n paketinhallintaa ***apt-get**
https://httpd.apache.org/docs/2.4/install.html
'''bash
sudo apt-get update;
sudo apt-get install apache2 -y;
'''

***Apache serveri***:
>> '''bash
>> sudo apache2 -v;
>> '''
>> Server version: Apache/2.4.66 (Debian)
>> Server built:   2025-12-05T18:52:34


### a) Serverin testaaminen
 #### main-server
Serveri suoraan palveli apachen main serverin sivua osoitteessa http://localhost:80/ ja tietokoneen kansiosta /var/www/, johon tarvitaan root-oikeudet.

#### oma serveri
Loin apache asetukset ja oikeudet niin, että virtuaalinen host käyttää kotihakemiston kansiota juurenaan.
Pystytään tekemään ja muokkaamaan omilla oikeuksilla tiedostoja.

'''bash
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
'''
Sisältö:
sirnothing@housename:/etc/apache2/sites-available$ cat nothing.conf 
<VirtualHost *:80>
	ServerName nothing.example.com
	ServerAlias www.nothing.example.com

	DocumentRoot "/home/sirnothing/publicsite/"
	<Directory "/home/sirnothing/publicsite/">
		require all granted
	</Directory>
</VirtualHost>

Toin uudet asetukset apachelle, loin index.html:n kotihakemiston publicsite-kansioon ja käynnistin palvelimen uudelleen.
'''bash
sudo a2ensite /etc/apache2/sites-available/nothing.conf;
echo hello local >> ~/publicsite/index.html;
sudo systemctl restart apache2;
'''
http://localhost/ näytti index.html:n omasta kansiosta.
<img width="391" height="90" alt="localhost_own" src="https://github.com/user-attachments/assets/5b52d230-e64b-4503-9790-e8de0feaf5d0" />

### Lokien selaaminen

Sivulla onnistuneesti käytyjen yritysten lokitiedoston selaaminen, jos ei ole erikseen luoto vhost:lle omia lokitiedostoja.
'''bash
sudo tail -f /var/log/apache2/other_vhosts_access.log;
'''
<img width="1336" height="187" alt="tailLog" src="https://github.com/user-attachments/assets/c5260bac-781f-47b2-bb91-8fb7a6eb28a3" />

Ensimmäinen sarake näyttää mistä pyyntö on tullut, joka on ipv6 loopback(localhost) osoite ::1.
Sen jälkeen näytetään päivämäärä, kellonaika ja aikavyöhyke. [27/Jan/2026:14:20:59 +0200]
Itse http-kyselyn sisältö jossa pyydetään index-tiedostoa juuresta käyttäen http:n versiota 1.1. "GET / HTTP/1.1"
Serverin vastauskoodi, OK=200
Vastauksen koko = 3383kB
Mitä domainia kysyttiin. http://localhost/
Headerista selaimen lisäämä user-agent kenttä, jossa selaimen/järjestelmän tietoja: "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36".















