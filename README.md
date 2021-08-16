
## FIRST THING FIRST

This installation list is based on my preferences while trying out Debian 11 (Bullseye) with KDE Plasma 5.20 on my - quite old - macbook and is not a definitive procedure. Feel free to use at your own peril.

<br />

### Settings and Partitions

:computer: Apple MacBook "Core 2 Duo" 2.0 13" (Unibody) Late 2008 Aluminum AKA MacBook5,1

Intel Core2 Duo P7350 @ 2x 1.995Ghz | amd64 EFI | 4GB RAM | NVIDIA GeForce 9400M @ 256MB

| Partition | File System | Mount     | Size              | Flags    |
| ---       | ---         | ---       | ---               | ---      |
| sda1      | fat32       | /boot/efi | 142MiB            | boot,esp |
| sda3      | ext4        | /         | 141.28GiB         |          |
| sda2      | linuxswap   |           | 7.63GiB (7812MiB) | swap     |

UEFI Boot with GUID Partition Table

* From this point on `:$` means instructions in the command line and `:#` means that the instuctions should be run as `root` or with `sudo`.

---

<br />

## WHILE IT'S STILL FRESH

<br />

- [x] **Update OS**
```
:# apt update
:# apt upgrade
```

<br />

- [x] **Install Broadcom Wireless**
* Add a "contrib" and "non-free" components to your existing repository line in `/etc/apt/sources.list` using nano, vi or other editor. Below is an example of said file in Debian 10. 
```
# See https://wiki.debian.org/SourcesList for more information.
deb http://deb.debian.org/debian buster main contrib non-free
deb-src http://deb.debian.org/debian buster main contrib non-free

deb http://deb.debian.org/debian buster-updates main contrib non-free
deb-src http://deb.debian.org/debian buster-updates main contrib non-free

deb http://security.debian.org/debian-security/ buster/updates main
deb-src http://security.debian.org/debian-security/ buster/updates main
```
* *Note:* Do not add a new line. Just add "contrib non-free" to the end of your existing line.
* Update the list of available packages:
```
:# apt update
```
* Install the appropriate firmware installer package:
For devices with a BCM4306 revision 3, BCM4311, BCM4318, BCM4321 or BCM4322 chip, install firmware-b43-installer:
```
:# apt install firmware-b43-installer -y
```
* Reboot.

