# Domaini serverille

> https://terokarvinen.com/linux-palvelimet/

> 27.3.2026

Domainin valinnan aloitin palveluntarjoajasta, johon valitsin amerikkalaisen namecheap.com:n, koska otin vain testi domainin halvalla. Muuten varmaan valitsisin Eurooppalaisen palveluntarjoajan.
> https://www.namecheap.com/
<img width="1333" height="1259" alt="namecheap com" src="https://github.com/user-attachments/assets/98f3e50c-6194-41c5-95f2-2c0eda3bd9a9" />


Rekisteröidyin ja valitsin itselleni halvan online. LTD:n ja sille oman sirnothing. domain nimen(sirnothing.online).
Tein domainille A-tietueen osoittamaan omalle palvelimelle ja sille CNAME:n www.sirnothing.online.
Lisäksi tein *(wildcard) CNAME-tietueen sirnothing.online domainille, joka vastaa kaikkia ei olemassa olevia alidomaineja.

<img width="1111" height="257" alt="updatedDNSSettings" src="https://github.com/user-attachments/assets/f38c786e-fab9-47ae-88a1-c13fe5506057" />

## Domainin tarkistus host ja dig-komennoilla linuxilla.

```dig <domain> +trace```
Kaikkien nimipalvelimien reitti authorative-palvelimelle asti. root(.) -> LTD (online.) -> Authorative(sirnothing.)
Mahdollista myös käydä serveri kerrallaan komennoilla ```dig <domain> +norecuse @root.server/@LTD/@auth``` 
root/ltd antaa referralin authorativelle, jolta vastauksen saa.

<img width="1755" height="942" alt="digTraceNothing" src="https://github.com/user-attachments/assets/c2f0a422-0127-4142-ad85-9e27171058d4" />

```dig sirothing.online A/AAAA/TXT/MX/SOA```
Oman domainin tietueet, joista AAAA-tietuetta ei ole, koska vastausta ei löydy. Pelkästään AUTHORITY osio löytyy.
Muuten näyttää A-tietueen serverin ip:llä ja sille kuuluvalla elossa olo ajalla(TTL), joka määrittää kauan muut palvelimet pitävät tietoa välimuistissa(cache), ennen kuin päivittävät tiedon. Vastauksen pienenevä TTL viittaa, että tieto tulee "välipalvelimelta", recursion server/forwading server/..., kun googlen nimipalvelimen välimuistista(cache). Eikä suoraan Authorative palvelimelta. ```dig domain @authServer```.

TXT-tietue sisältää vain Sender Policy Framework(SPF):n, joka on tässä domain-tarjoajan lisäämä. Kertoo kuka saa lähettää sähköpostia tästä domainista, johon in lisätty palveluntarjoajan serveri ja muut hylätään. Liittyy tunnistukseen käyttäen lisäksi 

MX-tietue listaa sähköpostipalvelimet, joista kolme jakaa liikenteen ja kaksi muuta ovat takavarana. Priority(10,15,20)

SOA-tietue kertoo domainin päänimipalvelun "aloituksen pään", Start of authority(SOA), joka näyttää palveluntarjoajan nimipalvelimiin ja hallitsijan sähköpostin(lisää @ ensimmäisen pisteen sijalle).
Numerosarjan ensimmäinen osa serials-numero(pvmRev), joka pitää olla sama kaikilla nimipalvelimilla, jotka sisältävät domainin tiedot, muuten jollain palvelimella on vanhaa tietoa. Päivitysaika(master/second-palvelimet), uusintayritysaika(mast/sec), vanhentumisaika(master ei vastaa, zone pyyhitään), minimi elossa olo aika(TTL).

<img width="1080" height="1194" alt="digNothing1" src="https://github.com/user-attachments/assets/424a5a0f-34e5-432f-87d7-86ee113a670e" />
<img width="1095" height="872" alt="digNothing2" src="https://github.com/user-attachments/assets/b55e93ea-c6ae-4e44-a505-bee99ffc44e3" />


