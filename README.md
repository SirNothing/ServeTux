# Linux asennus

Distrona toimi Debian versio 13.3.0.(Xfce ikkunajärjestelmä)
https://www.debian.org/CD/live/

Asennus tapahtui live imagen kautta virtuaalikoneelle.
Päivitin ensiksi virtualbox-ohjelma versioon 7.2.4.
https://www.virtualbox.org/wiki/Downloads

> ## Alustana toimi windows ympäristö

>> OS Name	Microsoft Windows 10 Pro
>> Version	10.0.19045 Build 19045
>> System Type	x64-based PC
>> Processor	AMD Ryzen 7 2700X Eight-Core Processor, 3700 Mhz, 8 Core(s), 16 Logical Processor(s)
>> BaseBoard Manufacturer	ASUSTeK COMPUTER INC.
>> BaseBoard Product	PRIME X470-PRO

## Asennus

Aloitin avaamalla virtualbox-ohjelman ja aloitussivulta valitsin "Create a new virtual machine(VM)".

<img width="744" height="412" alt="VBHome" src="https://github.com/user-attachments/assets/cce59b89-4bc1-427c-a631-90eca23a2d5e" />

Avautuvasta ikkunasta määrittelin ensin uudelle virtuaalikoneelle: 
- Nimen(VM NAME), joka on käyttäjän määriteltävissä.
- Virtuaalikoneen tiedostojen polun(VM Folder) host-tietokoneen tiedostojärjestelmästä.
- ISO-tiedoston(ISO Image) polun jätin tyhjäksi, koska muuten joutuisin käyttämään ohjatussa(wizard) asennuksessa automaattista asennusta(set up unattended guest os installation). Tiedosto lisättiin jälkeenpäin.

### Virtuaalikoneelle käytettävä rauta(Specify virtual hardware)
> Minimit ja suositeltavata määrät olivat n. 1-2 Gt muistia  
>> https://www.debian.org/releases/stable/amd64/ch03s04.en.html

- Muistin määräksi(Base memory) laitoin n.4 Gt, koska muistia oli tarpeeksi.
- Prosessorien määräksi laitoin 4 kpl, koska niitä löytyy tarpeeksi.
- Otin käyttöön EFI-bios:n laittamalla rastin ruutuun(Use EFI).

### Virtuaalisen kovalevyn tiedot(Specify virtual hard disk)
- Loin uuden virtuaalisen kovalevyn(Create a new virtual hard disk), jossa on tarpeeksi tilaa. 
> Suositeltavaa olla n. 20-30Gt.
>> https://www.debian.org/releases/stable/amd64/ch03s04.en.html
- Kovalevyn tyyppin ja formaattin pidin VirtualBox Disk Image:na(VDI), enkä allokoinut valmiiksi koko tilaa levyltä.
- Sitten loin virtuaalisen tietokoneen.

### Asetukset ja iso-tiedoston lisäys jälkeenpäin
** Avasin virtuaalisen tietokoneen asetukset. Sieltä tallennustila(storage), johon määrittelin IDE-ohjaimelle "debianin live image"-tiedoston, ja valitsin rastin ruutuun "Live CD/DVD" **
<img width="976" height="614" alt="VBStorageLive" src="https://github.com/user-attachments/assets/e366f663-1668-43d6-9eb7-39ce1379be0a" />
Verkkoasetuksista(Network) vaihdoin käytössä olevan adapterin NAT-tilasta bridge-tilaksi, jotta virtuaalikone saa oman ip-osoitteen.
<img width="781" height="388" alt="VBNetworkBridge" src="https://github.com/user-attachments/assets/0a2bfef3-824e-4342-8f1d-27c514b9682c" />

### Virtuaalikoneen käynnistys
Käynnistin virtuaalikoneen ja sain virhesanoman:
> VM Name: Debian

> Failed to load R0 module C:\Program Files\Oracle\VirtualBox/VMMR0.r0: SUP_IOCTL_LDR_OPEN failed (VERR_LDR_IMPORTED_SYMBOL_NOT_FOUND).
> Failed to load VMMR0.r0 (VERR_LDR_IMPORTED_SYMBOL_NOT_FOUND).
> Result Code:
> E_FAIL (0x80004005)
> Component:
> ConsoleWrap
> Interface:
> IConsole {6ac83d89-6ee7-4e33-8ae6-b257b2e81be8}.

>> Virheestä pääsin eroon ajamalla uudelleen virtualbox 7.2.4 asennustiedoston, ja korjaamalla asennuksen, jonka jälkeen käynnistin host-tietokoneen uudelleen.
>> https://github.com/VirtualBox/virtualbox/issues/303

### Debian:n asennus.

Virtuaalikoneen käynnistyttyä valitsin valikosta Live system (amd64), jonka jälkeen debian käynnistyi ja avasi suoraan live-tilan ikkunajärjestelmän.
Työpöydältä valitsin Install debian, ja kieleksi valitsin englanti. Muilla kielillä saattaa tulla bugeja, joten niiden asennus jälkeen päin toiseksi kieleksi.

<img width="1282" height="873" alt="debianLiveInstaller" src="https://github.com/user-attachments/assets/50a3038f-0961-44e0-8214-72ac7c069207" />

- Sijainnin(Location) valitsin oman sijainnin mukaan, kuten Region:n ja Zone:n.
- Näppäimistöksi valitsin "Generic 105-key PC" ja Suomen(Finnish) default version, jotta ääkköset toimivat.
- Ositukseksi(partitions) valitsin valikosta "Erase disk", joka luo automaattisesti boot, debian ja swap osiot.
- Käyttäjä(Users) valikossa valitsin nimen, kirjautumis nimen, tietokoneen nimen ja vahva salasana käyttäjälle.
Tämän jälkeen valitsin asenna(install) debian koneelle ja siellähän sen käynnistyksen jälkeen on.

















