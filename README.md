# Let's encrypt - https

### Alusta
Linux, debian 13 VirtualBox:lla.


### Asennus
Python3, jos ei ole jo.
> https://wiki.python.org/moin/BeginnersGuide(2f)Download.html
```sudo apt-get update; sudo apt-get install python3 python3-dev -y```
certbotin asennus, jonka kautta certifikaatit saadaan.
Sekä python3-certbot-apache, joka huolehtii sertifikaattien hallinnasta ja uusimisesta apachelle.
> https://packages.debian.org/sid/python3-certbot-apache
> https://certbot.eff.org/pages/about
```sudo apt-get install certbot python3-certbot-apache -y```

### Certbot käyttö.
```sudo certbot --apache --domains example.com,www.example.com```
Itse tein ilman --domains-optiota ja täytin ohjelman kysyessä domainin ja sähköpostin.

