Lähdin seuraavien harjoitusten tekemiseen Tero Karvisen 17.11.2022 pidetyn Palvelinten hallinta -oppitunnin pohjalta. Apuna muistiinpanot ja aiemmin tunnilla tehdyt tehtävät. Harjoituksissa käytin VirtualBoxin kautta Ubuntu Desktoppia(22.04.1 LTS) ja Ubuntu Serveriä(22.04.1 LTS). Tehtävissä Ubuntu Server toimi Masterina ja Ubuntu Desktop Orjana, joka on aikaisemmin jo konfiguroitu.

### a) Hei komento! Tee järjestelmään uusi "hei maailma" -komento ja asenna se orjille Saltilla. Liitä raporttiisi orjan 'ls -l /usr/local/bin/' tulosteesta ainakin se rivi, jolla näkyy uuden komentotiedostosi oikeudet. 

Lähdin tekemään a) tehtävää sillä ajatuksella, että luon ensin komennon, joka tulostaa tekstin "hei maailma" ja testaan, että se toimii ja sen jälkeen niin, että komennon voi syöttää mistä tahansa polusta. Lopuksi kokeilen asentaa komennon kaikille orjille Saltin avulla.

Aloitin luomalla kotihakemistooni kansion, jonne tulevaisuudessa laitan kaikki tekemäni komennot. Lisäsin kansioon uuden hei.sh tiedoston ja sinne sisällön.
```bash
miika@UbuntuServer:~$ mkdir komennot
miika@UbuntuServer:~$ cd komennot
miika@UbuntuServer:~/komennot$ nano hei.sh
#!/usr/bin/bash
echo "Hei maailma"
```
Nopea testi toimiiko komento:
```bash
miika@UbuntuServer:~/komennot$ ./hei.sh
hei.sh: command not found
```
Command not found.
Tiedoston luomisen jälkeen oli tiedostosta tehtävä ajettava tiedosto. Ajettavaa tiedostoa kuvaa kirjain "x". Komennolla `ls -l hei.sh` näin, että mikään ryhmä ei pysty ajamaan tiedostoa. 

```bash
miika@UbuntuServer:~/komennot$ ls -l hei.sh
-rw-rw-r-- 1 miika miika 35 Nov 19 15:17 hei.sh
```
Tein tiedostosta ajettavan jokaiselle ryhmälle komennolla: `chmod ugo+x hei.sh` (ugo = user/group/others).
Tarkistin perään vielä, että muutos tuli voimaan taas `ls -l hei.sh` komennolla:
```bash
miika@UbuntuServer:~/komennot$ chmod ugo+x hei.sh
miika@UbuntuServer:~/komennot$ ls -l hei.sh
-rwxrwxr-x 1 miika miika 35 Nov 19 15:17 hei.sh
```
Nyt tiedosto oli kaikinpuolin kunnossa, joten testasin ajaa sen uudestaan komennolla: `./hei.sh`
```bash
miika@UbuntuServer:~/komennot$ ./hei.sh
Hei maailma
```

Tiedoston ajaminen "komennot" -kansion ulkopuolella:

```bash
miika@UbuntuServer:~$ ./hei.sh
-bash: ./hei.sh: No such file or directory
miika@UbuntuServer:~$ ./komennot/hei.sh
Hei maailma
miika@UbuntuServer:~$ 
```


Jos tiedostoa halutaan pystyä ajamaan mistä vain kansiosta tai polusta ilman, että joka kerta syötetään sen suhteellinen polku, on se lisättävä käyttäjän `/usr/local/bin` kansioon. Kopioin tekemäni hei.sh -tiedoston kyseiseen polkuun komennolla: `sudo cp hei.sh /usr/local/bin`. Tähän komentoon piti käyttää `sudo` sillä se vaikuttaa kaikkiin käyttäjiin. Tarkistin vielä, että tiedosto on paikallaan menemällä kansioon ja syöttämällä `ls -l`

