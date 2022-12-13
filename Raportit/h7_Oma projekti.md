EDIT: [My project ](https://github.com/miljonka/miniproject).

My idea for the project was to come up with a [Salt](https://saltproject.io/) state that gives you good starter-pack of applications for everyday life. I knew that time was limited, so I chose to start with **Firefox-ESR**, **uBlock** and **Discord**. Im using these applications everyday for studying, working and during my free time, and would wish that every computer in the world would have them installed by default. 

Im doing and testing this project with Debian 11, and Ubuntu 22.04.1 LTS.

This project is done for the course [Configuration Management Systems](https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/?from=MoodleNews) by Tero Karvinen

***
### Installing by hand

I started my project by installing everything first manually. That way I have an idea how the installation is done in the salt state. I started first with Debian 11. Eventhough the default web browser in Debian's GNOME environment is Firefox, I decided to start clean from the start by unistalling Firefox-ESR and its files with the command: `$ sudo apt autoremove --purge firefox-esr` 

Now Im working from the start so lets begin!

![kuva](https://user-images.githubusercontent.com/112076418/206847983-8ddc228f-28f0-4289-ab7c-23687798fe9a.png)


I went on and installed Firefox-ESR back:
```bash
pinokkio@debian:~$ sudo apt install firefox-esr
```
![kuva](https://user-images.githubusercontent.com/112076418/206848957-a644621a-85e1-43ab-9549-f6251b8015f2.png)

Firefox-ESR installed. Next step is to add uBlock extension to it. I have personal experience of using uBlock in all of my computers so I know that it is installed if there is a red shield in top rigth corner on the browser.

![noublock](https://user-images.githubusercontent.com/112076418/206849230-fca1580b-46d1-419b-9e39-6311bf518422.png)

I went to google and found out from [package.debian.org](https://packages.debian.org/bookworm/webext-ublock-origin-firefox) that there is a package called webext-ublock-origin-firefox, so I tried to install that.
```bash
pinokkio@debian:~$ sudo apt install webext-ublock-origin-firefox
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  ublock-origin-doc
The following NEW packages will be installed:
  webext-ublock-origin-firefox
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/2054 kB of archives.
After this operation, 11.0 MB of additional disk space will be used.
Selecting previously unselected package webext-ublock-origin-firefox.
(Reading database ... 149185 files and directories currently installed.)
Preparing to unpack .../webext-ublock-origin-firefox_1.42.0+dfsg-1~deb11u1_all.d
eb ...
Unpacking webext-ublock-origin-firefox (1.42.0+dfsg-1~deb11u1) ...
Setting up webext-ublock-origin-firefox (1.42.0+dfsg-1~deb11u1) ...

```
Package found and installation done! And the result is:

![kuva](https://user-images.githubusercontent.com/112076418/206850501-9e78ae47-37d0-4e3d-9d33-0d2248cb973a.png)

Now that Firefox-ESR and uBlock are working, one thing that must be done is to set default startup page to google.com because of its ease of access to stuff like gmail, drive, youtube, maps and more. Its good stuff to have them available on browser startup instead of having to navigate to google.com everytime. 

For this task, I used Tero Karvinen's [Firefox System Wide Settings – /etc/firefox/syspref.js](https://terokarvinen.com/2016/firefox-system-wide-settings-etcfirefoxsyspref-js/) article as base information that I found out by googling. 

I started by going into the directory /etc/firefox-esr/ to see if there are any files I can configure:
```bash
pinokkio@debian:~$ cd /etc/firefox-esr/
pinokkio@debian:/etc/firefox-esr$ ls
firefox-esr.js
pinokkio@debian:/etc/firefox-esr$ sudo nano firefox-esr.js 
```
![kuva](https://user-images.githubusercontent.com/112076418/206851512-90bea195-a27a-4cab-a6cd-c4501cfba3b4.png)

Good news, looks like I'm in the rigth place! \
I tried adding line `pref('browser.startup.homepage', "http://google.com/ncr");` to the file

![kuva](https://user-images.githubusercontent.com/112076418/206851685-3cf8ee7f-e918-45b3-9372-6bae8c7b601a.png)

Saving and exiting.. and to see the result if something happened or not, I opened Firefox:

![kuva](https://user-images.githubusercontent.com/112076418/206851758-32ef110e-a204-48a4-be3f-3b68273a7c49.png)

YES! Homepage is now google.com like I wanted. Now I have Firefox and uBlock with google.com as a default startup page. \
I made a backup the of firefox-esr.js file to my home directory, so I can use it later in the automating part.
```bash
pinokkio@debian:/etc/firefox-esr$ sudo cp firefox-esr.js ~/
```

Next up is to download and install Discord. \
I first tried to install discord with `$ sudo apt install discord` to see if there are packages available for discord:
```bash
pinokkio@debian:~$ sudo apt install discord
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package discord
pinokkio@debian:~$ 
```
Nope, there are not. I went on to [Discord](https://discord.com/)s homepage and in the frontpage there is a download link for Linux. \
I chose the deb file.

![kuva](https://user-images.githubusercontent.com/112076418/206852177-9723334c-b824-44de-9524-a11c2dffb626.png)

I went on to see the Downloads folder in terminal:

![kuva](https://user-images.githubusercontent.com/112076418/206852293-b0b422a7-a1e6-4692-a30f-1822cd2d5849.png)

I moved the discord.deb file to /home directory so that it is accessible to other users for later when creating the salt state.
```bash
pinokkio@debian:~/Downloads$ sudo mv discord-0.0.22.deb /home
pinokkio@debian:~/Downloads$ ls /home
discord-0.0.22.deb  pinokkio
```

To install the deb file, I used command: `$ sudo dpkg -i /home/discord-0.0.22.deb`:
```bash
pinokkio@debian:~$ sudo dpkg -i /home/discord-0.0.22.deb
Selecting previously unselected package discord.
(Reading database ... 149621 files and directories currently installed.)
Preparing to unpack /home/discord-0.0.22.deb ...
Unpacking discord (0.0.22) ...
dpkg: dependency problems prevent configuration of discord:
 discord depends on libgconf-2-4; however:
  Package libgconf-2-4 is not installed.
 discord depends on libc++1; however:
  Package libc++1 is not installed.

dpkg: error processing package discord (--install):
 dependency problems - leaving unconfigured
Processing triggers for gnome-menus (3.36.0-1) ...
Processing triggers for desktop-file-utils (0.26-1) ...
Processing triggers for mailcap (3.69) ...
Errors were encountered while processing:
 discord
pinokkio@debian:~$ 
```
As the above shows, I got few errors while installing Discord but it still installed Discord and an icon appeared on apps bar. \
I went on to google to find out about the errors and found similiar situations pretty fast ([for example](https://stackoverflow.com/questions/53343241/dependency-problems-leaving-unconfigured)). To install the missing depencencies just incase:
```bash
pinokkio@debian:~$ sudo dpkg --configure -a
dpkg: dependency problems prevent configuration of discord:
 discord depends on libgconf-2-4; however:
  Package libgconf-2-4 is not installed.
 discord depends on libc++1; however:
  Package libc++1 is not installed.

dpkg: error processing package discord (--configure):
 dependency problems - leaving unconfigured
Errors were encountered while processing:
 discord

pinokkio@debian:~$ sudo apt-get -f install
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Correcting dependencies... Done
The following additional packages will be installed:
  gconf-service gconf2-common libc++1 libc++1-11 libc++abi1-11 libgconf-2-4
Suggested packages:
  clang
The following NEW packages will be installed:
  gconf-service gconf2-common libc++1 libc++1-11 libc++abi1-11 libgconf-2-4
0 upgraded, 6 newly installed, 0 to remove and 0 not upgraded.
```
Discord installed and it works!

![kuva](https://user-images.githubusercontent.com/112076418/206852960-1b9f2a5f-dd2e-4927-a4bd-932df03dd8b4.png)

Thats everything installed manually, next up is the most challening part: creating a salt state to do all this. \
Before that its time to unistall everything I just did: 
```bash
pinokkio@debian:~$ sudo apt-get autoremove --purge -y firefox-esr webext-ublock-origin-firefox discord
```

***
### Automating the installing process (Creating salt state to install the apps)

Im doing this part piece by piece to minimize errors and to help troubleshoot problems if they happen to occur.

I started by creating a folder called Starterpack inside /srv/salt path and navigating there.
```bash
pinokkio@debian:~$ sudo mkdir /srv/salt/Starterpack
pinokkio@debian:~$ cd /srv/salt/Starterpack/
pinokkio@debian:/srv/salt/Starterpack$ 
```
### Firefox, uBlock and setting up homepage

I started by creating a simple init.sls file in /srv/salt/Starterpack, containing only firefox installation:
```bash
pinokkio@debian:/srv/salt/Starterpack$ sudoedit init.sls
```
![kuva](https://user-images.githubusercontent.com/112076418/206855305-fa1ca25e-fd66-4454-afdd-24d6a03cb3e6.png)

Time to test. I'll do all tests first locally.

Before applying the state I did not have firefox:

![206847983-8ddc228f-28f0-4289-ab7c-23687798fe9a](https://user-images.githubusercontent.com/112076418/206855694-eb201649-580b-439a-9ac5-0c6b691a2a50.png)

After applying the state with command: `$ sudo salt-call --local state.apply Starterpack`

![206848957-a644621a-85e1-43ab-9549-f6251b8015f2](https://user-images.githubusercontent.com/112076418/206855734-e2c015d1-ab08-4f93-b504-07bfebd6fefe.png)

Firefox installation done. \
Next is uBlock.

Continuing to use the same logic with this one:
```bash
pinokkio@debian:/srv/salt/Starterpack$ sudoedit init.sls 
```
![kuva](https://user-images.githubusercontent.com/112076418/206856002-feafc2fb-7337-417b-b26e-6202fbe51383.png)

Before applying the state I did not have ublock:

![noublock](https://user-images.githubusercontent.com/112076418/206856389-aa4e6978-792d-446e-9238-74c3ad7be653.png)

After applying the state with command: `$ sudo salt-call --local state.apply Starterpack`

![206856209-57d4fca0-be54-4b26-9565-20deac18c009](https://user-images.githubusercontent.com/112076418/206856292-541410be-1161-42d2-88b7-05b7637844fe.png)

Done! Now to setup the startup page Im going to need the file from /etc/firefox-esr which has the settings I had set on the first part when installing everything manually.  Good for me, I copied it to my home folder so I dont have to do that again. Next I'll move that file to /srv/salt/Starterpack
```bash
pinokkio@debian:~$ sudo mv firefox-esr.js /srv/salt/Starterpack/
pinokkio@debian:~$ ls /srv/salt/Starterpack/
firefox-esr.js  init.sls
pinokkio@debian:~$
```
And back to editing the init.sls file..
```
pinokkio@debian:~$ cd /srv/salt/Starterpack/
pinokkio@debian:/srv/salt/Starterpack$ sudoedit init.sls 
```
![kuva](https://user-images.githubusercontent.com/112076418/206857001-cdfc5ea5-b6c5-4241-bf0c-5cf30acf96af.png)

Now when I run the state, it should create the file firefox-esr.js from Starterpack to /etc/firefox-esr/ folder and as a result Firefox homepage should be set to google.

Before applying the state my homepage was not google:

![kuva](https://user-images.githubusercontent.com/112076418/206857112-e47d278a-e39a-4176-97c6-9af3473389ab.png)

After applying the state with command: `$ sudo salt-call --local state.apply Starterpack`

![kuva](https://user-images.githubusercontent.com/112076418/206857188-35c6d2e3-b815-4ace-aa08-87a5a8b2476b.png)

Done! Everything related to Firefox works and below is a recap of all of the states.

![kuva](https://user-images.githubusercontent.com/112076418/206857283-cfc99770-e9eb-4bcb-b245-dc68cebbce8f.png)

### Discord

I started by moving the previously downloaded discord.deb that I used to install Discord manually to /srv/salt/Starterpack 

```bash
pinokkio@debian:~$ ls /home
discord-0.0.22.deb  pinokkio
pinokkio@debian:~$ sudo mv /home/discord-0.0.22.deb /srv/salt/Starterpack/
```
Next up, creating a state that creates discord.deb file to /home directory. Back to editing init.sls
```bash
pinokkio@debian:~$ cd /srv/salt/Starterpack/
pinokkio@debian:/srv/salt/Starterpack$ sudoedit init.sls
```
![kuva](https://user-images.githubusercontent.com/112076418/206861071-8accd67f-dd30-42ff-9f36-12444fa0a4d7.png)

Time to test. Before applying the state discord was nowhere to be found:

![kuva](https://user-images.githubusercontent.com/112076418/206861412-f23be175-57fd-45bc-80bc-27a846ad50c9.png)

After applying the state with command: `$ sudo salt-call --local state.apply Starterpack`

![2c6fc000bb98caf03178b0941c46e443](https://user-images.githubusercontent.com/112076418/206861577-4378f4fe-473f-41da-8947-39965024b562.png)

Discord installed succesfully!

Running `$ sudo salt-call --local state.apply Starterpack` again confirms that everything is in the correct state!

![5d3fefc4eaa61dd95cec61b00ec9f6df](https://user-images.githubusercontent.com/112076418/206861712-87ffa73d-988d-4bbf-acce-22c50183edba.png)

Final test to see if everything is working as it should. I unistalled and removed everything I've just done and then try to re-apply the state.

```
pinokkio@debian:~$ sudo apt-get autoremove --purge -y firefox-esr webext-ublock-origin-firefox discord
pinokkio@debian:~$ sudo rm /home/discord-0.0.22.deb
pinokkio@debian:~$ sudo salt-call --local state.apply Starterpack
local:
----------
          ID: firefox-esr
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: firefox-esr
     Started: 17:34:27.577326
    Duration: 33342.865 ms
     Changes:   
              ----------
              firefox-esr:
                  ----------
                  new:
                      102.5.0esr-1~deb11u1
                  old:
              libdbus-glib-1-2:
                  ----------
                  new:
                      0.110-6
                  old:
----------
          ID: webext-ublock-origin-firefox
    Function: pkg.installed
      Result: True
     Comment: 1 targeted package was installed/updated.
     Started: 17:35:00.924058
    Duration: 7001.637 ms
     Changes:   
              ----------
              webext-ublock-origin-firefox:
                  ----------
                  new:
                      1.42.0+dfsg-1~deb11u1
                  old:
----------
          ID: /etc/firefox-esr/firefox-esr.js
    Function: file.managed
      Result: True
     Comment: File /etc/firefox-esr/firefox-esr.js updated
     Started: 17:35:07.928024
    Duration: 38.153 ms
     Changes:   
              ----------
              diff:
                  --- 
                  +++ 
                  @@ -7,6 +7,11 @@
                   // Note that pref("name", value, locked) is allowed in these
                   // preferences files if you don't want users to be able to override
                   // some preferences.
                  +
                  +//set homepage
                  +pref('browser.startup.homepage', "http://google.com/ncr");
                  +
                  +pref('startup.homepage_welcome_url', "http://google.com/ncr");
                   
                   pref("extensions.update.enabled", true);
                   
----------
          ID: /home/discord-0.0.22.deb
    Function: file.managed
      Result: True
     Comment: File /home/discord-0.0.22.deb updated
     Started: 17:35:07.966324
    Duration: 4339.664 ms
     Changes:   
              ----------
              diff:
                  New file
              mode:
                  0644
----------
          ID: discord_install
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: discord
     Started: 17:35:12.308978
    Duration: 31064.319 ms
     Changes:   
              ----------
              discord:
                  ----------
                  new:
                      0.0.22
                  old:
              gconf-service:
                  ----------
                  new:
                      3.2.6-7
                  old:
              gconf2-common:
                  ----------
                  new:
                      3.2.6-7
                  old:
              libc++1:
                  ----------
                  new:
                      1:11.0-51+nmu5
                  old:
              libc++1-11:
                  ----------
                  new:
                      1:11.0.1-2
                  old:
              libc++abi1-11:
                  ----------
                  new:
                      1:11.0.1-2
                  old:
              libgconf-2-4:
                  ----------
                  new:
                      3.2.6-7
                  old:

Summary for local
------------
Succeeded: 5 (changed=5)
Failed:    0
------------
Total states run:     5
Total run time:  75.787 s
pinokkio@debian:~$ 
```
Success! And to confirm I opened Discord and Firefox. Discord opened correctly, Firefox has google.com as a homepage and uBlock installed.


![kuva](https://user-images.githubusercontent.com/112076418/206863197-d1301b62-7b65-4068-bf87-ac621e566e10.png)

Everything works perfectly locally. Next up is to test if I can apply the state to my Ubuntu minion.

***

### Applying the state to my Ubuntu minion

First, I checked if my previously connected minion responds to me:
```bash
pinokkio@debian:~$ sudo salt '*' cmd.run "echo Hello"
UbuntuDesktop:
    Hello
```
Cool, I got a respond so I assume I can go straigth to testing. Before that, I removed the Snap version of the regular Firefox from Ubuntu with: `miljo@UbuntuDesktop:~$ sudo snap remove firefox`

I applied the Starterpack state to minions on Debian with command: `$sudo salt '*' state.apply Starterpack` 

And the result is...

![kuva](https://user-images.githubusercontent.com/112076418/206879941-2c4698fe-347a-4e47-b3e0-cb9cfba30143.png)

Discord installed but Firefox didnt. I get the following error:
```bash
- Running scope as unit: run-rf0c8ff37cd2d498cbf7de0a59ccb52fa.scope
 E: Package 'firefox-esr' has no installation candidate
```
Im sure the problem is in my Ubuntu minion, so I went on to my UbuntuDesktop and tried to install Firefox-ESR there manually. 

![kuva](https://user-images.githubusercontent.com/112076418/206880785-92efb404-78f0-4e27-aba6-623b7051bb1d.png)

Here I got the same error. Googling a bit about the error, I found this article by [UbuntuHandbook](https://ubuntuhandbook.org/index.php/2022/04/install-firefox-deb-ubuntu-22-04/) saying that Ubuntu 22.04 has removed the .deb package for Firefox. Currently the default way to get Firefox is through a Snap package. I dont want to use Snap, but the .deb package instead, so I followed the articles instructions and tried adding the Mozillateam PPA repository to be able to install Firefox-ESR.

```bash
miljo@UbuntuDesktop:~$ sudo add-apt-repository ppa:mozillateam/ppa
PPA publishes dbgsym, you may need to include 'main/debug' component
Repository: 'deb https://ppa.launchpadcontent.net/mozillateam/ppa/ubuntu/ jammy main'
Description:
Mozilla Team's Firefox stable + 102 ESR and Thunderbird 102 stable builds

Support for Ubuntu 16.04 ESM is included.
More info: https://launchpad.net/~mozillateam/+archive/ubuntu/ppa
Adding repository.
Press [ENTER] to continue or Ctrl-c to cancel.

Fetched 228 kB in 2s (151 kB/s)         
Reading package lists... Done
```

Repository added, now lets try again:
```bash
miljo@UbuntuDesktop:~$ sudo apt install firefox-esr 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  xul-ext-ubufox
The following NEW packages will be installed:
  firefox-esr xul-ext-ubufox
0 upgraded, 2 newly installed, 0 to remove and 21 not upgraded.
Need to get 3 320 B/63,5 MB of archives.
After this operation, 234 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
```
It did find the packages, and Firefox-ESR is installed! Now that I have the repository I'm going to remove Firefox again and try to re-apply the state.

`$ miljo@UbuntuDesktop:~$ sudo apt autoremove --purge firefox-esr`

And running the state on Debian:

```bash
pinokkio@debian:~$ sudo salt '*' state.apply Starterpack
UbuntuDesktop:
----------
          ID: firefox-esr
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: firefox-esr
     Started: 03:19:01.197734
    Duration: 13876.228 ms
     Changes:   
              ----------
              firefox-esr:
                  ----------
                  new:
                      102.6.0esr+build1-0ubuntu0.22.04.1
                  old:
              xul-ext-ubufox:
                  ----------
                  new:
                      3.4-0ubuntu1.17.10.1
                  old:
----------
          ID: webext-ublock-origin-firefox
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 03:19:15.079590
    Duration: 1818.906 ms
     Changes:   
----------
          ID: /etc/firefox-esr/firefox-esr.js
    Function: file.managed
      Result: True
     Comment: File /etc/firefox-esr/firefox-esr.js updated
     Started: 03:19:16.900251
    Duration: 25.69 ms
     Changes:   
              ----------
              diff:
                  New file
              mode:
                  0644
----------
          ID: /home/discord-0.0.22.deb
    Function: file.managed
      Result: True
     Comment: File /home/discord-0.0.22.deb is in the correct state
     Started: 03:19:16.926077
    Duration: 634.889 ms
     Changes:   
----------
          ID: discord_install
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 03:19:17.561121
    Duration: 9.612 ms
     Changes:   

Summary for UbuntuDesktop
------------
Succeeded: 5 (changed=2)
Failed:    0
------------
Total states run:     5
Total run time:  16.365 s
pinokkio@debian:~$ 
```
All states succeeded this time! Now back to Ubuntu to see if everything is working correctly:

![kuva](https://user-images.githubusercontent.com/112076418/206882352-df8748f7-68de-47e8-bc57-6fbf20742f99.png)

Discord is installed and Firefox is installed with uBlock. But homepage is not set to google. I went on to /etc/firefox-esr/ folder where the configuration file is to see whats happening:
```bash
miljo@UbuntuDesktop:~$ cd /etc/firefox-esr/
miljo@UbuntuDesktop:/etc/firefox-esr$ ls
firefox-esr.js  syspref.js
miljo@UbuntuDesktop:/etc/firefox-esr$ 
```
My firefox-esr.js file is there, but there is also a syspref.js file which looks like Ubuntus own version of the file(?) \
My first instict was to remove syspref.js and rename firefox-esr.js to syspref.js to see if it has any effect:

```bash
miljo@UbuntuDesktop:/etc/firefox-esr$ sudo mv firefox-esr.js syspref.js
miljo@UbuntuDesktop:/etc/firefox-esr$ ls
syspref.js
```
Then I tried opening Firefox again and: 

![kuva](https://user-images.githubusercontent.com/112076418/206882544-49a80976-2de7-4849-9d96-f3a8523897a9.png)

Voilá! Homepage set to google. Next thing I had to do was to correct the Debian masters init.sls file so that it adds a file called syspref.js instead of firefox-esr.js if the state is being applied to Ubuntu, and also to add the mozillaPPA repository. So once again, I removed everthing I just did:

```bash
miljo@UbuntuDesktop:~$ sudo apt-get autoremove --purge -y firefox-esr webext-ublock-origin-firefox discord
miljo@UbuntuDesktop:~$ sudo add-apt-repository --remove ppa:mozillateam/ppa
```


### Back to init.sls

So.. I went back to my Debian system and tried to add the repo through pkgrepo.managed (thanks for the tip [therealhalonen](https://github.com/therealhalonen)):

![kuva](https://user-images.githubusercontent.com/112076418/206900591-ff118774-eedd-4d96-ab29-6ca30833cd44.png)

After running the state, I got following:

![ERROR](https://user-images.githubusercontent.com/112076418/206901526-a0ec5c57-0ca4-46e7-b64a-6cb147f40912.png)

Googling about this problem wasn't hard. Plenty of people having the same issue as me ([one example](https://github.com/saltstack/salt/issues/59065)), and apparently its a bug. I couldn't find a good fix so I decided to try and create a state that runs the command: "sudo add-apt-repository -y ppa:mozillateam/ppa"

![kuva](https://user-images.githubusercontent.com/112076418/206910937-4122c8d9-3611-4169-8725-28ef1941657a.png)

And now running the state looks like: 

![kuva](https://user-images.githubusercontent.com/112076418/206910971-6982fe61-0743-4e22-8d02-48b34ea1e298.png)

It works! I'm not sure if this is the rigth way to go, but atleast its working after battling with this issue for quite some time. Last but not least, I added a variable that is set based on if the minion has Ubuntu or not. If the system is Ubuntu, Salt runs the command to get the Mozilla repository and changes the firefox-esr.js filename to syspref.js.

```bash
{% set filename = 'firefox-esr.js' %}

{% if grains['os'] == 'Ubuntu' %}

get_repo:
  cmd.run:
    - name: sudo add-apt-repository -y ppa:mozillateam/ppa

#install_firefox_esr:
#  pkgrepo.managed:
#    - name: ppa:mozillateam/ppa
#    - dist: precise
#    - file: /etc/apt/sources.list.d/mozillateam.list

{% set filename = 'syspref.js' %}

{% endif %}
```


Last test (hopefully) is to remove everything once again, apply the state and see what happens. 

```bash
miljo@UbuntuDesktop:~$ sudo apt-get autoremove --purge -y firefox-esr webext-ublock-origin-firefox discord
miljo@UbuntuDesktop:~$ sudo add-apt-repository --remove ppa:mozillateam/ppa
```

![kuva](https://user-images.githubusercontent.com/112076418/206911776-f64f1cf3-a194-4e65-91ca-19b05a3e3c47.png)

After applying the state:

![kuva](https://user-images.githubusercontent.com/112076418/206911886-81f881fd-d4c2-4d6f-96b1-20f48d5360b9.png)

View on Ubuntu:

![kuva](https://user-images.githubusercontent.com/112076418/206911834-85c1e97d-3cf8-4108-8b13-aace1f5f8213.png)

Everything is working as they should. Firefox-ESR installed with uBlock and homepage set to google and Discord installed! The only problem with my solution is that everytime I apply the state, it executes the get_repo command. Thats why I'll have to edit the init.sls file one more time. Reading abit about [execution of arbitary commands](https://docs.saltproject.io/en/latest/ref/states/all/salt.states.cmd.html), I think I found a solution. I used " - creates: " so that the command under "get_repo:" only gets run if there is no such file to be found:

![kuva](https://user-images.githubusercontent.com/112076418/206913903-5d0eaf6f-f9aa-4113-9af6-3f30c4902864.png)

After tidiying up the init.sls and adding most of the packages together, final look after running Starterpack is:

![kuva](https://user-images.githubusercontent.com/112076418/207172230-59aa3c7e-0255-4858-839c-22d6cbf24551.png)



### Finished project ready to be distributed all over the world can be found [here](https://github.com/miljonka/miniproject).

***
EDIT: \
As a bonus after finding some more time to do this project, I added Flameshot screenshot application to the Starterpack.
It was available to download and install from apt packages. ( $ sudo apt install flameshot ) 

![kuva](https://user-images.githubusercontent.com/112076418/207182252-6b474966-4dc8-48a0-aeef-a2459a78f932.png)

I also did some more tests on Vagrant Ubuntu VMs. To run "add-apt-repository" command for the mozillarepo, software-properties-common package was needed, so I added that aswell to the init.sls file.

Final init.sls file for this project looked like this:

```bash
{% set filename = 'firefox-esr.js' %}

{% if grains['os'] == 'Ubuntu' %}

software-properties-common:
  pkg.installed

get_repo:
  cmd.run:
    - name: sudo add-apt-repository -y ppa:mozillateam/ppa
    - creates: /etc/apt/sources.list.d/mozillateam-ubuntu-ppa-jammy.list

#install_firefox_esr:
#  pkgrepo.managed:
#    - name: ppa:mozillateam/ppa
#    - dist: precise
#    - file: /etc/apt/sources.list.d/mozillateam.list

{% set filename = 'syspref.js' %}

{% endif %}

firefoxNstuff:
  pkg.installed:
    - pkgs:
      - firefox-esr
      - webext-ublock-origin-firefox
      - flameshot

/etc/firefox-esr/{{filename}}:
  file.managed:
- source: salt://Starterpack/firefox-esr.js

/home/discord-0.0.22.deb:
  file.managed:
    - source: salt://Starterpack/discord-0.0.22.deb

discord_install:
  pkg.installed:
    - sources:
      - discord: /home/discord-0.0.22.deb

```



### Sources

Salt https://saltproject.io/ \
uBblock for Firefox https://packages.debian.org/bookworm/webext-ublock-origin-firefox \
Tero Karvinen, Configuration Management Systems https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/?from=MoodleNews \
Tero Karvinen, Firefox systemwide settings https://terokarvinen.com/2016/firefox-system-wide-settings-etcfirefoxsyspref-js/ \
Discord, download https://discord.com/ \
Stackoverflow, Discord dependency problems https://stackoverflow.com/questions/53343241/dependency-problems-leaving-unconfigured \
UbuntuHandbook, Install firefox deb https://ubuntuhandbook.org/index.php/2022/04/install-firefox-deb-ubuntu-22-04/ \
GitHub errors https://github.com/saltstack/salt/issues/59065 \
Salt, Understanding Jinja https://docs.saltproject.io/en/latest/topics/jinja/index.html

