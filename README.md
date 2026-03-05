# Scripting with bash

> https://terokarvinen.com/linux-palvelimet/
> 5.3.2026

## Alusta
Debian linux 13, VirtualBox.

### Hello world kielillä
Kääntäminen: gcc *.c -o <outfile>: g++ *.cpp -o <outfile> 
<img width="1019" height="953" alt="scripts" src="https://github.com/user-attachments/assets/fa1a9668-8b92-460f-9fae-111043ceec26" />

### Komennon lisääminen kaikille

Kirjoitetaan scripti valmiiksi. Annetaan oikeudet oikein ja kopioidaan $PATH-polkuun.

*Script* - 
```
#!/usr/bin/bash
date
if [ "$#" -eq 0 ]
then
	echo "Ei argumentteja annettu"
	whoami
	exit 1
fi
echo "Hello" $1
```

*Oikeudet* - user:all, group: +rx, other: +rx.
```chmod ugo+x tere; chmod g-w tere```

*Siirto* 
```sudo cp ./tere /usr/local/bin/```

<img width="825" height="314" alt="CommAdded" src="https://github.com/user-attachments/assets/86ff3d5c-63d3-47fb-bb2a-419cce76072e" />

