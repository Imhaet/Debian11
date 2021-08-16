## FIRST THING FIRST

This installation list is based on my preferences while trying out Debian 11 (Bullseye) with KDE Plasma 5.20 on my - quite old - macbook and is not a definitive procedure. Feel free to use at your own peril.

<br />

### Settings and Partitions

:computer: Apple MacBook "Core 2 Duo" 2.0 13" (Unibody) Late 2008 Aluminum AKA MacBook5,1

Intel Core2 Duo P7350 @ 2x 1.995Ghz | amd64 EFI | 4GB RAM | NVIDIA GeForce 9400M @ 256MB

| Partition | File System | Mount     | Size              | Flags |
| ---       | ---         | ---       | ---               | ---   |
| sda1      | fat32       | /boot/efi | 142MiB            | boot  |
| sda3      | ext4        | /         | 141.28GiB         |       |
| sda2      | linuxswap   |           | 7.63GiB (7812MiB) | swap  |

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
* Add a "contrib" and "non-free" components to your existing repository line in `/etc/apt/sources.list` using nano, vi or other editor. Below is an example of said file in Debian 11.
```
# See https://wiki.debian.org/SourcesList for more information.
deb http://deb.debian.org/debian bullseye main contrib non-free
deb-src http://deb.debian.org/debian bullseye main contrib non-free

deb http://deb.debian.org/debian bullseye-updates main contrib non-free
deb-src http://deb.debian.org/debian bullseye-updates main contrib non-free

deb http://security.debian.org/debian-security/ bullseye/updates main
deb-src http://security.debian.org/debian-security/ bullseye/updates main
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

- [x] ~~**GIT**~~
* *Already installed*
```
:# apt install git
```

- [x] **VLC**
```
:# apt install vlc
```

- [x] ~~**Firefox**~~
* *Already installed*
```
:# apt install firefox
```

- [x] **LaTeX**
* To avoid future problems with extra packages, install the full version of Tex Live. If other package installation problems should appear, this [thread](https://forums.linuxmint.com/viewtopic.php?t=300053) might help.
```
:# apt install texlive-full
:# apt install latexmk
```

---

<br />

## COSMETICS

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

## PERSONAL DE SETTINGS IN KDE

Following are my personal settings for the DE in KDE.

<br />

- [X] **Appearance**
* *Global Theme* -> Select **Breeze Dark** for the Theme
* *Plasma Style* -> Select **Breeze Dark** for the Style
* *Icons* -> Select **Breeze Dark** for the Icons
* *Cursors* -> Select **Breeze** for the Cursors

- [X] **Workspace**
* *Workspace Behavior | General Behaviour* -> Click behavior: Select **Double-click to open files and folders**
* *Workspace Behavior | Screen Locking* -> Click behavior: Select **Double-click to open files and folders**


- [x] **Hardware > Input Devices > Touchpad**
* *Tapping:* -> **Check** Tap-to-click
* *Scrolling:* -> **Check** Invert scroll direction (Natural scrolling)


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
