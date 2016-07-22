Complete [Checklist: Start](https://github.com/astrohr/dagor_tca/wiki/Checklist:-Start) first!

## 1. Turn on the operator PC


![](https://raw.githubusercontent.com/astrohr/dagor_tca/master/wiki_files/img/checklist_login/0001.jpg)

![](https://raw.githubusercontent.com/astrohr/dagor_tca/master/wiki_files/img/checklist_login/0002.jpg)

If there is no image on the display, change "input source":

![](https://raw.githubusercontent.com/astrohr/dagor_tca/master/wiki_files/img/checklist_login/0003.jpg)


## 2. Log in to  Windows

![](https://raw.githubusercontent.com/astrohr/dagor_tca/master/wiki_files/img/checklist_login/0005.jpg)

There is no password.


## 3. Log in to TCS via SSH

Open MTPuTTY, and connect to `TCS`:

![](https://raw.githubusercontent.com/astrohr/dagor_tca/master/wiki_files/img/checklist_login/0010.jpg)


Log in using your username and password:

![](https://raw.githubusercontent.com/astrohr/dagor_tca/master/wiki_files/img/checklist_login/0020.jpg)

(password is not displayed when typing it in)

> TCS server starts accepting SSH connections roughly 3 minutes after power on  
> (boot time is 2 minutes for BIOS / POST checks and 1 minute for Linux to start)


## 4. Switch to account `dagor`

Enter `dagor` in the command line prompt.

Password is also `dagor`.

![](https://raw.githubusercontent.com/astrohr/dagor_tca/master/wiki_files/img/checklist_login/0030.jpg)


## Logged in

Now you can access the observatory command line interface. Enter `d` for a quick command reference, or `d --help` for more detailed info.

> It is OK (and sometimes necessary) to login multiple times, using multiple tabs in MTPuTTY.

TODO: 

 * more detailed guide to Dagor CLI






