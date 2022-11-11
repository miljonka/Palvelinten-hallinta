### x) Lue ja tiivistä (Tässä x-alakohdassa pelkkä luku / katselu / kuuntelu riittää, ei tarvitse tehdä testejä tietokoneella. Muutama ranskalainen viiva per artikkeli riittää)

[Karvinen 2020: Command Line Basics Revisited](https://terokarvinen.com/2020/command-line-basics-revisited/)
- artikkeli sisältää paljon hyödyllisiä komentoja, joita voi käyttää Linuxin komentorivillä.
- komennot on avattu selityksillä ja esimerkeillä.

[Karvinen 2018: Salt States – I Want My Computers Like This](https://terokarvinen.com/2018/salt-states-i-want-my-computers-like-this/)
- artikkelissa on ohjeet, joilla voidaan määrittää tietokoneisiin halutut palvelut ja toiminnot Masterin ohjeistuksilla Orjille.
- sisältää linkin Salt Quickstart artikkeliin, jossa on ohjeet ja komennot Mestarin ja Orjan asennukseen.

[Karvinen 2006: Raportin kirjoittaminen](https://terokarvinen.com/2006/raportin-kirjoittaminen-4/?fromSearch=raportin%20kirjoittaminen)
- artikkelissa on ohjeita raportin kirjoittamista varten, kun testataan tietokoneella jotakin.
- esimerkkejä hyvistä käytänteistä ja vaatimuksista, sekä pahimmista mokista.


### a) Tee Saltilla tiedosto yksittäisellä komentorivillä (state.single)

Aloitin harjoitusten tekemisen omalla koneella VirtualBoxin kautta Ubuntu Serverillä (22.04.1).
Lähdin harjoitusten tekoon Tero Karvisen ensimmäisen Palvelinten hallinta -opetuskerran pohjalta.


Tiedoston tekeminen onnistui komennolla:

`sudo salt-call --local state.single file.managed /tmp/miikawashere`

![1](https://user-images.githubusercontent.com/112076418/198856619-84d83d24-98bb-4e3d-97f1-a322a574ce6a.png)

Tarkistin vielä, että tiedosto oli varmasti luotu, ja että se oli oikeassa paikassa:

`ls /tmp/`

![2](https://user-images.githubusercontent.com/112076418/198856629-ff04c4d6-00de-44ee-8698-010667c88a44.png)


### b) Tee saltille idempotentti, infra koodina hei maailma (siis tiedostosta, /srv/salt/foo.sls)

Lisäsin /srv/salt polkuun kansion `hello` ja sinne tiedoston init.sls

Tämän jälkeen lisäsin tiedostoon koodia.

`sudoedit /srv/salt/hello/init.sls`

![3](https://user-images.githubusercontent.com/112076418/198856633-8b67a91b-fa01-4bf8-94b2-61eb4a19961c.png)

Testasin toimiiko koodi vai tuleeko erroreita komennolla:

`sudo salt-call --local state.apply hello`

![oo](https://user-images.githubusercontent.com/112076418/198416306-9508a005-9bc8-4925-b77b-59e8a6655356.png)

Uusi tiedosto luotu. Tarkistin vielä, että se löytyi sieltä mistä kuuluikin.

![toimi1i](https://user-images.githubusercontent.com/112076418/198421097-12bcf5c4-bc99-4fe3-ae09-51d09f46a449.png)


### d) Kerää tietoa koneesta saltin avulla (grains.items)

`sudo salt-call --local grains.items | less` komennolla pystyin selata koneen tietoja

![grains](https://user-images.githubusercontent.com/112076418/198856644-e46ff123-ae88-4f2b-8013-d0338ceda229.png)

### e) Kokeile jotain toista tilaa kuin file.managed.

Halusin kokeilla tilaa `pkg.installed` ja tarkoituksena oli asentaa nudoku peli samassa yhteydessä kun lisätään helloworld tiedosto.
Apua oikean koodin löytämiseen sain sys.state_doc tiedostosta komennolla: `sudo salt-call --local sys.state_doc pkg.installed|less`

Lisäsin koodin samaan tiedostoon aikaisemman kanssa.

`sudoedit /srv/salt/hello/init.sls`

![8](https://user-images.githubusercontent.com/112076418/198856747-1ff75448-d1ec-4c89-a54d-a04089296d11.png)


Testasin toimivuutta taas `sudo salt-call --local state.apply hello` komennolla.

![13](https://user-images.githubusercontent.com/112076418/198856793-82153d70-2a95-4ebd-8fc1-2fdaf01fb367.png)

Varmistin vielä, että onnistuiko asennus avaamalla pelin komennolla `nudoku`

![nudoku1](https://user-images.githubusercontent.com/112076418/198856658-89012e5b-054f-4cf7-8a2b-62324dded255.png)

Hyvin pelittää

### Lähteet

Karvinen, Tero: Ensimmäinen oppitunti 2022-10-28, Palvelinten hallinta -kurssi

Karvinen, Tero 2018: Salt States – I Want My Computers Like This: https://terokarvinen.com/2018/salt-states-i-want-my-computers-like-this/
