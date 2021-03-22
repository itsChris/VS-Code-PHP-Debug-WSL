# VS-Code-PHP-Debug-WSL

How to setup your Windows 10 client to write and debug PHP using Visual Studio Code and WSL  (Windows Subsystem for Linux)

## Install/activate WSL Feature on Windows 10
<a href="https://docs.microsoft.com/en-us/windows/wsl/install-win10" target="_blank">Windows Subsystem for Linux Installation Guide for Windows 10</a>

You can try:
 
    WSL --install

if you get (in PowerShell):

> wsl : The term 'wsl' is not recognized as the name of a cmdlet,
> function, script file, or operable program. Check the 
> spelling of the name, or if a path was included, verify that the path
> is correct and try again.
> At line:1 char:1
> + wsl --install
> + ~~~
> + CategoryInfo : ObjectNotFound: (wsl:String) [], CommandNotFoundException
> + FullyQualifiedErrorId : CommandNotFoundException

or (in cmd):

> 'wsl' is not recognized as an internal or external command, operable
> program or batch file.

then you have to take the long way.. which is:

    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
Now reboot!

### Download and install Linux Kernel update package

    (New-Object System.Net.WebClient).DownloadFile('https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi',"$env:temp\wsl_update_x64.msi")

### Install Linux Kernel update package

    Start-Process -FilePath msiexec -Wait -ArgumentList "/i `"$env:temp\wsl_update_x64.msi`" /qn+"

## Download Linux
You can download/install various Linux Distros like Ubuntu, Kali, Debian, Suse, Fedora and OpenSUSE using the Windows Store. I prefer downloading it manually. Links can be found here:
<a href="https://docs.microsoft.com/en-us/windows/wsl/install-manual" target="_blank">Manually download Windows Subsystem for Linux distro packages</a>

### To download Ubuntu 20.04 Appx

    (New-Object System.Net.WebClient).DownloadFile('https://aka.ms/wslubuntu2004',((New-Object -ComObject Shell.Application).NameSpace('shell:Downloads').Self.Path) + "\Ubuntu_2004.2020.424.0_x64.appx")

Head to the download folder and double click the just downloaded Appx. You can also install it using (switch to the Downloads folder first...)

    Add-AppXPackage .\Ubuntu_2004.2020.424.0_x64.appx

### Set WSL Version to 2

    wsl --set-default-version 2

### Start Ubuntu
In your start menu you'll find Ubuntu. Start it. First time setup will run, enter a username and a passwort (and don't forget to write it down!) -> https://www.keepass.info ;-)

### Update Ubuntu
to update your Ubuntu installation:

    sudo apt update -y
    sudo apt upgrade -y
this will take a minute or two .. 
## Install Visual Studio Code

    (New-Object System.Net.WebClient).DownloadFile("https://vscode-update.azurewebsites.net/latest/win32-x64/stable", ((New-Object -ComObject Shell.Application).NameSpace('shell:Downloads').Self.Path) + "\vscode-stable-64.exe")

Head over to your Downloads folder and install vscode-stable-64.exe.
Note: this will install Visual Studio Code 'machine-based' -> you can also download it and install it per user... google will be your friend if you want to know/read more about that.

## Setup Visual Studio Code to debug PHP using WSL

### Setup PHP in WSL
In WSL type:

    php --version

If you get a PHP Version, php is installed, if not, install it using:

    sudo apt install php -y

### Install and configure Xdebug in WSL
<a href="https://xdebug.org/docs/install" target="_black">More about Xdebug</a>

In WSL type: 

    sudo apt install php-xdebug -y

now configure Xdebug.

    nano /etc/php/7.4/cli/conf.d/20-xdebug.ini

check the version of Xdebug using:

    php -r "var_dump(phpversion('xdebug'));"

and add the follow (valid for Version 2.x)

    [XDebug]
    
    xdebug.remote_enable = 1
    xdebug.remote_autostart = 1

for version > 2.x, use google.

### Install PHP Debug in Visual Studio Code

![Screenshot 2021-03-22 151543](https://user-images.githubusercontent.com/2221944/112003768-7e0e9a80-8b21-11eb-8d0d-f7101959c7a0.png)

### Check setup using PhpInfo()

- Fire up WSL
- Switch to home directory -> cd ~/
- Create folder/directory -> mkdir phpHelloWorld
- Switch location/change directory -> cd phpHelloWorld/

Create a phpfile named phpinfo.php using:

    nano phpinfo.php

the file content needs to be: 

    <?php
    phpinfo();

save it.

open Visual Studio Code using 

    code .

Now start a php development web server: 

    php -S localhost:8000

open a Browser and navigate to http://localhost:8000/phpinfo.php

check for

    xdebug.remote_autostart & xdebug.remote_enable 

-> both parameters need to be enabled!

### Download PHP for Windows

https://windows.php.net/download/

Download PHP -> i downloaded 7.4.x
extract it -> i extracted to c:\php

### Add PHP binary path to Visual Studio Code Settings
i added there two lines to the settings file:

    },
    "debug.allowBreakpointsEverywhere": true,
    "php.validate.executablePath": "C:\\php\\php.exe"
    }
