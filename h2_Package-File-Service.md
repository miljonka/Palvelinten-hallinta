### x) Lue ja tiivistä

[Karvinen 2018: Salt Quickstart - Salt Stack Master and Slave on Ubuntu Linux](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/)
- Artikkeli sisältää ohjeet ja komennot Masterin ja Orjan asennukseen
- Sisältää ohjeet asetuksiin niin, että Orjilta saadaan vastaus Masterin kutsuihin ja käskyihin
- Linkkejä SaltStackin dokumentteihin (Ohjeita, komentoja, moduuleja), sekä Hardyn SaltStack Cheatsheet 
- Linkin "Leppälahti, Kolkki, Koski, Laine 2017: Arctic CCM: Salt" sisältöä ei löytynyt githubista (Error 404)
<br>

Salt official documentation: [Salt Getting Started Guide-kirjasta luvut Understanding SaltStack ja SaltStack Fundamentals ja SaltStack Configuration Management: Functions](https://docs.saltproject.io/en/getstarted/)

- [Understanding SaltStack](https://docs.saltproject.io/en/getstarted/system/index.html) osiossa kerrotaan siitä, miten Salt toimii ja miten sitä voidaan hyödyntää.
  - Saltin skaalaantuvuus, hallittseminen ja automatisointi selitettyinä
  - Saltin toiminta selitettynä kuvien kanssa
  - Ei tarvitse osata ohjelmointikieliä, infrakoodi riittää
  - Mistä data saadaan ja miten se jaetaan (Salt pillar, Salt mine ja Grains)

- [SaltStack Fundamentals](https://docs.saltproject.io/en/getstarted/fundamentals/index.html) osiossa on ohjeet testiympäristön luomiseen.
  - Ohjeet ja video Saltin asennukseen ja asetuksiin, sekä kuinka hyväksyä avaimet ja testata, että kaikki toimii
  - Sisältää esimerkkejä yksinkertaisistaa komennoista ja Saltin syntaksista
  - Orjien valikointi
  - Ohjeita tilojen luomiseen ja niiden päälle laittamiseen 
  - Termien avausta ja niiden käyttötarkoituksia

- [SaltStack Configuration Management](https://docs.saltproject.io/en/getstarted/config/index.html) tukee SaltStack Funamentals osiota
  - Tilojen funktioiden muokkaaminen, suorittaminen ja järjestys
  - Lisää syntaksista 
  - Jinjan käyttötarkoitukset ja esimerkit sen käytöstä

<br>
<br>

Lähdin seuraavien harjoitusten tekemiseen Tero Karvisen 3.11.2022 pidetyn Palvelinten hallinta -oppitunnin pohjalta. Apuna muistiinpanot ja muutama kuvakaappaus tunnilta.
Harjoituksissa käytin VirtualBoxin kautta Ubuntu Desktoppia(22.04.1 LTS) ja UbuntuServeriä (22.04.1 LTS)


### a) Demonin asetukset. Säädä jokin demoni (asenna+tee asetukset+testaa) package-file-service -rakenteella. Ensin käsin: muista tehdä ja raportoida asennus ensin käsin, vasta sitten automatisoiden. Jos osaat hyvin, voit tehdä jonkin eri asetuksen kuin tunnilla. Harjoitusta varten tulee siis tehdä alusta ja raportoida samalla.

Päätin ryhtyä säätämään Apache demonia. Tarkoituksena oli tehdä apachen avulla paikallinen weppisivu localhost ja lisätä käyttäjäkohtainen sivu localhostin alle. Tässä tapauksessa `http://localhost/~miika`
Tavoitteena oli saada sivulle näkymään teksti "Hei maailma" komennolla: <br>
`curl http://localhost/~miika`


![1](https://user-images.githubusercontent.com/112076418/200198920-08a74a17-b260-4280-a1b9-4901c04c34a5.png)

Ylhäällä olevat testit tietysti epäonnistuivat, koska aloitin puhtaalta pöydältä niin, että apachea ei oltu vielä asennettu.

Aloitin asentamalla ensin apachen komennolla: `sudo apt-get install apache2` <br>
Asennuksen jälkeen tarkistin onko apache2 päällä komennolla: `sudo systemctl status apache2`

![2](https://user-images.githubusercontent.com/112076418/200199408-6d3253ea-69b7-4afc-9732-52a0e312856f.png)

Apache oli asennettu ja aktiivisena.
Kokeilin lisätä omaa tekstiä apachen oletussivulle komennolla: <br> `echo "Hello world, this is a test" | sudo tee /var/www/html/index.html` <br>

Testasin `curl localhost` komennolla, näyttäisikö sivu mitään sisältöä.

![3](https://user-images.githubusercontent.com/112076418/200199443-6835725a-d8f1-4b73-bd0a-8308a4b1fd4f.png)

Testi meni meni läpi, joten siirryin seuraavaan vaiheeseen. <br>
Userdir moduulin kytkeminen päälle onnistui komennolla: `sudo a2enmod userdir` <br>

![a2](https://user-images.githubusercontent.com/112076418/200199767-2b6ada6a-697d-4e6d-a1f1-be49907ff49a.png)

Jotta uudet asetukset aktivoituisivat, piti apache käynnistää uudestaan.

`systemctl restart apache2`

Tämän jälkeen loin public_html kansion kotihakemistooni komennolla: `mkdir public_html` apachen oletusasetusten mukaisesti ja kansion sisälle index.html tiedoston. index.html tiedoston lisäsin komennolla: `sudo nano public_html/index.html` ja sinne lisäsin tekstin "Hei maailma" <br>
Tämän jälkeen käynnistin apachen varmuuden vuoksi uudestaan: `systemctl restart apache2`

Testasin näyttääkö sivu mitään komennolla: `curl http://localhost/~miika/`


![Hei maailma](https://user-images.githubusercontent.com/112076418/200199014-222eb832-3758-4da3-b215-c7bdf7e88b81.png)


Kaikki näytti toimivan ainakin terminalista käsin, joten tarkistin vielä toisella virtuaalikoneella (Ubuntu Desktopilla).
Syötin Firefoxin kenttään Ubuntu Serverin osoitteen, jonka sain selville serveriltä komennolla `ifconfig`

![ifconfig](https://user-images.githubusercontent.com/112076418/200199696-3364d0f1-4299-4a3c-9e08-62c7bc49517b.png)



![firefox](https://user-images.githubusercontent.com/112076418/200199024-29b183b6-9cdf-4611-8db5-8816ace7ba19.png)


Kaikki toimi käsin asennettuna, joten seuraavaksi oli tarkoitus automatisoida saltilla koko homma, joka sisältää apachen asennuksen, käyttäjäkohtaisen sivun luomisen ja ylläpitämisen.
Tässä tehtävässä auttoivat oppitunneilta saadut kuvankaappaukset, joten ei tarvinnut muistaa ulkoa mitä tiedoston täytyi sisältää.
Aloitin poistamalla apachen, sekä vanhat testit.

`sudo apt-get purge apache2` <br>
`sudo rm -r /var/www//html/index.html` <br>
`sudo rm -r public_html` <br>

Testasin, että poistaminen varmasti onnistui:


![localhost fail](https://user-images.githubusercontent.com/112076418/200199039-28e3303b-e414-415f-a25e-ced87689bd57.png)


![apachefail](https://user-images.githubusercontent.com/112076418/200199044-69687c16-0942-43bd-83d1-8176a1f59e20.png)


Jatkoin harjoitusta tekemällä /srv/salt polkuun kansion `apache` ja sinne index.html tiedoston jossa on teksti
"Hello, test test test".

Sen jälkeen lisäsin init.sls tiedoston.

`sudoedit /srv/salt/apache/init.sls`

Lisäsin tiedostoon seuraavat asetukset alla olevan tunnilta saadun kuvakaappauksen mukaan, sekä käskyn luoda kotihakemistoon public_html kansio ja sinne sivu index.html, jossa teksti "Hei maailma":

![asetukset](https://user-images.githubusercontent.com/112076418/200199053-6b0161c0-02b2-4614-aad5-aede9cbb33ed.png)


![init](https://user-images.githubusercontent.com/112076418/200199056-852e8461-594b-4053-a16b-bd4114b76e07.png)


Asetukset oli laitettu ja testasin ajamalla komennon: `sudo salt-call --local state.apply apache`


![toimii](https://user-images.githubusercontent.com/112076418/200199061-93c31732-9e5c-4a98-a359-4f184d6f1240.png)


Kaikki näytti ainakin olevan kunnossa, mutta suoritin vielä lisätestejä.

Testasin, toimiiko `curl http://localhost` ja `curl http://localhost/~miika/`


![curlcurl](https://user-images.githubusercontent.com/112076418/200200137-fc97cff8-3e2a-4a3a-b749-fd8b8f580672.png)


Kokeilin vielä pysäyttää apachen ja katsoa meneekö se takaisin päälle:

`sudo systemctl stop apache2` <br>
`sudo systemctl status apache2`


![status](https://user-images.githubusercontent.com/112076418/200199071-3a054b8d-adc7-4616-ad60-c320968b1d68.png)


`sudo salt-call --local state.apply apache`


![true](https://user-images.githubusercontent.com/112076418/200199078-2d148af1-3d41-4232-8f31-688c0d62018d.png)


Kaikki toimi niinkuin piti, joten tein vielä viimeisen testin, jossa poistin taas apachen, sekä vanhat testit:

`sudo apt-get purge apache2` <br>
`sudo rm -r /var/www/html/index.html` <br>
`sudo rm -r public_html` <br>

Ajoin komennon: `sudo salt-call --local state.apply apache` 
Ja tuloksena oli onnistunut tila: 


![finaltest](https://user-images.githubusercontent.com/112076418/200199088-2e582da0-d662-4f45-9e49-2f70c40d519b.png)


Ja Ubuntu Desktopilta näkymä:


![firefox2](https://user-images.githubusercontent.com/112076418/200199094-672d7027-03d4-4bff-b145-816f3f3f4618.png)



### b) Asenna Salt herra-orja niin, että molemmat ovat samalla koneella. Voit tehdä ne esimerkiksi uudelle virtuaalikoneelle, niin pääset kokeilemaan puhtaalta pöydältä.

Ohjeet asennukseen löytyivät Teron [Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/index.html) artikkelista

Herra-Orjan asennus onnistui komennolla: `sudo apt-get -y install salt-master salt-minion`


![saltmasterminion](https://user-images.githubusercontent.com/112076418/200199156-d2163301-38ab-4ddb-95a0-33b2f6782a78.png)


Orjalle täytyi määritellä Herra tiedostossa `/etc/salt/minion`

`sudoedit /etc/salt/minion`


![master](https://user-images.githubusercontent.com/112076418/200200455-24629b1d-04e5-4d7d-955d-ca485ce240d2.png)


Tässä tapauksessa Herra oli sama tietokone kuin orja, joten osoite oli sama. 
Jotta asetukset saatiin voimaan, täytyi Orja demoni käynnistää uudelleen.

`sudo systemctl restart salt-minion`

Jotta Herra pystyi käskyttämään Orjaa, täytyi Herran hyväksyä orjan lähettämä avain komennolla: `sudo salt-key -A`


![salt-A](https://user-images.githubusercontent.com/112076418/200199109-466020b5-558f-4daf-a57c-a61fcad9926c.png)



Avaimen hyväksyttyä, minulla oli oikeus käskyttää Orjaa. Kysyin kaikkien ('*') orjien nimeä komennolla: `sudo salt '*' cmd.run hostname`


![orjat](https://user-images.githubusercontent.com/112076418/200199115-75b96a4b-561a-4060-90a8-9d4f5e2a8a6f.png)


Tässä vaiheessa olin jo tehnyt vapaaehtoisen tehtävän d) Vapaaehtoinen: Asenna Salt herra ja orja eri koneille.
UbuntuDesktop oli toinen virtuaalikoneeni, josta tein Orjan UbuntuServerille.

### c) Aja jokin tila paikallisesti ilman master-slave arkkitehtuuria. Analysoi debug-tulostetta. 'sudo salt-call --local state.apply hellotero -l debug'

`sudo salt-call --local state.apply hello -l debug | less`


![dewbug](https://user-images.githubusercontent.com/112076418/200200272-59667abf-2013-49ee-9428-e7c5168ba114.png)



Tein tarkoituksella typon ensimmäisellä harjoituskerralla luotuun tiedostoon ja poistin `- contents` kohdasta `:` ennen "Hello Hello world!" tekstiä <br>
Debuggaus ehdotti toiseksiviimeisellä rivillä tätä ja sisälöä kun katsoi niin asia oli juurikin näin. Lisäsin `:` kohtaan josta se puuttui ja tiedosto oli taas kunnossa.


![ouraid](https://user-images.githubusercontent.com/112076418/200199138-5e6a0c22-f573-4351-a8f3-769b913381a2.png)

### Lähteet

Karvinen, Tero: Toinen opetuskerta 2022-11-03, Palvelinten hallinta -kurssi <br>
Karvinen, Tero Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux
