# Komentokehote
> Koneena VB:n päällä pyörivä debian linux.
>> <img width="615" height="557" alt="vmtiedot" src="https://github.com/user-attachments/assets/afaf0232-86fd-4521-b581-a859d804d2b4" />

### Tekstieditorin asennus - apt-get

Asensin debian linux:lle **micro** tekstieditorin oheisen kuvan mukaan. **Sudolla** sain root-oikeudet, jotta sain asennettua ohjelmat.
> sudo apt install micro
>> <img width="1394" height="1066" alt="InstallMicro" src="https://github.com/user-attachments/assets/0c404989-4057-4781-a156-68c0a6705d2d" />

### Muita asennuksia

Seuraavaksi etsin kolme minulle uutta ohjelmaa, joita ajetaan komentokehotteessa.
Etsin kolme uutta ohjelmaa ja yhden parannellun version hyödyllisempää ohjelmaa.

> 1. **asciinema:n** - Komentokehotteen tallennus "videona".
>> https://asciinema.org/

> 3. **websocketd** serveri ohjelman, jolla voi tehdä nopean serverin testaukseen ym.
> Valitsin suoraan paketista ajettavan version, jotta ei tarvinnut asentaa koneelle mitään.
>> https://github.com/joewalnes/websocketd

> 4. **socat** - Yhteyksien luomiseen, hienompi työkalu kuin netcat.
>> https://linux.die.net/man/1/socat


Muut ohjelmat hain paketinhallinnan kautta, mutta websocketd:tä ei löytynyt, joten latasin sen koneelle komentokehotteesta **wcurl**-komennolla osoitteesta: https://github.com/joewalnes/websocketd/releases/download/v0.4.1/websocketd-0.4.1-linux_amd64.zip koti kansiooni.
> sudo apt install asciinema socat


websocketd:n latasin ensin kotikansiooni ja purin luomaani websocketD-kansioon, josta käsin pääsin ajamaan sitä. Koska en vielä ainakaan päivittänyt sille path-tietoja, jotta muualtakin käsin pääsisi ajamaan ohjelmaa.

> websocket asciinema:n kautta.

![doit cart](https://github.com/user-attachments/assets/857926e4-771e-408c-b211-9b2610edb1c0)

> socat asciinema:n kautta. Yhdistetään telnet-serverille.

![sw](https://github.com/user-attachments/assets/2a767c11-74c1-4669-93f5-ca1818e0e901)