```bash
miika@UbuntuServer:~/komennot$ sudo cp hei.sh /usr/local/bin
miika@UbuntuServer:~/komennot$ cd /usr/local/bin
miika@UbuntuServer:/usr/local/bin$ ls -l
-rwxr-xr-x 1 root root 35 Nov 19 16:01 hei.sh
```
Nyt komentoa pystyi ajamaan mistä vain yksinkertaisesti syöttämällä komentoriville tiedoston nimen. Kokeilin komentoa kotihakemistossani, sekä /tmp kansion sisällä:
```bash
miika@UbuntuServer:~$ hei.sh
Hei maailma
miika@UbuntuServer:~$ cd /tmp
miika@UbuntuServer:/tmp$ hei.sh
Hei maailma
``` 
Nyt kun komento toimi käsinasennettuna mistä vain kansiosta UbuntuServerillä, oli aika automatisoida asennus Saltilla ja testata toimiiko komento Orjalla. (Ubuntu Desktop on orja tässä tapauksessa)

Kopioin suoraan aikasemmin tehdyn "komennot" -kansion ja sen sisällön (hei.sh komento) kotihakemistostani `/srv/salt` polkuun komennolla: <br> `sudo cp -r komennot/ /srv/salt/`

```bash
miika@UbuntuServer:~$ sudo cp -r komennot/ /srv/salt/
miika@UbuntuServer:~$ cd /srv/salt
miika@UbuntuServer:/srv/salt$ ls
apache  komennot  ssh  vieras
miika@UbuntuServer:/srv/salt$ cd komennot/
miika@UbuntuServer:/srv/salt/komennot$ ls
hei.sh
miika@UbuntuServer:/srv/salt/komennot$ 
```
Kaikki oli tallella. Sitten tein init.sls tiedoston komennot -kansioon, joka luo sille osoitetun tiedoston Salt masterin kansiosta ja tekee tiedostosta ajettavan; orjan /usr/local/bin kansioon.

```bash
miika@UbuntuServer:/srv/salt/komennot$ sudoedit init.sls

NANO:
/usr/local/bin/hei.sh:
  file.managed:
    - source: salt://komennot/hei.sh
    - mode: '0755'
```
Komennolla `stat hei.sh` pystyi näkemään, millä oikeuksilla tiedosto toimi, kun sitä kokeilin aikaisemmassa vaiheessa, joten mode kohtaan tulee sama numero:
```bash
miika@UbuntuServer:/srv/salt/komennot$ stat hei.sh 
  File: hei.sh
  Size: 35        	Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d	Inode: 524456      Links: 1
Access: '(0755/-rwxr-xr-x)'  Uid: (    0/    root)   Gid: (    0/    root)
```
Kaikki oli nyt valmista testaamista varten. Aloitin tyhjentämällä `/usr/local/bin` kansion.
```bash
miika@UbuntuServer:~$ cd /usr/local/bin
miika@UbuntuServer:/usr/local/bin$ sudo rm hei.sh 
miika@UbuntuServer:/usr/local/bin$ ls
miika@UbuntuServer:/usr/local/bin$ 
```
Kun kansio oli tyhjä, menin kotihakemistooni ja kokeilin ensin ajaa `hei.sh` komennon:
```bash
miika@UbuntuServer:~$ hei.sh
hei.sh: command not found
miika@UbuntuServer:~$ 
```
Ei toiminut (ei tietenkään koska juuri poistin kyseisen komennon). Kokeilin ajaa äsken tekemäni "komennot" -tilan paikallisesti komennolla: `sudo salt-call --local state.apply komennot`:

