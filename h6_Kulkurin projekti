
### x) Lue ja tiivistä (muutamalla ranskalaisella viivalla per artikkeli, poimi esim itsellesi keskeisimmät komennot)

**[Karvinen 2017: Vagrant Revisited – Install & Boot New Virtual Machine in 31 seconds](https://terokarvinen.com/2017/04/11/vagrant-revisited-install-boot-new-virtual-machine-in-31-seconds/)**

- Artikkelissa pika-ohje virtuaalikoneen asennukseen Vagrantilla. Asennuksen jälkeen 
kolmella komennolla virtuaalikone pystyssä ja hallinnassa:
```
   $ vagrant init debian/bullseye64
   $ vagrant up
   $ vagrant ssh
```
- Virtuaalikoneen ja sen tiedostojen tuhoaminen yhdellä komennolla: `$ vagrant destroy`

- Artikkelissa on linkki Teron Karvisen toiseen artikkeliin, joka sisältää paljon hyödyllisiä ohjeita, esimerkiksi kuinka asentaa Vagrantilla orjakone ja määrittää sille masteri valmiiksi.

**[Karvinen 2021: Two Machine Virtual Network With Debian 11 Bullseye and Vagrant ](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/)**

- Artikkelissa on ohjeet ja linkit Vagrantin ja VirtualBoxin asennukseen. 
- Vagrant ja VirtualBox Windowsille: https://developer.hashicorp.com/vagrant/downloads & https://www.virtualbox.org/wiki/Downloads
- Vagrant ja VirtualBox Linuxille: `$ sudo apt-get install vagrant virtualbox`
- Sisältää ohjeet ja Vagrantfile tiedoston, jonka avulla voidaan määritellä asennettavaksi kaksi virtuaalikonetta.

**[Karvinen 2018: Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/)**

- Ohjeet Saltin asennukseen ja siihen, kuinka viritetään herra ja orja, niin että saadaan orja vastaamaan herran käskyihin. 

Lyhykäisyydessään:
```
slave$ sudoedit /etc/salt/minion

master: 10.0.0.88
id: tero

slave$ sudo systemctl restart salt-minion.service

master$ sudo salt-key -A
Unaccepted Keys:
tero
Proceed? [n/Y]
Key for minion tero accepted.
```
&nbsp;
--
&nbsp;

Lähdin seuraavien harjoitusten tekemiseen Tero Karvisen 1.12.2022 pidetyn [Palvelinten hallinta](https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/?from=MoodleNews) -kurssin oppitunnin pohjalta. Apuna muistiinpanot ja aiemmin tunnilla tehdyt tehtävät, sekä ylläolevat artikkelit. Harjoituksissa käytin ympäristönä omaa pöytäkonetta, jossa on Windows 10. 

### a) Hello Vagrant. Asenna virtuaalikone Vagrantilla.

Minulla oli Vagrant jo asennettuna ennen tehtävän aloittamista. Sen sai ladattua ja asennettua osoitteesta: https://developer.hashicorp.com/vagrant/downloads \
Host koneena toimi pöytäkoneeni, jossa on Windows 10, joten nämä tehtävät on tehty PowerShellillä. 

Aloitin tekemällä kansion tulevalle virtuaalikoneelle.
```
PS C:\users\MilDerP> mkdir vagrantVM
PS C:\users\MilDerP> cd vagrantVM
PS C:\users\MilDerP\vagrantVM> ls
PS C:\users\MilDerP\vagrantVM>
```
Yhden virtuaalikoneen asennus onnistui ensin valitsemalla boxi. Valitsin debian/bullseye64 -boxin.
```
PS C:\users\MilDerP\vagrantVM> vagrant init debian/bullseye64
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
PS C:\users\MilDerP\vagrantVM> ls

    Directory: C:\users\MilDerP\vagrantVM

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         02-Dec-22     20:45           3093 Vagrantfile

PS C:\users\MilDerP\vagrantVM>
```
Komento loi boxille konfiguraatiotiedoston, johon ei tässä tehtävässä tarvinnut koskea. Koneen asennus ja käynnistys onnistui komennolla: `vagrant up`


![1](https://user-images.githubusercontent.com/112076418/205443082-b1f76839-1a62-4641-8c60-daaf040ec687.png)


Ylläolevan tapahtumalokin mukaan kone on nyt lisätty VirtualBoxiin, joten tarkistin asian kurkistamalla VM manageriin:


![VagrantVM](https://user-images.githubusercontent.com/112076418/205385392-71b5a965-e26b-431b-95b8-550534bbc2b6.png)


VagrantVM virtuaalikone oli ilmestynyt! Seuraavaksi kokeilin ottaa koneeseen yhteyden ssh:lla:


![vagrantSSH](https://user-images.githubusercontent.com/112076418/205385412-80d9ee18-8ce4-4b94-8afc-0007f7638114.png)


Virtuaalikone asennettu ja yhteys muodostettu onnistuneesti. Nyt minulla oli käytössä Debian virtuaalikone, jonka luominen onnistui kolmella komennolla!
```
$ vagrant init debian/bullseye64
$ vagrant up
$ vagrant ssh
```

### b) Yksityisverkko. Asenna kaksi virtuaalikonetta samaan verkkoon Vagrantilla. Laita toisen koneen nimeksi "isanta" ja toisen "renki1". Kokeile, että "renki1" saa yhteyden koneeseen "isanta" (esim. ping tai nc). Tehtävä tulee siis tehdä alusta, vaikka olisit ehtinyt kokeilla tätä tunnilla.

Kahden virtuaalikoneen asennus samaan verkkoon onnistui nopeasti seuraamalla ohjeita Tero Karvisen [Two Machine Virtual Network With Debian 11 Bullseye and Vagrant ](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/) artikkelista. Käytin artikkelissa sisältävää valmista Vagrantfileä ja muokkasin sitä omien tarpeiden mukaan.

Aloitin luomalla uuden kansion `twoVMs` ja tekemällä kansioon Vagrantfilen, jonne tuli seuraava sisältö:
```
PS C:\Users\MilDerP> mkdir twoVMs
PS C:\Users\MilDerP> cd twoVMs
PS C:\Users\MilDerP\twoVMs> notepad Vagrantfile

# -*- mode: ruby -*-
# vi: set ft=ruby :
# Copyright 2019-2021 Tero Karvinen http://TeroKarvinen.com

$tscript = <<TSCRIPT
set -o verbose
apt-get update
apt-get -y install tree
echo "Done - set up test environment - https://terokarvinen.com/search/?q=vagrant"
TSCRIPT

Vagrant.configure("2") do |config|
	config.vm.synced_folder ".", "/vagrant", disabled: true
	config.vm.synced_folder "shared/", "/home/vagrant/shared", create: true
	config.vm.provision "shell", inline: $tscript
	config.vm.box = "debian/bullseye64"

	config.vm.define "isanta" do |isanta|
		isanta.vm.hostname = "isanta"
		isanta.vm.network "private_network", ip: "192.168.88.101"
	end

	config.vm.define "renki1", primary: true do |renki1|
		renki1.vm.hostname = "renki1"
		renki1.vm.network "private_network", ip: "192.168.88.102"
	end
	
end
```
```
PS C:\Users\MilDerP\twoVMs> ls

    Directory: C:\Users\MilDerP\twoVMs

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         03-Dec-22     14:26            828 Vagrantfile.txt
```
Notepad tekee tiedostosta automaattisesti tekstitiedoston, jolloin Vagrantfile ei toimi kun sitä yritetään pystyttää:

![txtisbad](https://user-images.githubusercontent.com/112076418/205441086-61ea57e6-66b6-4179-8ea2-8b34779700e3.png)

Uudelleennimesin tiedoston komennolla: `mv Vagrantfile.txt Vagrantfile`
```
PS C:\Users\MilDerP\twoVMs> mv .\Vagrantfile.txt Vagrantfile
PS C:\Users\MilDerP\twoVMs> ls

    Directory: C:\Users\MilDerP\twoVMs

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         03-Dec-22     14:26            828 Vagrantfile
```
Toinen tapa olisi voinut olla tehdä kommentoitu Vagrantfile komennolla `vagrant init` ja lisätä sisältö sinne.

Sitten kokeilin pystytystä uudelleen:
```
PS C:\Users\MilDerP\twoVMs> vagrant up                                                                                
Bringing machine 'isanta' up with 'virtualbox' provider...                                                              
Bringing machine 'renki1' up with 'virtualbox' provider... 
==> isanta: Importing base box 'debian/bullseye64'...
==> renki1: Importing base box 'debian/bullseye64'...
...
...

```
Pitkän tulostuksen jälkeen koneiden pitäisi nyt olla käytettävissä. Katsoin VM Manageriin ja sinne oli ilmestynyt kaksi uutta virtuaalikonetta, kuten oli tarkoitus.

![kaksiuutta](https://user-images.githubusercontent.com/112076418/205441807-d08b1e20-2f1f-46b5-b536-7c40c13c0bd0.png)

Sitten kokeilin ottaa ssh yhteyden molempiin koneisiin:

![renk1](https://user-images.githubusercontent.com/112076418/205442148-4166614e-c269-47b4-92a4-977ca78a4d95.png)![8430c69914adfa9b348fce2a96521c68](https://user-images.githubusercontent.com/112076418/205442170-1565cb2f-a07a-4d6f-996d-451940944f9b.png)

Molemmat koneet onnistuneesti asennettu ja niihin saadaan yhteys. Seuraavaksi oli tarkoitus pingata isanta-koneella renki1-konetta ja toisinpäin selvittääkseni yhteyden tilanteen. Ensin täytyi tietysti saada IP-osoitteet. Ne määriteltiin Vagrantfileä tehdessä, jotka tässä tapauksessa olivat:

isanta.vm.network "private_network", ip: "192.168.88.101" \
renki1.vm.network "private_network", ip: "192.168.88.102"

`vagrant@renki1:~$ ip a` toimisi myös IP-osoitteen selvittämiseksi.

Ensin kokeilin isanta-koneella pingata renki1-konetta:

![ping renki](https://user-images.githubusercontent.com/112076418/205442900-316a3ba4-86a6-4b1d-a7ae-360522fcbcde.png)

ja sen jälkeen renki1-koneella isanta-konetta:  

![pingisanta](https://user-images.githubusercontent.com/112076418/205442915-05150440-fb0f-43bc-beaf-be4b33618688.png)

Molemmilla pingailu onnistui ja nyt minulla oli koneet isanta ja renki1 samassa verkossa.

### c) Salt master-slave. Toteuta Salt master-slave -arkkitehtuuri verkon yli. Aseta edellisen kohdan kone renki1 orjaksi koneelle isanta.

Aloitin tehtävän ensin asentamalla isanta-koneeseen salt-master demonin ja renki1-koneeseen salt-minion demonin:
```
vagrant@isanta:~$ sudo apt-get install salt-master 
vagrant@renki1:~$ sudo apt-get install salt-minion
```
Sitten määritin renki1-koneelle masteriksi isanta-koneen. Eli menin /etc/salt/ kansioon ja lisäsin minion tiedostoon isanta-koneen IP-osoitteen.
```
vagrant@renki1:/etc/salt$ sudoedit minion
master: 192.168.88.101
id: renki1
```
Sitten käynnistin vielä minion demonin uusiksi, jotta asetukset tulevat voimaan:
```
vagrant@renki1:/etc/salt$ sudo systemctl restart salt-minion
```
Nyt renki1-koneen pitäisi olla koputtelemassa isanta-konetta avaimen kera. Menin isanta-koneelle tarkistamaan asian katsomalla onko avainta tullut:

![saltkey](https://user-images.githubusercontent.com/112076418/205459854-64ad1e91-e54c-47b6-941e-9972b5160f8a.png)

Hyväksyin avaimen ja nyt renki1 on isännän orjakone. Testasin vielä, että vastaako se käskyihin.

![hello](https://user-images.githubusercontent.com/112076418/205461393-3997cc8b-4950-4c16-86e1-3405e19625dd.png)

Orja vastasi käskyihin ja nyt renki1 oli onnistuneesti isännän orja.

### d) Oma suola. Tee ensimmäinen työversio projektistasi. Miniprojektilla tulee olla jokin tarkoitus, vaikka se olisi keksitty. Projektilla tulee olla sivu (esim. Github, Gitlab...), josta selviää projektin perustiedot. Toiminnallisuutta tulee olla kokeiltu, mutta sen ei tarvitse olla valmis. Valmiit projektit esitellään viimeisellä tapaamiskerralla. Tässä tehtävässä palautettava työversio ei siis ole vielä lopullinen.

Coming soon

### Lähteet

[Configuration Management Systems - Palvelinten Hallinta ](https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/?from=MoodleNews)

[Karvinen 2017: Vagrant Revisited – Install & Boot New Virtual Machine in 31 seconds](https://terokarvinen.com/2017/04/11/vagrant-revisited-install-boot-new-virtual-machine-in-31-seconds/)

[Karvinen 2021: Two Machine Virtual Network With Debian 11 Bullseye and Vagrant ](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/)

[Karvinen 2018: Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/)