More information can be found in [Debian Wiki](https://wiki.debian.org/bcm43xx) apple wifi webpage.

<br />

- [x] **Fixing WiFi Connection Problems After Reboot**
* If after rebooting the wireless network device can scan, but will not complete connecting, we can try turning off MAC address randomization.
* Edit `/etc/NetworkManager/NetworkManager.conf` by adding:
```
[device]
wifi.scan-rand-mac-address=no
```

<br />

- [x] **Enable Tapping and Reverse Scrolling (Natural) on Touchpad**
* Create the config file `/etc/X11/xorg.conf.d/40-libinput.conf`. We'll enable **tapping** and **reverse scrolling** by adding the following lines to said file:
```
Section "InputClass"
    Identifier "libinput touchpad catchall"
    MatchIsTouchpad "on"
    MatchDevicePath "/dev/input/event*"
    Driver "libinput"
    Option "Tapping" "on"
    Option "NaturalScrolling" "true"
EndSection
```
* Restart the DM:
```
:$ systemctl restart lightdm
```

More information can be found in [Debian Wiki](https://wiki.debian.org/SynapticsTouchpad#Enable_tapping_on_touchpad) synaptics touchpad webpage.

<br />

- [x] **Sound (For MacBook Aluminum - late 2008)** :speaker:
```
:# alsamixer
```
* Set all up Master, PCM, Line-Out and switch from 2ch to 6ch.
* Search for the **Volume** plug in (PulseAudio Plugin) and add it to the *Panel*, and make sure the **Enable keyboard shortcuts for volume control** is `ON`.

<br />

- [x] **Enable System Sounds**
* XFCE4 supports freedesktop system sounds, but it is not configured out of the box, therefore:
```
:# apt install libcanberra libcanberra-pulse gnome-session-camberra
```
* Add `canberra-gtk-module` to the environment variables:
```
:# nano /etc/environment
```
```
# For the libcanberra-gtk-module to work
export GTK_MODULES=canberra-gtk-module
```
* In *Settings Manager -> Appearance -> Settings* check **Enable event sounds**
* In *Settings Manager -> Settings Editor -> xsettings/Net/SoundThemeName* from `default` to a sound theme located in `/usr/share/sounds/`
* Make sure that **System Sounds** in the audio mixer is **turned on** and not **0**.

This process is based on the information from [ArchLinux Wiki](https://wiki.archlinux.org/index.php/Xfce#Sound_themes) webpage and [this](https://forum.xfce.org/viewtopic.php?id=8618) forum thread.
* *Note: The sound-theme-freedesktop that comes preinstalled provides a compatible sound theme, but it lacks many required events. A better choice is [sound-theme-smooth](https://aur.archlinux.org/packages/sound-theme-smooth/) (SoundThemeName should be "Smooth").*

<br />

- [X] **Bluetooth** :large_blue_diamond:

**Important:** The following procedure is incomplete and untested.
```
:# apt install bluez blueman
```
* From here, it should instal a bluetooth manager to pair a blouetooth device...
* LOL, it did not, so I ran`:$ hcidev` to prove it was installed, then `:$ hciscan` to find out the bluetooth device I wanted to pair and somehow it works now...
:laughing:

<br />

- [x] **Dual Displays** :desktop_computer:

*If you are not using dual displays, it may just be easier to stick with the open-source `nouveau` drivers.*

While XFCE4 is the lightweight linux desktop environments, it is not the friendliest when using multiple displays. This comes from the fact that XFCE treats the display arrangement as one big workspace. So, to simplify things, XFCE prefers to arrenge the 2nd monitor to the right of the primary display, which may not be the configuration that you prefer (I have my Laptop usually on the right). Also, the out-of-the-box `nouveau` drivers limits the max resolution of the external monitor if connected through VGA, thus we may want to change the Graphic Card drivers to the propietary `NVIDIA` ones. Most of this process is based on the information from the [Nvidia Graphics Drivers Wiki](https://wiki.debian.org/NvidiaGraphicsDrivers).

* The NVIDIA graphics processing unit (GPU) series/codename of an installed video card can usually be identified using the `lspci` command.
```
:$ lspci -nn | egrep -i "3D|Display|VGA"
02:00.0 VGA COMPATIBLE CONTROLLER [0300]: NVIDIA Corporation C79 [GeForce 9400M] [10de:0863] (rev b1)
```
* Or to see which drivers are in use:
```
:$ lspci -k | grep -EA3 "3D|Display|VGA"
02:00.0 VGA compatible controler: NVIDIA Corporation C79 [GeForce 9400M] (rev b1)
        Subsystem: Apple Inc. MacBook5,1
        Kernel driver in use: nouveau
        Kernel modules: nouveau
```
* The `nvidia-detect` script can also be used to identify the GPU and the recommended driver package to install:
```
:# apt install nvidia-detect
:$ nvidia-detect
Detected NVIDIA GPUs:
02:00.0 VGA compatible controller [0300]: NVIDIA Corporation C79 [GeForce 9400M] [10de:0863] (rev b1)

Checking card:  NVIDIA Corporation C79 [GeForce 9400M] (rev b1)
Your card is only supported upo to the 340 legacy drivers series.
It is recommenmded to install the
    nvidia-legacy-340xx-driver
package.
```
* Before installing new drivers, you must obtain the proper kernel headers for the drivers to build with.
```
:# apt install linux-headers-amd64
```
* Update the list of available packages. Install the NVIDIA driver package:
```
:# apt install nvidia-legacy-340xx-driver
```
A warning message may appear while running the Package Configuration:
```
Conflicting nouveau kernel moduoe loaded
The free nouveau kernel module is currently ooaded and conflicts with the non-free nvidia kernel module.
The easisest way to fix this is to reboot the machine once the installation has finished.
<Ok>
```
* DKMS will build the `nvidia` module for your system, via the `nvidia-legacy-340xx-kernel-dkms` package. After, create an Xorg server configuration file by installing the `nvidia-xconfig` package. Then run it with `sudo`, It will automatically generate a Xorg configuration file at `/etc/X11/xorg.conf`.

For some reason, when running Debian (or some other Debian based distros like Ubuntu, ElementaryOS, etc.) with the `nvidia-340xx-driver`, this particular machine and old Macs with similar characteristics, experience an intermitent -and anoying- glitch/flickering at the bottom of the screen around 20px high, which is not present when using the open-source nouveau drivers (*Note:* It is quite curious that said glitch disapears when an external monitor is connected to the laptop).

In any case, there is only one solution that I've found works reasonably well. It requires editing the `xorg.conf` file that was just generated. We'll add a new `viewportout=1278x798+1+0` line under the `Screen` section of the file. This forces the Nvidia driver to switch into a meta-mode of 1278x798 instead of the 1280x800 the screen actually has (it basically eliminates 2 lines of pixels from your screen somehow eliminating the glitch).

* Edit the `/etc/X11/xorg.conf` file with `sudo`, add the "metamodes" option line and save. It should looke something like this:
```
Section "Screen"
    Identifier     "Screen0"
    Device         "Device0"
    Monitor        "Monitor0"
    DefaultDepth    24
    Option "metamodes" "nvidia-auto-select +0+0 { viewportout=1278x798+1+0 }"
    SubSection     "Display"
        Depth       24
    EndSubSection
EndSection
```
* Reboot your system to enable the nouveau blacklist as well as the changes on the Xorg configuration file.
```
:$ systemctl reboot
```
* After the system reboots, connect the external monitor, open the `xfce4-display-settings` and configure your setup. (i.e., HP 23", Resolution - 1920x1080, 60.0Hz, Rotation - Left or None, position (0,0)). Make sure to set the `Laptop` display as the Primay display. *Note:* You may want to deselect the `Configure new displays when connected` option.

**Important:** After some trial and error it appears that the best practice regarding switching between the laptop and the dual monitor is to connect all the cables before turning on the machine, this way all the settings are preserved. It is still possible to connect/disconnect the display, but you may need to reconfigure all your external monitor settings and position.

<br />

- [X] **External Monitor color correction with NVIDIA drivers**
The external monitor that I am using for the Dual setting has some color problems. The NVIDIA X driver dows not preserve the values set with nvidia-settings between runs of the Xserver (or even between logging in and logging out of X). This is intentional, because different users may have different preferences, thus these settings are stored on a per user basis in a configuration file stored in the user's home directory.

* Open `nvidia-settings`, go to *GPU 0 - (GeForce 9400M) -> DFP-2 (HP ZZ3i) -> Color Correction* and change the **Brighness** and **Gamma** for each individual Active Color Channel. The following values are the best that I have at the moment:
```
...
[DPY:DP-1]/RedBrightness=   -0.300000
[DPY:DP-1]/GreenBrightness= -0.200000
[DPY:DP-1]/BlueBrightness=   0.200000
[DPY:DP-1]/RedContrast=      0.000000
[DPY:DP-1]/GreenContrast=    0.000000
[DPY:DP-1]/BlueContrast=     0.000000
[DPY:DP-1]/RedGamma=         1.300000
[DPY:DP-1]/GreenGamma=       1.150000
[DPY:DP-1]/BlueGamma=        0.800000
...
```
* Quiting should create and record all this values on the `~/.nvidia-settings-rc` file. If it didn't, go into the `nvidia-settings` again, repeat the procedure and then go to to *nvidia-settings Configuration* and press the **Save Current Configuration** button.

* From now on, every time you turn on the computer with the dual monitor connected, you can run `nvidia-settings --load-config-only` or just `nvidia-settings -l` and the color correction settings for the external monitor should apply. You could create a script to run this command at the start of the computer.

<br />

- [x] **Screen Brightness with NVIDIA drivers**

**Important:** The following procedure is incomplete and untested.
* After installing the NVIDIA drivers, you won't be able to change the screen brightness. To fix this we need to edit the Xorg configuration file `/etc/X11/xorg.conf` again by adding the following line under the `Screen` section:

`Option         "RegistryDwords" "EnableBrightnessControl=1;"`

* Reboot again for the changes to take effect.

---

<br />

## TERMINAL STUFF

<br />

- [x] **Enable and Start Firewall**
```
:# apt install ufw
:# ufw default deny incoming
:# ufw default allow outgoing
:# ufw allow ssh
```
* If more ports need to be open (eg. port 80 for web server), use the comand `:# ufw allow` and then the *Port ##*. Finally, enable UFW (Uncomplicated FireWall).
```
:# ufw enable
:# ufw status
```

<br />

### Installing Apps

- [x] **GParted** 
```
:# apt install gparted
```

- [x] **GIT**
```
:# apt install git
```

- [x] **VLC**
```
:# apt install vlc
```

- [x] ~~**Firefox**~~
```
:# apt install firefox
```

- [x] **LaTeX**
* To avoid future problems with extra packages, install the full version of Tex Live. If other package installation problems should appear, this [thread](https://forums.linuxmint.com/viewtopic.php?t=300053) might help.
```
:# apt install texlive-full
:# apt install latexmk
```

- [x] **Skype for Linux**
```
:$ wget https://go.skype.com/skypeforlinux-64.deb
:# apt install ./skypeforlinux-64.deb
```
* Sometimes when trying to update with 'apt update' like:
```
W: Errore GPG: https://repo.skype.com/deb stable InRelease: Le seguenti firme non sono state verificate perché la chiave pubblica non è disponibile: NO_PUBKEY 1F3045A5DF7587C3
E: The repository 'https://repo.skype.com/deb stable InRelease' is not signed.
N: 
```
The problem is that after installing skype using its deb installer, it will add skype's repositories into your sources file, after adding a repository to your sources, you should add its public key too, however it seems that Skype did not add its public key to your system so you should add it manually:
```
wget -O - https://repo.skype.com/data/SKYPE-GPG-KEY | sudo apt-key add - 
```

---

<br />

## COSMETICS

<br />

- [x] **Install Themes and Icons**
* Open *Synaptic Package Manager* and search for **-theme**, **-icon** and **greeter**
* Select and install:
```
arc-theme
papirus-icon-theme
lightdm-gtk-greeter-settings
```
* Open the *Settings Manager*
* In *Appearance -> Style* select the to **Arc-Dark**
* In *Appearance -> Icons* select the to **Papirus-Dark**
* In *Window Manager -> Style* select the to **Arc-Dark**
* Also change the **Style, Icons, etc.** in the *LightDM GTL+ Greeter settings*
* Finally, consider adding the **Whisker Menu** on the *Panel Preferences -> Items*

<br />

- [x] **Advanced Themes and Icons**
* Most themes and icons can be downloaded from [Xfce-look.org](https://Xfce-look.org); Flat-Remix is a good choice.
* The basic details of the procedure are:
1. Download the archive.
2. Extract it with the right click of your mouse.
3. Create the **.icons** and **.themes** folders in your home directory. The fastest way to do that is by running in the terminal: `:$ mkdir ~/.icons ~/.themes`.
4. Move the extracted theme folders to the ~/.theme folder and the extracted icons to the ~/.icons folder. You can make the .theme and .icons folders visible by pressing Ctrl+H or in the menu of your file manager *View -> Show Hidden Files*.
* More detailed information about Themes and Icons can be found [here](https://averagelinuxuser.com/xfce-look-modern-and-beautiful/).

* If some of the icon themes show a Xfce icons gtk-update-icon-cache warning, run the following command in the terminal:
```
:$ gtk-update-icon-cache ~/.icons/yourIconsTheme
```

<br />

- [x] **Configure Greeter**
* Create user configuration file for **lightDM**, the greeter that asks for user and credentials, and edit it:
```
:# nano /usr/share/lightdm/lightdm.conf.d/01_my.conf
```
* Add the following lines to the file and save :floppy_disk: :
```
[SeatDefaults]
greeter-hide-users=false
```
* After rebooting, the user name will be prompted.

<br />

### KEYBOARD SHORTCUTS

<br />

Go to *Settings Manager -> Keyboard -> Application Shortcuts*.

- [x] **Terminal**
* `xfce4-terminal` runs with *Ctrl+Alt+T*

- [x] **Dropdown Terminal**
* `xfce4-terminal --drop-down` runs with *Super+T*

- [x] **Kill Frozen Applications**
* `xkill` runs with *Ctrl+Alt+Esc*

---

<br />

## FINISHING (OPTIONAL) TOUCHES

<br />

- [x] **Atom** :link: [atom.io](https://atom.io)
* To install Atom on Debian, Ubuntu, or related distributions, add our official package repository to your system by running the following commands:
```
:$ wget -qO - https://packagecloud.io/AtomEditor/atom/gpgkey | sudo apt-key add -
:# sh -c 'echo "deb [arch=amd64] https://packagecloud.io/AtomEditor/atom/any/ any main" > /etc/apt/sources.list.d/atom.list'
:# apt update
```
* You can now install Atom using `apt`:
```
# Install Atom
:# apt install atom
```
* Alternatively, you can download the Atom .deb package and install it directly:
```
# Install Atom
:# dpkg -i atom-amd64.deb
# Install Atom's dependencies if they are missing
:# apt -f install
```
* Some packages to consider installing would be:
  - [atom-updater-linux by andyrichardson](https://atom.io/packages/atom-updater-linux) - Checks automatically for updates when opening Atom.
  - [minimap by atom-minimap](https://atom.io/packages/minimap) - Displays a preview of the full source code.
  - [atom-beautify by Glavin001](https://atom.io/packages/atom-beautify) - To properly format your code.
  - [git-plus by Akonwi](https://atom.io/packages/git-plus) - Do git things without the terminal.
  - ~~[linter](https://atom.io/packages/linter) - Checks and cleans your code. Consider looking specific Linter packages for your language.~~
  - ~~[Todo](https://atom.io/packages/todo) - An in *ToDo* list inside Atom.~~
  - ~~[File Icons](https://atom.io/packages/file-icons) - Shows icons of files depending on their type.~~

<br />

- [x] **LaTeX for Atom** :link: [atom.io](https://atom.io)
* To compile LaTeX files from within Atom, install [`latex` by thomasjo](https://atom.io/packages/latex). Make sure *Enable SyncTeX* is selected and make `build` your *Output Directory* (You can also tick *Build on Save* and will do that every time you save).
* To display the generated PDF in Atom you need [`pdf-view` by izuzak](https://atom.io/packages/pdf-view), then make sure that *Auto reload on update* is enabled.
* For the Syntax highlighting and snippets, get [`language-latex` by area](https://atom.io/packages/language-latex).
* To autocomplete citations, environments and references, the best package I've found is [`latexer` by Focus](https://atom.io/packages/latexer).
* To show a document tree view for LaTeX, use [`latex-tree` by raphael-cch](https://atom.io/packages/latex-tree). Then go *Packages -> Latex Tree -> Toggle Tree View* to activate.
* To spell check your LaTeX file, use the Core Package `spell-check`. Just add `text.tex.latex` into the *Grammar Settings*.
* ~~For LaTeX function autocompletion, [`latex-autocomplete`](https://atom.io/packages/latex-autocomplete).~~
* ~~Apparently the only LaTeX sensitive wordcount package is Aerijo's [`latex-wordcount`](https://atom.io/packages/latex-wordcount). It's still incomplete, but it does provide a couple of counting techniques.~~
* ~~For an undistracted writing experience check out the [Typewriter](https://atom.io/themes/pen-paper-coffee-syntax) theme.~~

<br />

- [x] **Jupyter Notebook** :link: [jupyter](https://jupyter.readthedocs.io/en/latest/index.html)
* First make sure you have [pip](https://jupyter.readthedocs.io/en/latest/glossary.html#term-pip) for Python installed. If not, just run `:# apt install python3-pip` and then run the command `pip3 -V` to verify the installation.
* Then install the Jupyter Notebook using:
```
pip3 install jupyter
```
* Congratulations, you have installed Jupyter Notebook. To run the notebook:
```
jupyter notebook
```
* If you receive an error message such as `bash: jupyter: command not found` then it is possible that `~/.local/bin` is not on your path. Fix this by running `export PATH=$PATH:~/.local/bin` for your current session, then either log out and in or run `source ~/.bashrc`, then run `jupyter notebook` and it should open in your browser.

<br />

- [x] **Zotero** :link: [zotero.org](https://zotero.org/download/)
* Go to [zotero.org](https://zotero.org/download/) and `Download` the tarball file for Linux 64-bit.
* Extract the contents of the `Zotero-...x86_64.tar.bz2` tarball and move all the files into a new directory `/opt/zotero`.
* Run  the `/opt/zotero/set_launcher_icon` script from a terminal with `./set_launcher_icon` to update the .desktop file for that location.
* Symlink `zotero.desktop` for Zotero to appear in the launcher with `ln -s /opt/zotero/zotero.desktop /usr/share/applications/zotero.desktop`.
* Edit said file to point to the correct script:
```
:# nano /usr/share/applications/zotero.desktop
```
* Edit a few lines to the following and save :floppy_disk: :
```
[Desktop Entry]
Name=Zotero
Comment=Open-source Reference Management Tool (Stand Alone)
Exec=/opt/zotero/zotero
Icon=/opt/zotero/chrome/icons/default/default256.phg
Type=Application
Terminal=false
Categories=Office;
MimeType=text/plain
```
* After rebooting, the application should run from the menu.

---

<br />

## FINAL APP CLEANING

<br />

- [x] **Uninstall Apps**
* Uninstall non needed apps using the `sudo apt purge PROGRAM` command.

- [x] **Clean Up OS**
* After uninstalling, make sure there are no dependencies left by running the following commands:
```
:# apt autoremove -y
:# apt autoclean -y
```

<br />
:tada: THE END :tada:

<br />
<br />

---

<br />
<br />

## PERSONAL DE SETTINGS IN XFCE

Following are my personal settings for the DE in XFCE.

<br />

- [x] **Appearance**
* *Style* -> **Flat-Remix-GTK-Blue-Dark**
* *Icons* -> **Flat-Remix-Blue-Dark**

- [x] **Window Manager**
* *Style* -> **Flat-Remix-GTK-Blue-Dark**
* *Style | Button layout* -> Remove the **Minimize** button
* *Keyboard* -> `Tile window to the top` runs with **Super+Up**
* *Keyboard* -> `Tile window to the bottom` runs with **Super+Down**
* *Keyboard* -> `Tile window to the left` runs with **Super+Left**
* *Keyboard* -> `Tile window to the right` runs with **Super+Right**

- [X] **Window Manager Tweaks**
* *Compositor* -> Set **Opacity of windows during move:** to **~3/4** Opaque

- [x] **LightDM GTK+ Greeter settings**
* *Appearance* -> Select **Arc-Dark** for the Theme
* *Appearance* -> Select **Papirus-Dark** for the Icons
* *Appearance* -> Change the Default user image

- [x] **File Manager Preferences**
* *Display* -> View new folders using: **Detailed List View**

- [x] **Workspaces**
* *General* -> Number of wokspaces: **3**

- [x] **XFCE Terminal Preferences**
* *Drop-down* -> Set Height to **70 %**
* *Appearance* -> Set **Transparent background** to **0.70** Opacity
* *Appearance* -> **Uncheck** Display menubar in new windows
* *Appearance* -> Set **Default geometry:** to **120** columns **40** rows

- [x] **Desktop**
* *Background* -> **Uncheck** Apply to all workspaces
* *Background* -> Change the Folder: **path**
* *Icons* -> **Uncheck** Home on the Default Icons
* *Icons* -> **Uncheck** Filesystem on the Default Icons
* *Icons* -> **Uncheck** Trash on the Default Icons

- [X] **Power Manager**
* *Display | Blank after* -> **5 minutes** On battery
* *Display | Blank after* -> **30 minutes** Plugged in
* *Display | Put to sleep after* -> **10 minutes** On battery
* *Display | Put to sleep after* -> **45 minutes** Plugged in
* *Display | Switch off after* -> **20 minutes** On battery
* *Display | Switch off after* -> **Never** Plugged in

- [x] **Panel**
* **Detailed List View** the 2nd Panel
* *Items* -> The Items list is as follows:
  * **Whisker Menu**
    * *Appearance* -> **Uncheck** Show application descriptions
    * *Appearance* -> **Check** Show menu hierarchy
    * *Behavior* -> **Check** Position search entry next to panel button
    * *Behavior* -> **Check** Position categories next to panel button
    * *Commands* -> Run the `xfce4-taskmanager` command for the Edit Profile
  * **Separator** -> Transparent
  * **Separator** -> Separator
  * **Show Desktop**
  * **Launcher** -> File Manager
  * **Launcher** -> Firefox ESR
  * **Trash Applet**
  * **Window Buttons**
    * *Filtering* -> **Check** Show only minimized windows
  * **Separator** -> Transparent; Expand
  * **Separator** -> Separator
  * **Power Manager Plugin** -> Show Percentage
  * **Battery Monitor** -> **Check** only Display power
  * **PulseAudio Plugin**
  * **Notification Area**
    * *Appearance* -> Maximum icon size (px): **22**
    * *Appearance* -> **Uncheck** Show frame
  * **Clock**
* *Display* -> Output: **Primary**
* *Display* -> Make the Row Size (pixels): to **28**
* *Appearance* -> Set Alpha: to **0**
* **Move** Panel 1 to the bottom

- [x] **User Icon in Whisker Menu**
* I don't like the red silouwete icon for the user on Flat-Remix, so I will change it to the Debian logo.
```
mv ~/.icons/flat-Remix-Blue-Dark/apps/scalable/system-users.svg ~/.icons/flat-Remix-Blue-Dark/apps/scalable/system-users.svg.OLD
mv ~/.debian_setup/UserIcons/debian.svg ~/.icons/flat-Remix-Blue-Dark/apps/scalable/system-users.svg
```

<br />
:octocat:
