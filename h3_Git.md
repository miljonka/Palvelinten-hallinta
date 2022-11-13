Lähdin seuraavien harjoitusten tekemiseen Tero Karvisen 10.11.2022 pidetyn Palvelinten hallinta -oppitunnin pohjalta. Apuna muistiinpanot ja aiemmin tunnilla tehdyt tehtävät. Harjoituksissa käytin VirtualBoxin kautta Ubuntu Desktoppia(22.04.1 LTS) ja Ubuntu Serveriä(22.04.1 LTS) vapaahtoisissa tehtävissä. Tämä tehtäväraportti on tehty MarkDownina.

### b) Offline. Tee paikallinen offline-varasto git:llä. Varaston nimessä tulee olla sana "cat" (kissa). Aiemmin tehty varasto ei siis kelpaa. Aseta itsellesi sähköpostiosoite ja nimi. Näytä varastollasi muutosten teko ja niiden katsominen lokista.

Aloitin offline-varaston luomisen luomalla kotihakemistoon git varaston "cat>dog" komennolla: `git init "cat>dog"`. 

![1](https://user-images.githubusercontent.com/112076418/201332468-00ad65c0-aeeb-48ad-a935-8ab731ec1a51.png)

Siirryin kyseiseen kansioon ja loin sinne README.md tiedoston, jonne lisäsin tekstin "Koirat vs Kissat battleroyale"

![2](https://user-images.githubusercontent.com/112076418/201332477-c0f105a9-8e73-4da4-9eab-4504d52cd29d.png)

Varasto ja sinne lisätty tiedosto oli tehty. Sitten kokeilin tallentaa uuden varastoni komennoilla `git add .` ja `git commit` 

![3](https://user-images.githubusercontent.com/112076418/201333977-dcf44829-37b9-475b-9f3f-a7d43c7b8ab3.png)

Minun piti vielä kertoa gitille, että kuka minä olen. Tein tämän ylläolevan kuvan ohjeiden mukaisesti.
```
git config --global user.email "rehtirepo@huvitustensaari.fi"
git config --global user.name "pinokkio"
```
Tietojen asettamisen jälkeen kokeilin uudestaan komentoa: `git add .` ja `git commit` (vaihtoehtoisesti `git add . && git commit`)

Commit viestiksi kirjoitin selkeästi sen, mitä olin viimeiseksi lisännyt: "Add readme file" 

![4](https://user-images.githubusercontent.com/112076418/201336378-70b03c7d-53cc-4478-9e0e-fabbd6d9649a.png)

Tämän jälkeen katsoin viimeisimmän commitin muutokset komennolla: `git log --patch`

![5](https://user-images.githubusercontent.com/112076418/201337476-4633970c-f5ba-4a58-9dea-92e533ec8b1a.png)

Kokeilin vielä lisätä aikaisempaan README.md tiedostoon uuden rivin tekstiä, toistamalla `git add . && git commit` komennon ja katsoa tulokset `git log --patch` komennolla:

![6](https://user-images.githubusercontent.com/112076418/201338257-72033b71-1086-476d-a8f5-e26fb6534eb7.png)

### c) Doh! Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia

Huomasin, että joku oli käynyt sotkemassa README.md tiedostoa (minä itse):

![7](https://user-images.githubusercontent.com/112076418/201343131-9d652548-14bf-4917-9dc2-5d7cfb768264.png)

Syötin komennon `git reset --hard` palatakseni aikaisempaan tallennettuun versioon.

![8](https://user-images.githubusercontent.com/112076418/201343704-01014973-47b0-4a94-bf52-e3c5c89edbf2.png)

### d) Online. Tee uusi varasto GitHubiin (tai Gitlabiin tai mihin vain vastaavaan palveluun). Varaston nimessä ja lyhyessä kuvauksessa tulee olla sana "car" (auto). Aiemmin tehty varasto ei kelpaa. (Muista tehdä varastoon tiedostoja luomisvaiheessa, suosittelen tekemään README.md ja vapaista lisensseistä itse tykkään GPLv3 eli GNU General Public License, version 3)

Aloitin menemällä GitHubin sivuille ja kirjautumalla sisään. Oikeassa yläkulmassa profiilikuvan vieressä olevasta + merkistä aukesi valikko, josta valitsin New repository

![8 5](https://user-images.githubusercontent.com/112076418/201345372-d96d689b-9384-4416-810c-37879e2b790a.png)


Tein GitHubiin uuden varaston Teron suosittelemilla tiedostoilla (README.md ja vapaa lisenssi GPLv3) 

![9](https://user-images.githubusercontent.com/112076418/201345448-c47ac583-f7bc-42ce-a750-bf5ba0376095.png)

Luomisen jälkeen näkymä oli:

![10](https://user-images.githubusercontent.com/112076418/201351124-ac66b118-99df-46fd-bf90-c19dccbccd0b.png)

### e) Dolly. Kloonaa edellisessä kohdassa tehty varasto itsellesi, tee muutoksia, puske ne palvelimelle, ja näytä, että ne ilmestyvät weppiliittymään.

Yritin kloonata varaston itselleni kopioimalla varaston SSH URLin ja syöttämällä terminaliin komennon `git clone git@github.com:miljonka/Fast-Car.git`


![11](https://user-images.githubusercontent.com/112076418/201360295-ac0dae97-8507-45df-9625-c80d4dbea0b6.png) ![12](https://user-images.githubusercontent.com/112076418/201360717-860843bf-de56-43af-8e2f-496488f2dac3.png)

Ei onnistunut, koska minulla ei ole oikeita oikeuksia varastoon. Tässä tapauksessa piti lisätä oma julkinen SSH-avain GitHubiin oman profiilin avaimiin. Ensin täytyi tehdä ssh-avaimet komennola `ssh-keygen`:

![13](https://user-images.githubusercontent.com/112076418/201362064-23bb1c46-fae4-4ded-a3a8-581d6ec1f807.png)

Kun avaimet oli luotu, menin kansioon `~/.ssh` ja kopioin sieltä id_rsa.pub avaimen. 

![14](https://user-images.githubusercontent.com/112076418/201362605-ca60e475-d0c4-44ce-9900-70af134c443a.png)

Tämän jälkeen menin GitHubin sivulle ja painoin oikealta yläkulmasta profiilinkuvan kohdalta ja sieltä kohtaan settings ja SSH and GPG keys.

![15](https://user-images.githubusercontent.com/112076418/201363439-34c211fc-58d7-46b4-99ef-f3fb3c8ab82d.png)

Kohdasta New SSH key painamalla pääsi lisäämään avaimen

![16](https://user-images.githubusercontent.com/112076418/201364109-a5203d67-03a2-4de1-9ba2-408ad311e90f.png)

Nyt kun avain oli lisätty, niin tein kansion GitHub ja kokeilin uudestaan komentoa sen sisällä: `git clone git@github.com:miljonka/Fast-Car.git`

![17](https://user-images.githubusercontent.com/112076418/201364869-0ce358cf-56ff-4e48-959a-d4e7b2bc0b6d.png)

Onnistui! Sitten menin Fast-car kansioon ja lisäsin README tiedostoon uuden väliotsikon ja tekstiä:

![18](https://user-images.githubusercontent.com/112076418/201365830-20c74559-d5a4-4e6e-8e52-7a55ff9d4af3.png)

Kokeilin tallentaa ja lisätä muutokset suoraan GitHubiin komennolla: `git add . && git commit; git pull && git push`

![19](https://user-images.githubusercontent.com/112076418/201366304-bb8918db-d2ee-43a0-9908-0a4c8341313a.png)

Tarkistin vielä login komennolla: `git log --patch`

![20](https://user-images.githubusercontent.com/112076418/201366572-cc6d15cb-cee8-41da-8bb0-b39518fdf38f.png)

Kaikki näytti olevan kunnossa, joten menin katsomaan GitHubin sivulle olivatko muutokset tulleet voimaan.

![21](https://user-images.githubusercontent.com/112076418/201366907-42282c4f-daf1-4a9f-967c-8088eefa6d37.png)

Muutokset näkyvät sivulla, sekä commitin tekijä.

### f) Vapaaehtoinen: Tee uusi salt-moduli. Voit asentaa ja konfiguroida minkä vain uuden ohjelman: demonin, työpöytäohjelman tai komentokehotteesta toimivan ohjelman. Käytä tarvittaessa ‘find -printf “%T+ %p\n”|sort’ löytääksesi uudet asetustiedostot. (Tietysti eri ohjelma kuin aiemmissa tehtävissä, tarkoitushan on harjoitella Salttia)


Aikaisemmassa tehtävässä konfiguroin apachen asetuksineen, joten tässä valitsin demoniksi SSH:n.
Tässä tehtävässä käytin VirtualBoxin kautta Ubuntu Serveriä, jossa aiemmat salt harjoituksetkin olin tehnyt. Tarkoituksena oli muodostaa onnistuneesti ssh yhteys UbuntuDesktoppiin, joka aikaisemmin oli jo konfiguroitu Serverin minioniksi.

Lähdin luomaan /srv/salt kansion sisälle uuden kansion nimeltä ssh. Kansioon lisäsin init.sls tiedoston, jonka sisälle ssh:n asetukset. Tilan tarkoituksena oli katsoa, että ssh on asennettu ja, että se on päällä. Kaikki asetukset tulevat salt-masterin sshd_configista.

![22](https://user-images.githubusercontent.com/112076418/201522940-8ce1aecc-251f-488f-adb6-71456c689f0b.png) 

![23](https://user-images.githubusercontent.com/112076418/201523157-98ad7581-27ce-4c20-95a6-37812be0ab3b.png)


Sitten täytyi vielä lisätä itse sshd_config tiedosto /srv/salt/ssh kansioon. Tämä onnistui kopioimalla /etc/ssh/sshd_config kyseiseen kansioon.
`sudo cp /etc/ssh/sshd_config /srv/salt/ssh`

![24](https://user-images.githubusercontent.com/112076418/201523162-3c48128d-ccbd-4f6b-be2f-45b7434d1754.png)

Muutin vielä testimielessä oletusportin portiksi 8888 ja poistamalla risuaidan portin edestä, jotta asetus tulisi voimaan.

![25](https://user-images.githubusercontent.com/112076418/201523428-1e199a3a-0e36-48a9-8947-19577f01d623.png)

Kokeilin tässä välissä ottaa yhteyden toiseen uunituoreeseen minion koneeseeni (Ubuntu Desktop). Oletuksena tietysti se, että mitään ei tapahdu ja niin kävikin: 

![26](https://user-images.githubusercontent.com/112076418/201523816-d0698b35-1699-404f-aa6c-9092e5d80823.png)

Sitten kokeilin aktivoida ssh tilan kaikille minioneille komennolla: `sudo salt '*' state.apply ssh`

![27](https://user-images.githubusercontent.com/112076418/201523906-9ec72ed2-057a-4145-947e-3c8358e99a10.png)

Kaikki näytti menevän ykkösellä purkkiin, joten kokeilin uudestaan yhdistämistä: `ssh -p 8888 miika@192.168.10.7`

![28 ](https://user-images.githubusercontent.com/112076418/201524387-9ab5b301-08f2-4afb-9ebf-5dc5fe82c698.png)

Onnistui!

g) Vapaaehtoinen: Laita salt gittiin. Tee uusi moduli. Kloonaa varastosi toiselle koneelle (tai poista srv/salt ja palauta se kloonaamalla) ja jatka sillä. (Salt tiedostot mistä vain hakemistosta, huomaa suhteellinen polku: 'sudo salt-call --local --file-root srv/salt/ state.apply')

Päätin tehdä gittiin yksinkertaisen tilan, joka luo vieraskäyttäjän ilman salasanaa, ja vieraskäyttäjän työpöydälle tiedoston "testi", jonka sisältönä on teksti "Testi is testi"

![git init sls](https://user-images.githubusercontent.com/112076418/201546601-a1f61ed7-73b1-4838-9747-fccb34e3a4c1.png)

![git](https://user-images.githubusercontent.com/112076418/201546687-36de3668-39c4-4715-b963-f4e53bae9f65.png)

Aloitin kokeilulla vaihtaa käyttäjää: `su vieras`

![34](https://user-images.githubusercontent.com/112076418/201546802-2bb847fc-134a-4570-bfbf-a100f3b6bd08.png)

Eipä tietenkään toiminut. Jatkoin kloonaamalla äsken tekemäni Git varaston: `git@github.com:miljonka/srv.git`

![srv](https://user-images.githubusercontent.com/112076418/201547035-797cf2e2-3bdf-4d34-a7ef-d573141514d3.png)

Sitten kokeilin aktivoida tilan komennolla: `sudo salt-call --local --file-root srv/salt/ state.apply vieras`

![saltcall](https://user-images.githubusercontent.com/112076418/201547162-77291dd0-5e3d-42a6-9bd8-4718b373d04e.png)

Kaikki näytti olevan OK, joten kokeilin uudestaan vaihtaa käyttäjää komennolla: `su vieras`

![suveras](https://user-images.githubusercontent.com/112076418/201547296-4e6e6d72-4435-413d-9169-d9fdae2eecd7.png)

Pääsin vaihtamaan vieraskäyttäjälle ja testikin löytyi! 


