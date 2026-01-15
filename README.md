# Linux asennus

Distrona toimi Debian versio 13.3.0.(Xfce ikkunajärjestelmä)
https://www.debian.org/CD/live/

Asennus tapahtui live imagen kautta virtuaalikoneelle.
Päivitettiin virtualbox ohjelma versioon 7.2.4.
https://www.virtualbox.org/wiki/Downloads

## Alustana toimi windows ympäristö

OS Name	Microsoft Windows 10 Pro
Version	10.0.19045 Build 19045
System Type	x64-based PC
Processor	AMD Ryzen 7 2700X Eight-Core Processor, 3700 Mhz, 8 Core(s), 16 Logical Processor(s)
BaseBoard Manufacturer	ASUSTeK COMPUTER INC.
BaseBoard Product	PRIME X470-PRO

## Asennus

Aloitetaan avaamalla virtualbox ja aloitussivulta valitaan "Create a new virtual machine(VM)

<img width="744" height="412" alt="VBHome" src="https://github.com/user-attachments/assets/cce59b89-4bc1-427c-a631-90eca23a2d5e" />

Avautuvasta ikkunasta määriteltiin ensin uudelle virtuaalikoneelle nimi(VM NAME), joka on käyttäjän määriteltävissä.
Virtuaalikoneen tiedostojen polku(VM Folder) tietokoneen tiedostojärjestelmästä.
ISO-tiedoston(ISO Image) polku jätettiin tyhjäksi, koska muuten joutuisi käyttämään ohjatussa asennuksessa automaattista asennusta(set up unattended guest os installation). Tiedosto lisätään jälkeenpäin.

### Seuraavaksi määriteltiin virtuaalikoneelle käytettävä rauta(Specify virtual hardware)
Minimit ja suositeltavata määrät n. 1-2 Gt muistia  
https://www.debian.org/releases/stable/amd64/ch03s04.en.html

Muistin määräksi(Base memory) laitettiin n.4 Gt, koska muistia on koneella 16 Gt.
Prosessorien määräksi laitettiin 4 kpl, koska niitä löytyy koneelta 16 kpl.
Otettiin käyttöön EFI-bios laittamalla rasi ruutuun(Use EFI).

### Sitten määriteltiin virtuaalisen kovalevyn tiedot(Specify virtual hard disk)
Luo uusi virtuaalinen kovalevy(Create a new virtual hard disk), jossa tarpeeksi tilaa. Suositeltavaa olla n. 20-30Gt.
https://www.debian.org/releases/stable/amd64/ch03s04.en.html
Kovalevyn tyyppi ja formaatti pidetään VirtualBox Disk Image:na(VDI), eikä allokoitu valmiiksi koko tilaa levylle.
Luodaan virtuaalinen tietokone

### Asetukset ja iso-tiedoston lisäys jälkeenpäin
Avattiin virtuaalisen tietokoneen asetukset. Sieltä tallennustila(storage), johon määriteltiin IDE-ohjaimelle debianin live image tiedosto ja valittiin rasti ruutuun "Live CD/DVD"
<img width="976" height="614" alt="VBStorageLive" src="https://github.com/user-attachments/assets/e366f663-1668-43d6-9eb7-39ce1379be0a" />
Verkkoasetuksista(Network) vaihettiin käytössä olevan adapterin NAT-tila bridge-tilaksi, jotta virtuaalikone saa oman ip-osoitteen.
<img width="781" height="388" alt="VBNetworkBridge" src="https://github.com/user-attachments/assets/0a2bfef3-824e-4342-8f1d-27c514b9682c" />

### Virtuaalikoneen käynnistys
Käynnistettiin virtuaalikone ja saatiin virhesanoma:
VM Name: Debian

Failed to load R0 module C:\Program Files\Oracle\VirtualBox/VMMR0.r0: SUP_IOCTL_LDR_OPEN failed (VERR_LDR_IMPORTED_SYMBOL_NOT_FOUND).
Failed to load VMMR0.r0 (VERR_LDR_IMPORTED_SYMBOL_NOT_FOUND).
Result Code:
E_FAIL (0x80004005)
Component:
ConsoleWrap
Interface:
IConsole {6ac83d89-6ee7-4e33-8ae6-b257b2e81be8}.

Virheestä päästiin eroon ajamalla uudelleen virtualbox 7.2.4 asennustiedosto ja korjaamalla asennus, jonka jälkeen käynnistettiin host-tietokone uudelleen.
https://github.com/VirtualBox/virtualbox/issues/303

### Debian:n asennus.

Virtuaalikoneen käynnistyttyä valittiin valikosta Live system (amd64), jonka jälkeen debian käynnistyi ja avasi suoraan ikkunajärjestelmän.
Työpöydältä valitaan Install debian ja kieleksi valittiin englanti, koska muilla kielillä saattaa olla bugeja.

<img width="1282" height="873" alt="debianLiveInstaller" src="https://github.com/user-attachments/assets/50a3038f-0961-44e0-8214-72ac7c069207" />

Sijainti(Location) valittiin sijainnin mukaan. Region: Europe ja Zone: Helsinki.
Näppäimistö valittiin Generic 105-key PC ja Suomen(Finnish) default versio, jotta ääkköset toimivat.
Ositukseksi(partitions) valittiin valikosta Erase disk, joka luo automaattisesti boot, debian ja swap-osiot.
Käyttäjä(Users) valikossa valittiin nimi, kirjautumis nimi, tietokoneen nimi ja vahva salasana käyttäjälle.
Tämän jälkeen valittiin asenna(install) debian koneelle.

















