# Let's encrypt - https

### Alusta
Linux, debian 13 VirtualBox:lla.


### Asennus
Python3, jos ei ole jo.

> https://wiki.python.org/moin/BeginnersGuide(2f)Download.html

```sudo apt-get update; sudo apt-get install python3 python3-dev -y```

certbotin asennus, jonka kautta certifikaatit saadaan.
Sekä python3-certbot-apache lisäosa, joka huolehtii sertifikaattien hallinnasta ja uusimisesta apachelle.

> https://packages.debian.org/sid/python3-certbot-apache

> https://certbot.eff.org/pages/about

```sudo apt-get install certbot python3-certbot-apache -y```

### Certbot käyttö.
```sudo certbot --apache --domains example.com,www.example.com```

Itse tein ilman --domains-optiota ja täytin ohjelman kysyessä domainin ja sähköpostin.
Tämän jälkeen Let's encrypt:ltä sai varmenteen domainille sirnothing.online. Varmenne tulee vain laitetuille domaineille, eikä esimerkiksi alidomaineille.
Kaikille saadaan wildcard(*):n kautta, tai erikseen dns:än kautta haetut varmenteet. python lisäosa(pip) certbot-dns-<provider>.

<img width="671" height="357" alt="certbotReneweDryrun" src="https://github.com/user-attachments/assets/7b3c5d4a-fae0-4c3c-a76b-84aa6bbfe7f1" />

> https://sirnothing.online
>> [Varmenne sirnothing.online.pdf](https://github.com/user-attachments/files/25762064/Varmenne.sirnothing.online.pdf)


> https://www.ssllabs.com/ssltest/index.html
>> [SSL Server Test_ sirnothing.online (Powered by Qualys SSL Labs).pdf](https://github.com/user-attachments/files/25762850/SSL.Server.Test_.sirnothing.online.Powered.by.Qualys.SSL.Labs.pdf)