![2 (2)](https://user-images.githubusercontent.com/112076418/202862349-58a00773-2bc2-46fd-8ae9-33cf1324c275.png)

Homma näytti skulaavan, joten kokeilin ajaa tilan muillekkin orjille. Ensin kokeilin komentoa `hei.sh` orjakoneellani (Ubuntu Desktop).

![3](https://user-images.githubusercontent.com/112076418/202862462-fa2709ad-4b3a-4190-8ce6-cc122a26e635.png)

Ei toiminut (yllätys). Syötin Masterilla (UbuntuServer) seuraavan komennon aktivoidakseni "komennot" -tilan kaikille orjille: `sudo salt '*' state.apply komennot`

```bash
miika@UbuntuServer:~$ sudo salt '*' state.apply komennot
UbuntuDesktop:
----------
          ID: /usr/local/bin/hei.sh
    Function: file.managed
      Result: True
     Comment: File /usr/local/bin/hei.sh updated
     Started: 18:57:56.515967
    Duration: 54.003 ms
     Changes:   
              ----------
              diff:
                  New file
              mode:
                  0755

Summary for UbuntuDesktop
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1
Total run time:  54.003 ms
miika@UbuntuServer:~$ 
```
Ei virheilmoituksia, joten testasin komennon `hei.sh` uudestaan orjalla (UbuntuDesktop) ja katsoin vielä, että oikeudet ovat samat, jotka laitoin init.sls tiedostoon. ('0755')

![4](https://user-images.githubusercontent.com/112076418/202862697-f986a95f-fecf-4892-bffd-6f99ae12ea49.png)

![5](https://user-images.githubusercontent.com/112076418/202863053-3983aaea-db9b-4a6c-8944-5cfd70c836db.png)

-rwxr-xr-x  =  0755. Kaikki ok!


### b) whatsup.sh. Tee järjestelmään uusi komento, joka kertoo ajankohtaisia tietoja; asenna se orjille. Vinkkejä: Voit näyttää valintasi mukaan esimerkiksi päivämäärää, säätä, tietoja koneesta, verkon tilanteesta...

Tein tehtävän samalla periaatteella kuin edellisen a) tehtävän. Eli ensiksi tein uuden komento -tiedoston nimeltä `whatsup.sh` "komennot" -kansioon ja lisäsin sinne komennon, joka näyttää tämän hetkisen päivämäärän ja kellonajan. 

```bash
miika@UbuntuServer:~$ cd komennot/
miika@UbuntuServer:~/komennot$ nano whatsup.sh
#!/bin/bash
echo "Moi, tämän hetkinen päivämäärä on:"
date '+%F'
echo "kello tällä hetkellä:"
date '+%R'
```

Annoin edellisen tehtävän mukaan taas tiedostolle ajo-oikeudet komennolla: `chmod ugo+x whatsup.sh`. Tämän jälkeen kokeilin ajaa tiedoston komennolla: `./whatsup.sh`

![6](https://user-images.githubusercontent.com/112076418/202866760-30645112-8bd6-4299-8417-769b753387f6.png)

Nyt kun tiesin, että komento toimii, kokeilin suoraan asentaa komennon /usr/local/bin kansioon saltilla samanlailla kuin a) kohdassa.
Kopioin whatsup.sh komennon /srv/salt/komennot/ kansioon.

```bash
miika@UbuntuServer:~/komennot$ sudo cp whatsup.sh /srv/salt/komennot/
miika@UbuntuServer:~/komennot$ cd /srv/salt/komennot/
miika@UbuntuServer:/srv/salt/komennot$ ls -l
total 12
-rwxr-xr-x 1 root root  35 Nov 19 18:16 hei.sh
-rw-r--r-- 1 root root  95 Nov 19 18:29 init.sls
-rwxr-xr-x 1 root root 117 Nov 19 20:57 whatsup.sh
```
Lisäsin init.sls tiedostoon aikaisemman komennon lisäksi whatsup.sh komennon oikeuksineen samanlailla kuin a) kohdassa. Tiedosto näytti nyt seuraavalta:
```bash
miika@UbuntuServer:/srv/salt/komennot$ sudoedit init.sls
miika@UbuntuServer:/srv/salt/komennot$ cat init.sls 
/usr/local/bin/hei.sh:
  file.managed:
    - source: salt://komennot/hei.sh
    - mode: '0755'

/usr/local/bin/whatsup.sh:
  file.managed:
    - source: salt://komennot/whatsup.sh
    - mode: '0755'
```
Nyt oletuksena oli, että kun "komennot" -tila ajetaan, syntyy käyttäjän /usr/local/bin kansioon komento `hei.sh` ja `whatsup.sh`. Tein testin ennen jälkeen komennon:

![7](https://user-images.githubusercontent.com/112076418/202873153-3ce1ce95-799f-4ac0-b315-9166aac0e454.png)

Kaikki ok, joten siirryin taas testaamaan toisella orjakoneella. Kokeilin syöttää orjakoneella (Ubuntu Desktop) komennon `whatsup.sh` ilman tulosta.

![8](https://user-images.githubusercontent.com/112076418/202874187-51e46efb-91b3-40bd-966e-7fb195533a1f.png)

 Sitten syötin komennon: `sudo salt '*' state.apply komennot` masterilla (Ubuntu Server) ja kokeilin uudestaan:

```bash
miika@UbuntuServer:/srv/salt/komennot$ sudo salt '*' state.apply komennot
UbuntuDesktop:
----------
          ID: /usr/local/bin/hei.sh
    Function: file.managed
      Result: True
     Comment: File /usr/local/bin/hei.sh is in the correct state
     Started: 00:27:33.991882
    Duration: 52.957 ms
     Changes:   
----------
          ID: /usr/local/bin/whatsup.sh
    Function: file.managed
      Result: True
     Comment: File /usr/local/bin/whatsup.sh updated
     Started: 00:27:34.045028
    Duration: 20.792 ms
     Changes:   
              ----------
              diff:
                  New file
              mode:
                  0755
Summary for UbuntuDesktop
------------
Succeeded: 2 (changed=1)
Failed:    0
------------
Total states run:     2
Total run time:  73.749 ms
miika@UbuntuServer:/srv/salt/komennot$ 
```

![11](https://user-images.githubusercontent.com/112076418/202874784-6d744b88-746b-4bec-89c3-b14029f750a6.png)

Homma toimi jälleen halutusti, jes!


### c) hello.py. Tee järjestelmään uusi komento Pythonilla ja asenna se orjille. Vinkkejä: Hei maailma riittää, mutta propellihatut saavat toki koodaillakin. Shebang on "#!/usr/bin/python3". Helpoin Python-komento on: print("Hei Tero!")

Komentojen tekeminen alkaa jo sujua rutiininomaisen hyvin, joten aloitin tehtävän tekemisen luomalla uuden komentotiedoston suoraan /srv/salt/komennot -kansioon. Pythonkin oli aiemmilta kursseilta tuttu, joten päätin tehdä pientä koodia tiedostoon. Tiedoston ajaminen antaa ensin teksin "Hei maailma" ja sen jälkeen pyytää käyttäjää syöttämään haluamansa joulukuusen koon. Joulukuusen koko on yhtä monta riviä korkea kuin käyttäjä syöttää.


```bash
miika@UbuntuServer:/srv/salt/komennot$ sudoedit hello.py

NANO:
#!/usr/bin/python3
print("Hei maailma")

koko = int(input("Syötä joulukuusen koko: "))
merkki = "*"
i = koko-1
print("Tässä on sinulle joulukuusi\n")
while koko > 0:  
    print(" " * (koko-1) + merkki)
    merkki += "**"
    koko -= 1
print(" " * i + "*")
```
Annoin tiedostolle ajo-oikeudet komennolla `sudo chmod ugo+x hello.py` ja testasin ajaa komentoa:
```bash
miika@UbuntuServer:/srv/salt/komennot$ ./hello.py
Hei maailma

Syötä joulukuusen koko: 13
Tässä on sinulle joulukuusi

            *
           ***
          *****
         *******
        *********
       ***********
      *************
     ***************
    *****************
   *******************
  *********************
 ***********************
*************************
            *
```
Tämän jälkeen muokkasin taas init.sls tiedostoa ja lisäsin komennon sinne oikeuksineen. Nyt tiedosto oli tämän näköinen:
```bash
miika@UbuntuServer:/srv/salt/komennot$ sudoedit init.sls 

NANO:
/usr/local/bin/hei.sh:
  file.managed:
    - source: salt://komennot/hei.sh
    - mode: '0755'

/usr/local/bin/whatsup.sh:
  file.managed:
    - source: salt://komennot/whatsup.sh
    - mode: '0755'

/usr/local/bin/hello.py:
  file.managed:
    - source: salt://komennot/hello.py
    - mode: '0755'
```
Ja taas testaamaan. Syötin ensin komennon `hello.py` ilman tulosta. Sen jälkeen komennon: `sudo salt-call --local state.apply komennot`
ja homma rokkasi jälleen.

![12](https://user-images.githubusercontent.com/112076418/202877854-ef2b6fe1-bb93-4973-8d7a-3dc55ddc0da3.png)
 
Ja vielä sama muille orjille: 
```bash
miika@UbuntuServer:/srv/salt/komennot$ sudo salt '*' state.apply komennot
----------
          ID: /usr/local/bin/hello.py
    Function: file.managed
      Result: True
     Comment: File /usr/local/bin/hello.py updated
     Started: 03:07:07.800297
    Duration: 123.111 ms
     Changes:   
              ----------
              diff:
                  New file
              mode:
                  0755

Summary for UbuntuDesktop
------------
```
Kuva ennen ja jälkeen komennot tilan päivittämisestä (Ubuntu Desktop näkökulma):

![13](https://user-images.githubusercontent.com/112076418/202877970-1ed1806d-7108-41e1-bb12-5c2dd7a5c26d.png)




### d) Laiskaa skriptailua. Tee kansio, josta jokainen skripti kopioituu orjille.

Komentoja alkoi tulla sen verta kovaa vauhtia, että komennoille olisi hyvä tehdä muutenkin oma kansio. Tarkoituksena oli tehdä kansio, jossa olevat komennot kopioituvat automaattisesti orjille ilman, että niitä tarvitsee määrittää erikseen init.sls tiedostoon kuten edellisissä tehtävissä. Eli aina kun "komennot" -tila ajetaan, niin kansiossa olevat komennot asentuvat suoraan /usr/local/bin kansioon.

Aloitin luomalla kansion "komentokansio" /srv/salt/komennot/ sisälle.

```bash
miika@UbuntuServer:~$ cd /srv/salt/komennot
miika@UbuntuServer:/srv/salt/komennot$ sudo mkdir komentokansio
miika@UbuntuServer:/srv/salt/komennot$ ls
hei.sh  hello.py  init.sls  komentokansio  whatsup.sh
miika@UbuntuServer:/srv/salt/komennot$ 
```
Sitten siirsin aikaisemmin tehdyt komennot "hei.sh", "hello.py" ja "whatsup.sh" juuri tehtyyn komentokansioon komennolla: `sudo mv hei.sh hello.py whatsup.sh komentokansio`.

```bash
miika@UbuntuServer:/srv/salt/komennot$ sudo mv hei.sh hello.py whatsup.sh komentokansio
miika@UbuntuServer:/srv/salt/komennot$ ls
init.sls  komentokansio
miika@UbuntuServer:/srv/salt/komennot$ cd komentokansio/
miika@UbuntuServer:/srv/salt/komennot/komentokansio$ ls
hei.sh  hello.py  whatsup.sh
miika@UbuntuServer:/srv/salt/komennot/komentokansio$ 
```
Tiedostot siirretty. Nyt täytyi vielä muuttaa init.sls tiedosto osoittamaan kyseistä kansiota. Menin takaisin /srv/salt/kommennot kansioon ja muokkasin init.sls sisältöä. Poistin aikaisemmat komennot init.sls tiedostosta ja nyt tiedosto näytti huomattavasti siistimmältä ja lyhyemmältä. Tiedostoon täytyi muuttaa "mode:" kohtaan "file_mode" ja file.managed kohtaan "file.recurse". Näin init.sls tiedosto luo /usr/local/bin kansioon kaikki komennot sille osoitetun kansion sisältä. Muistin tämän tunnilta, mutta ohjeita erilaisiin vaihtoehtoihin löytyi myös sivulta: [SaltStack](https://docs.saltproject.io/en/latest/ref/states/all/salt.states.file.html)
```bash
miika@UbuntuServer:/srv/salt/komennot/komentokansio$ cd ..
miika@UbuntuServer:/srv/salt/komennot$ sudoedit init.sls 

NANO:
/usr/local/bin:
  file.recurse:
    - source: salt://komennot/komentokansio
    - file_mode: '0755'
```
Sitten menin ja poistin kaikki vanhat komennot kansiosta /usr/local/bin ja ryhdyin testaamaan.
```bash
miika@UbuntuServer:/srv/salt/komennot$ cd /usr/local/bin
miika@UbuntuServer:/usr/local/bin$ sudo rm *
miika@UbuntuServer:/usr/local/bin$ ls
miika@UbuntuServer:/usr/local/bin$ 
```

Nyt kansio oli tyhjä ja testasin komentoja ennen ja jälkeen tilan ajamista.

![14](https://user-images.githubusercontent.com/112076418/202907654-8028005a-c665-4d54-94fe-0c80a7e12df2.png)

![15](https://user-images.githubusercontent.com/112076418/202907694-bae6f285-5f01-4fe0-8120-7787b1938986.png)

Ja orjakoneella sama juttu. Ensin tiedostojen poisto orjalla(Ubuntu Desktop) ja testaaminen ja sitten tilan ajaminen masterilla(Ubuntu Server) ja uudelleen testaaminen:
```bash
miika@UbuntuDesktop:~$ cd /usr/local/bin
miika@UbuntuDesktop:/usr/local/bin$ ls
hei.sh  hello.py  whatsup.sh
miika@UbuntuDesktop:/usr/local/bin$ sudo rm *
miika@UbuntuDesktop:/usr/local/bin$ ls
miika@UbuntuDesktop:/usr/local/bin$ 
```

![16](https://user-images.githubusercontent.com/112076418/202907930-fe56fcb8-6ed5-4057-b7e0-61b03a3dab03.png)

Masterilla taas tilan ajaminen kaikille orjille:

```bash
miika@UbuntuServer:~$ sudo salt '*' state.apply komennot
```
Sitten uudelleen testaus orjalla syöttämällä komennot:

![17](https://user-images.githubusercontent.com/112076418/202908036-92b3e68d-2579-4b8c-8dda-a6e69e1ab28a.png)

Kaikki harjoitukset toimivat paikallisesti ajettuna, kuin myös toisella orjakoneella. Jatkossa on helppo vain lisätä uusi komento kansioon ja ajaa tila, niin komennot tulevat voimaan kaikilla orjilla. Kätevää!

### e) Intel. Etsi kolme loppuprojektia joltain vanhalta kurssitoteutukselta. Kuvaile projektit tiiviisti, viittaa ja linkitä alkuperäiseeen raporttin. Tässä alakohdassa ei tarvitse vielä kokeilla mitään koneella, vaan voit kuvailla niitä oheismateriaalin perusteella.

Palvelinten hallinta - kevään 2021 "myllys" nimimerkin oma moduuli:

[Steam, pelikauppa, jossa paljon digitaalisia rajoitusmenetelmiä (DRM)](https://myllys.wordpress.com/palvelinten-hallinta-harjoitus-7-oma-moduli-spring-2021/)

Steam -alustan asennus saltin avulla kaikille orjille. Raportissa on step-by-step ohjeet ja vaiheet kuvattu miten tämä onnistuu.
Loppujenlopuksi käyttäjän tarvitsee vain syöttää komento steam, ja steam asentuu. Projektin kulkua oli helppo seurata sen selkeyden ja kuvien takia. Ohjeet ja github linkit löytyvät kaikki raportista.

Palvelinten hallinta - kevään 2021 "Justus-stack" nimimerkin oma moduuli:

[Työpöytäohjelmia: Discord; Firefox asetuksineen, Blender asetuksineen](https://github.com/Justus-stack/h7_moduli/blob/main/report.md)

Erilaisten työpöytäohjelmien asentaminen saltilla. Tila asentaa Discordin, Spotifyn, Firefoxin ja Blenderin. Firefoxiin ja Blenderiin liitetään konfiguraatiotiedostot, jotta ne asentuvat halutuilla asetuksilla. Projekti meni järjestelmällisesti eteenpäin ensin käsin asentamalla ja vasta sitten automatisoimalla. Helppolukuinen ja paljon kuvia.

Palvelinten hallinta - kevään 2021 "Krister" nimimerkin oma moduuli:

[Firefoxin lisäkkeet (Addons), Addblock Plus ja Bleachbit](https://krisunsetit.wordpress.com/2021/05/18/oma-moduli/)

Adblockin asennus Firefoxiin, BleachBit siivioushojelman ja Thunderbird sähköpostisovelluksen asennus saltilla. Projektissa jonkin verran erroria ja virhetilanteita, eikä lopputulos kuulemma ollut haluttu tekijän mukaan, mutta koko prosessi oli hyvin raportoitu ja maaliin päästiin!


### e) Lukua, ei luottamusta. Kokeile yhtä kohdassa d-Intel löytämääsi modulia koneella. Tämä on infraa koodina, joten luottamusta ei tarvita. Voit lukea koodista, mitä olet ajamassa.

Päätin kokeilla "myllys" nimimerkin [Steam](https://myllys.wordpress.com/palvelinten-hallinta-harjoitus-7-oma-moduli-spring-2021/) asennus modulia. Menin linkissä olevalle sivulle ja sieltä linkillä modulin github sivulle: https://github.com/myllys/moduuli.

![19](https://user-images.githubusercontent.com/112076418/202932848-7bf78aa1-17e2-4294-b083-ac44eec72d5f.png)

Aloitin kloonaamalla repositoryn ylläolevan kuvan https linkin avulla.
```bash
miika@UbuntuDesktop:~$ git clone https://github.com/myllys/moduuli.git
Cloning into 'moduuli'...
remote: Enumerating objects: 45, done.
remote: Counting objects: 100% (45/45), done.
remote: Compressing objects: 100% (44/44), done.
remote: Total 45 (delta 9), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (45/45), 22.36 KiB | 880.00 KiB/s, done.
Resolving deltas: 100% (9/9), done.
miika@UbuntuDesktop:~$ cd moduuli/
miika@UbuntuDesktop:~/moduuli$ ls
LICENSE  steam
miika@UbuntuDesktop:~/moduuli$ cd steam
miika@UbuntuDesktop:~/moduuli/steam$ ls
init.sls  steam.gpg  steam.list
```
Kopioin steam kansion sisältöineen /srv/salt/ kansioon.
```bash
miika@UbuntuDesktop:~/moduuli$ sudo cp -r steam /srv/salt/
miika@UbuntuDesktop:~/moduuli$ cd /srv/salt/steam
miika@UbuntuDesktop:/srv/salt/steam$ ls
init.sls  steam.gpg  steam.list
miika@UbuntuDesktop:/srv/salt/steam$ 
```
Kurkkasin init.sls tiedostoon mitä se pitää sisällää:
```bash
miika@UbuntuDesktop:/srv/salt/steam$ cat init.sls 
# steam.gpg key
/etc/apt/trusted.gpg.d/steam.gpg:
  file.managed:
    - source: salt://steam/steam.gpg
    
# steam.list for install
/etc/apt/sources.list.d/steam.list:
  file.managed:
    - source: salt://steam/steam.list

# need support for 32-bit
sudo dpkg --add-architecture i386:
  cmd.run

# update before installing steam
apt-get update:
  cmd.run

apt-get install:
  cmd.run

# suggested libraries from repo.steampowered
apt-get install -y libgl1-mesa-dri:amd64:
  cmd.run

apt-get install -y libgl1-mesa-dri:i386:
  cmd.run
 
apt-get install -y libgl1-mesa-glx:amd64:
  cmd.run

apt-get install -y libgl1-mesa-glx:i386:
  cmd.run
  
# steam lisenssin ehtojen hyväksyminen
dpkg_steam_license:
  cmd.run:
   - unless: which steam
   - name: '/bin/echo /usr/bin/debconf steam/license note | /usr/bin/debconf-set-selections'
   - require_in:
      - pkg: steam
      - cmd: dpkg_steam_question

dpkg_steam_question:
  cmd.run:
    - unless: which steam
    - name: '/bin/echo /usr/bin/debconf steam/question select "I AGREE" | /usr/bin/debconf-set-selections'
    - require_in:
      - pkg: steam

# If xterm not installed, launching steam will not work on some distros and will give error line 42: xterm command not found
xterm:
  pkg.installed

steam:
  pkg.installed:
    - sources:
      - steam-launcher: https://steamcdn-a.akamaihd.net/client/installer/steam.deb
miika@UbuntuDesktop:/srv/salt/steam$ 
```


Kokeilin ajaa tilan komennolla: `sudo salt-call --local state.apply steam`

. . . .

Hetken odotuksen ja pitkän terminal tulostuksen jälkeen Steam Installer ikkuna avautuu:


![steam](https://user-images.githubusercontent.com/112076418/202933306-07efe031-06b6-4bb4-aa69-1838311174fd.png)

Start Steam klikkaamisen jälkeen aukesi uusi ikkunna, jossa Steam asentaa lisäpaketteja. Painoin tässä enteriä ja asennus jatkui.

![b2da7308ad4020a8ad3738c8fe4a482d](https://user-images.githubusercontent.com/112076418/202933515-ebf46ad4-b651-4475-9559-9e195b1cab47.png)

Steam alkoi päivittämään ja sen jälkeen asentui onnistuneesti.

![steam](https://user-images.githubusercontent.com/112076418/202933527-a3bba231-5565-4fad-b1d5-7e291fb9f0d2.png)

![Steam (2)](https://user-images.githubusercontent.com/112076418/202933608-ceb9b70f-0324-4fac-9524-5e1e3e803acf.png)






Lähteet

Tero Karvinen, Palvelinten hallinta https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/?from=MoodleNews

SaltStack https://docs.saltproject.io/en/latest/ref/states/all/salt.states.file.html

Myllys Steam Moduli, https://myllys.wordpress.com/palvelinten-hallinta-harjoitus-7-oma-moduli-spring-2021/ ja https://github.com/myllys/moduuli



