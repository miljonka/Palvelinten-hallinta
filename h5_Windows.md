Lähdin seuraavien harjoitusten tekemiseen Tero Karvisen 24.11.2022 pidetyn Palvelinten hallinta -oppitunnin pohjalta. Apuna muistiinpanot ja aiemmin tunnilla tehdyt tehtävät. Harjoituksissa käytin ympäristönä omaa pöytäkonetta, jossa on Windows 10, sekä VirtualBoxin kautta Ubuntua (22.04.1 LTS).

### a) Hello Window Salt! Tee Windowsille SLS-tiedostoon Salt-tila, joka tekee tiedoston nimeltä "suolaikkuna.txt".

Tämän tyyppiinen tehtävä tulikin jo kurssin ensimmäisellä viikolla tehtyä (h1 Hello Salt), mutta ympäristönä toimi silloin Ubuntu (Linux). Lähdin tekemään tehtävää jotakuinkin samalla periaatteella kuin tein [h1_Hello Salt, b) kohdassa.](https://github.com/miljonka/Palvelinten-hallinta/blob/main/h1_Hello%20Salt.md) Tämä tehtävä oli tehty Windowsin PowerShellillä.

Aloitin luomalla "temp" kansion, jonne laitan kaikki tulostukset ja testitiedostot näistä tehtävistä. Tein kansion temp C: aseman juureen komennolla: `mkdir temp`

```powershell
PS C:\>
PS C:\> mkdir temp
PS C:\> cd temp
PS C:\temp> ls
PS C:\temp>

```
Nyt minulla oli tyhjä testikansio valmiina tehtävää varten, joten siirryin itse tehtävään.

Aloitin tekemällä C:/ polkuun srv/salt/saltwindow kansiot. Tein kansiot samalla tavalla kuin tekisin Ubuntulla selkeyden vuoksi. Eli "saltwindow" on tila, joka luo tiedoston nimeltä "suolaikkuna.txt". Tämän jälkeen menin "saltwindow" kansioon ja loin sinne init.sls tiedoston käyttämällä notepadia tekstieditorina komennolla: `notepad.exe init.sls`.  init.sls tiedoston sisällöksi tuli polku mihin tiedosto luodaan ja luotavan tiedoston nimi ja contents osioon tiedoston sisältö.

![notepad](https://user-images.githubusercontent.com/112076418/204102211-9ddeea80-1a0a-4f88-82cd-bdf72057d233.png)

```powershell
PS C:\temp> cd C:/
PS C:\> mkdir /srv/salt/saltwindow
PS C:\> cd /srv/salt/saltwindow
PS C:\srv\salt\saltwindow>
PS C:\srv\salt\saltwindow> notepad.exe init.sls
PS C:\srv\salt\saltwindow> cat .\init.sls
c:/temp/suolaikkuna.txt:
  file.managed:
    - contents: "suolaa"
PS C:\srv\salt\saltwindow>
```

Seuraavaksi kokeilin ajaa tilaa. Oppitunnilla tuli ilmi, että Windowsilla täytyy jostain syystä syöttää komennolle tilan polku, ja tila täytyy ajaa /srv/salt kansiosta. Menin siis kansioon C:/srv/salt ja ajoin tilan komennolla: ` salt-call --file-root=C:/srv/salt --local state.apply saltwindow`. Komento `salt-call --file-root=. --local state.apply saltwindow` ajaa saman asian. Piste kuvastaa nykyistä hakemistoa. Kai tämänkin saa jotenkin muutettua, mutta en käyttänyt aikaa siihen tässä tehtävässä.

```powershell
PS C:\srv\salt\saltwindow> cd ..
PS C:\srv\salt>
PS C:\srv\salt> salt-call --file-root=C:/srv/salt --local state.apply saltwindow
local:
----------
          ID: c:/temp/suolaikkuna.txt
    Function: file.managed
      Result: True
     Comment: File c:/temp/suolaikkuna.txt updated
     Started: 19:35:17.019452
    Duration: 52.703 ms
     Changes:
              ----------
              diff:
                  New file

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1
Total run time:  52.703 ms
PS C:\srv\salt>
```
Tilan ajaminen onnistui, joten tarkistin vielä, että näin oli tapahtunut katsomalla temp kansion sisälle.

![suolaikkuna](https://user-images.githubusercontent.com/112076418/204102070-e73f6f0c-465b-4c73-99c5-fba122d63d9b.png)

Haluttu tila saavutettu!

### b) Ei vihkoa, ei kynää. Kerää Windows-koneen tekniset tiedot tekstitiedostoon. Vapaaehtoinen bonus: Saatko tiedot tallennettua myös json-muodossa?

Aloitin menemällä C:/temp kansioon, jonne halusin tallentaa Windows-koneen tiedot. 

Koneen tiedot Windowsilla onnistui samalla komennolla kuin [h1 Hello Salt d)](https://github.com/miljonka/Palvelinten-hallinta/blob/main/h1_Hello%20Salt.md) kohdassa, eli `salt-call --local grains.items`.

Koneen tiedot tekstitiedostoksi onnistui yksinkertaisesti osoittamalla komennolle, että minkä nimiseen tiedostoon tiedot tallennetaan: `salt-call --local grains.items > koneentiedot.txt`

![123](https://user-images.githubusercontent.com/112076418/204110257-89896814-bb61-4d81-a18e-c0604a3141eb.png)

Tiedot sain tallennettua JSON muotoon [SaltStackin Display return data in JSON format](https://docs.saltproject.io/en/latest/ref/output/all/salt.output.json_out.html) -ohjeen avulla.

 Komennolla `salt-call --local grains.items --out=json > koneentiedot.json` tiedot muuttuivat json muotoon.

![jsonn](https://user-images.githubusercontent.com/112076418/204110217-7b55dc38-bcdf-4c23-8c30-dff983926634.png)

Nyt minulla oli molemmissa muodoissa tallennetut tiedot.

### c) Kop kop. Onko TCP-portti auki vai kiinni? Näytä esimerkit portin kokeilusta Linuxilla ja Windowsilla. Näytä kummallakin käyttöjärjestelmällä ainakin yksi avoin ja yksi suljettu portti. (Kokeile tätä vain omaan koneeseesi. Vieraiden koneiden ja verkkojen porttiskannaaminen on kiellettyä. Yksittäisen portin testaavat komennot ovat suositeltavia, esim. nc, tnc)

Kaikki testit on tehty omassa verkossa ja omilla koneilla!

Lähdin tutkimaan Ubuntun salt-masterin (192.168.1.114) portteja 4505 ja 4506, joita salt-minion (Windows) tarvitsee toimiakseen oikein. Sitten, kun yritän tulevaisuudessa parittaa Windows orjakoneen Ubuntu masterille, niin tiedän ainakin, että portit toimivat. Aloitin tehtävän Windowsilla käyttämällä komentorakennetta `tnc <masterinIP> -port <PORT>`. tnc on lyhenne Test-NetConnection komennosta. Kokeilin portteja järjestyksessä 4505-4508. Ihannetapauksessa porteista 4505 ja 4506 tulisi positiivinen vastaus eli True. 

![ports](https://user-images.githubusercontent.com/112076418/204131809-45db104d-9472-48ca-9657-99ec04d0f9fc.png)

TcpTestSucceeded : True 4505 ja 4506 porteista, eli kaikki kunnossa! Porteista 4507 ja 4508 : False, epäonnistui.. ovet ovat kiinni. Tämä vähentää mahdollista troubleshoottaamista tulevaisuudessa, kun tiedän, että portit 4505 ja 4506 portit ainakin toimivat ja kommunikaatio salt-masterin ja salt-minioneiden välillä pitäisi olla OK.

Kokeilin vielä toistaa saman linuxilla netcatin avulla, jossa komennon sai yhteen pakettiin. Komento: `nc -vz 192.168.1.114 4505-4508`, jossa parametrit: v = verbose, näyttää ruudulla onnistuiko yritys vai ei ja z = zero-I/0 mode, joka jatkaa skannausta, jos komennolle annetaan useampi portti. Komennolla: `netcat -help` löytyi ohjeita komennon käyttöön enemmän. `nc -vz 192.168.1.114 4505-4508` komento skannaa siis portit väliltä 4505-4508.

![nc](https://user-images.githubusercontent.com/112076418/204134947-3048a494-faaf-42ef-8922-ec11d9e6e463.png)

Lopputulos: Ubuntun Portit 4505 ja 4506 vastasivat onnistuneesti, ja olivat auki, kuten halusin. Portit 4507 ja 4508 epäonnistuneesti eli portit olivat kiinni. 

--

Seuraavaksi aloitin Windowsin porttien tutkimisen. Windowsin IP-osoite oli kätevästi tallella aiemmassa tehtävässä luoduissa koneentiedot.txt ja json tiedostoissa. Myös komento ipconfig olisi tietysti toiminut.

![ib](https://user-images.githubusercontent.com/112076418/204135907-98fbaac3-6267-44ea-9cd8-e2b00c4f6430.png)

Windows koneella päätin kokeilla onko ssh portti 22 auki vai kiinni. 

![kuva](https://user-images.githubusercontent.com/112076418/204135962-9c7cee9f-9714-4c23-9ec4-148845087a4f.png)

Kiinni on ja pysyy

Seuraavaksi piti keksiä, mikä portti olisi auki, jota voisi testata. Säästin itseltäni vaivan, että kokeilisin skannata jokaista porttia yksitellen ja annoin komennon `netstat -a`. -a parametri listaa kaikki yhteydet ja kuuntelevat portit. Pitkä lista erilaisia yhteyksiä aukesi ruutuun. Valitsin listasta mielenkiintoisen näköisen portin 57621. 

```powershell
PS C:\> netstat -a

Active Connections

 Proto  Local Address          Foreign Address        State

 TCP    0.0.0.0:57621          DESKTOP-0IDOPPP:0      LISTENING
```

Internetin [SpeedGuiden](https://www.speedguide.net/port.php?port=57621) mukaan porttia käyttää Spotifyn client.

![yo](https://user-images.githubusercontent.com/112076418/204142652-b0cdf95b-f62c-43a8-b8bd-92f26396db34.png)

Samat testit Ubuntulla testattuna, eli `nc -vz -w 5 <windowsIP> <portti>`. Tähän lisäsin parametrin `-w 5`, joka katkaisee pyynnön viiden sekunnin kuluttua, jos vastausta ei kuulu, muuten joutui odottamaan hyvin kauan vastausta.

![linx](https://user-images.githubusercontent.com/112076418/204143011-c6c469ca-fbd6-445a-97fc-8ad09fb0c1d6.png)

Lopputulos: Portti 22 on kiinni, eikä siihen saa yhteyttä ja portti 57621 auki johon yhteys onnistui.

### d) Vapaaehtoinen: Ohjaa Windows-konetta Linuxista Saltilla.

Lähdin tekemään tehtävää ajatuksella, että ensin lisään Windows-koneen orjaksi ja sitten kokeilen ajaa masterilla tilan, joka luo yksinkertaisen tiedoston orjan temp kansioon.

Aloitin määrittelemällä Windows orjalle masterin. Saltin konfiguraatiot Windowsissa sijaitsivat polussa `C:/ProgramData/Salt Project/Salt/conf`. Siellä avasin minion tiedoston ja lisäsin Ubuntun IP-osoitteen masteriksi.

```powershell
PS C:\ProgramData\Salt Project\Salt\conf> notepad.exe minion
```

![conf](https://user-images.githubusercontent.com/112076418/204150296-7e5dfd4c-ba00-493c-9f10-3253a7428496.png)

Masteri määritelty, sitten Ubuntulla täytyi hyväksyä salt-avain. Sitä ennen potkaisin varmuuden vuoksi Windowsin minion demonia, jotta uudet minion-asetukset tulevat varmasti voimaan. Tämä onnistui komennolla `ssm restart salt-minion`

```powershell
PS C:\Programdata\Salt Project\Salt\conf> ssm restart salt-minion
salt-minion: STOP: The operation completed successfully.
salt-minion: START: The operation completed successfully.
PS C:\Programdata\Salt Project\Salt\conf>
```
Tämän jälkeen menin Ubuntu salt-master koneelle ja hyväksyin avaimen komennolla: `sudo salt-key A`


![acept](https://user-images.githubusercontent.com/112076418/204157012-3394ec74-b97b-41bc-b8c6-5e16bb4e8b8e.png)

Nyt miikanWindows on orja. Kokeilin tehdä samanatyylisen tilan kuin a) tehtävässä, eli tila, joka luo C:/temp kansioon tekstitiedoston:

```bash
miika@UbuntuDesktop:~$ cd /srv/salt
miika@UbuntuDesktop:/srv/salt$ sudo mkdir saltwindow
miika@UbuntuDesktop:~$ cd saltwindow/
miika@UbuntuDesktop:/srv/salt/saltwindow$ sudoedit init.sls
miika@UbuntuDesktop:/srv/salt/saltwindow$ cat init.sls 
C:/temp/suolaikkuna2.txt:
  file.managed:
    - contents: "Suolaa linuxilta"

miika@UbuntuDesktop:/srv/salt/saltwindow$ 
```
Ajoin tilan komennolla: `sudo salt 'miikanWindows' state.apply saltwindow`

![ikkuna2](https://user-images.githubusercontent.com/112076418/204157459-80c9e2b2-062b-466c-b37e-9f0442aeca8c.png)

Tilan ajaminen onnistui, joten seuraavaksi katsoin oliko suolaikkuna2.txt ilmestynyt Windowsin C:/temp kansioon.


![saltlinux](https://user-images.githubusercontent.com/112076418/204157505-d1e52af6-5cd0-47d1-8422-5eaf1750947f.png)

Olihan se, nyt pystyn hallita Windows konettani Linuxilla!

### e) Vapaaehtoinen: Asenna ohjelmia Windowsiin Saltin pkg.installed. Ensin on tämä on toki viriteltävä käyttöön, koska Windowsissa ei ole omaa kattavaa paketinhallintaa. 

Tämän tehtävän tein pitkälti Teron [Control Windows with Salt](https://terokarvinen.com/2018/control-windows-with-salt/) artikkelin mukaan.

Aloitin luomalla uuden kansion "win" /srv/salt polkuun. Muutin artikkelin ohjeiden mukaan kansion omistuksen "root.salt" ja annoin user + group ryhmille lukun, kirjoitus ja ajo-oikeuden tiedostoihin. Tämän jälkeen päivitin repositoryt windowsille.

```bash
miika@UbuntuDesktop:~$ sudo mkdir /srv/salt/win
miika@UbuntuDesktop:~$ sudo chown root.salt /srv/salt/win
miika@UbuntuDesktop:~$ sudo chmod ug+rwx /srv/salt/win 
miika@UbuntuDesktop:~$ sudo salt-run winrepo.update_git_repos
https://github.com/saltstack/salt-winrepo-ng.git:
    /srv/salt/win/repo-ng/salt-winrepo-ng
https://github.com/saltstack/salt-winrepo.git:
    /srv/salt/win/repo/salt-winrepo
miika@UbuntuDesktop:~$ sudo salt -G 'os:windows' pkg.refresh_db
miikanWindows:
    ----------
    failed:
        0
    success:
        309
    total:
        309
```

Nyt Windows koneelle pitäisi pystyä lataamaan paketteja. Kokeilin suoraan tehdä tilan, joka asentaa pari pakettia.

```bash
miika@UbuntuDesktop:~$ cd /srv/salt/win
miika@UbuntuDesktop:/srv/salt/win$ sudoedit init.sls
miika@UbuntuDesktop:/srv/salt/win$ cat init.sls 
packages:
  pkg.installed:
    - pkgs:
      - gedit
      - vlc
```
Ennen tilan ajamista katsoin kyseisten pakettien tilanteen Windows koneeltani:

![yay](https://user-images.githubusercontent.com/112076418/204159178-20a6a05c-5fbb-409b-9af4-bce00a31fae0.png)

![novlc](https://user-images.githubusercontent.com/112076418/204159180-268b5516-e6ae-4e4a-b5c2-e2bd2cb2ff6d.png)

Ei löytynyt kumpaakaan. Sitten kokeilin ajaa tilan: `sudo salt 'miikanWindows state.apply win`

![installed](https://user-images.githubusercontent.com/112076418/204159352-181f6dc8-f1c4-45a9-87d1-60219d109356.png)

Tila onnistui! Sitten katsoin, oliko tosiaan tapahtunut näin:

![jee](https://user-images.githubusercontent.com/112076418/204159378-377f9262-3b15-4700-9ac1-2a220d46cd7d.png)

![yesvlc](https://user-images.githubusercontent.com/112076418/204159401-b4a80c11-b912-4d52-93f7-6f9c1fca38d6.png)

Kyllä vain! Sain asennettua VLCn ja geditin Ubuntulla salt tilan avulla Windowsille. Molemmat vielä toimivat.

![molemmat](https://user-images.githubusercontent.com/112076418/204159545-e8a4b252-8a63-4be7-9852-be773c905b69.png)

### Lähteet

Tero Karvinen, [Palvelinten hallinta](https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/?from=MoodleNews)

Tero Karvinen, [Control Windows with Salt](https://terokarvinen.com/2018/control-windows-with-salt/)

SaltStack, [Display return data in JSON format](https://docs.saltproject.io/en/latest/ref/output/all/salt.output.json_out.html)




