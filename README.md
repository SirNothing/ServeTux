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


## Linux tiedostojärjestelmästä poimittuja

Kansiot kävin tässä järjestyksessä: /(root), /home/, /home/<user>/, /etc/, /media/, /var/log/ ja valitsin sieltä mielestä tärkeän tiedoston/kansion.




<img width="1397" height="1286" alt="firstHFS" src="https://github.com/user-attachments/assets/55867f5a-3d21-4560-a60b-3cb0be848baa" />




<img width="1383" height="1286" alt="secHFS" src="https://github.com/user-attachments/assets/ec9c92ce-fab1-4ac4-af57-88cd4364a2fd" />




<img width="924" height="1039" alt="lastHFS" src="https://github.com/user-attachments/assets/01b48e73-449a-4231-a7ce-fff32a8875a7" />


## Vähä grep:iä ja tietoja putkien kautta.


<img width="1148" height="1222" alt="ekalshwgrep" src="https://github.com/user-attachments/assets/30a1a23a-6122-455b-9e09-9a6842ce0cee" />

<img width="1025" height="399" alt="tokalshw" src="https://github.com/user-attachments/assets/b6adff6d-0738-4a6b-b841-95dae0719cef" />

Koneen listauksesta(VB) tärkeimpinä nähtiin, muistin määrä, prosessorin malli, Emolevyn/piirilevyjen mallit(natoma/triton2), jotka hoitavat PCI, ISA/IDE ym. laitteita, voluumit(dev/sda1,2,3), kovalevyn koon. inputteja. 







