//TODO: Divide into stages: musts, shoulds and wants 

Welcome to my system setup guide! 

I would like to preface this with the following: Im *not* a reputable source, I just like to cobble stuff together. After breaking my system one too many times, I decided to keep some sort of log of what im doing to it, and, you know, actually store keep track of my config files, so I decided to start writing this small summary.

I keep forgetting what everything does, so this may be useful to more people than just me.

# Partitions

### SSD (Sda label)

Partition madness!

Trying to keep some order and easy connection between systems, I have set several partitions with specific purposes. My SSD contains all of the OSs and some Swap space. Roughly half is taken by Windows, and the rest is unevenly split by Linux partitions (My main distro Ubuntu and a flavor of the month). Given that its considered a good principle to keep your SSD as empty as possible, only the OSs are stored on mine, and most data is kept on the HDD.

If attemping, its recommended to always install Windows first, then Linux. 

| Windows         | Ubuntu      | Manjaro   | Swap|
| ---|---|---|---|
|  a1/a2          | a6          | a7        | a5  |

### HDD (Sdb label)

My HDD contains my data and program files. Windows programs are installed here (whenever possible). 

* The "Data" partition is linked to Windows Libraries/Collections and set as default path to free up space in the SSD. 

* The "Programs" partition contains a copy of `Program Files` and `Program Files (x86)`. The installation path is set there whenever prompted.

* The "Games" partition serves a similar function but exclusively for games. Is set as the default adress for Steam data storage.

* The "Encrypted" is reserved for [Veracrypt](https://www.veracrypt.fr/en/Home.html), and can only be accessed through it. 

| Windows |     |       |Shared | Ubuntu | Manjaro |
| ----- | ----- | ----- | ----- | ----- | ----- |
|  b1   |bp     |bg     | b2    | b3    | b4    |
|  D:/ Data  | Programs | Games      | Encrypted     | /home        | /home  |

# Managing Dotfiles

Some packages we can install right away. apt --fix makes sure all dependencies are installed.

```
#all packages install
sudo apt install git vim fonts-liberation libappindicator3-1 curl

apt --fix-broken install

#To install .deb package
sudo dpkg -i DEB_PACKAGE
sudo dpkg –-remove skypeforlinux
```

## Before: Configuring git

Set up
```
  sudo apt install git
  git config --global user.email "e@gmail.com"
  git config --global user.name "Ema"
``` 

To avoid conflicts with Windows, set Linux time to local

```
timedatectl set-local-rtc 1 --adjust-system-clock
```

## Copying

Install dotfiles Using the (Atlassian)[https://www.atlassian.com/git/tutorials/dotfiles] method. To clone all files to a system:

```
# add 'config' alias to .bashrc/.zsh
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'


#Ignore self -> avoid recursion problems
echo ".cfg" >> .gitignore
# .cfg -> this repo
# .config -> actual config files

# clone my repo of course
git clone --bare https://github.com/smallAtlas/dotfiles $HOME/.cfg

# redefine alias
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'

# move conflict files to backup folder
mkdir -p .config-backup && \
config checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | \
xargs -I{} mv {} .config-backup/{}

# checkout! (download)
config checkout

# Dont track files on this repo
config config --local status.showUntrackedFiles no
```

## Adding

Use config as a git command

```
config status
config add .vimrc
config commit -m "Add vimrc"
config add .bashrc
config commit -m "Add bashrc"
config push
```

# MAIN TEXT EDITOR: Vim 

Surprisingly, Ubuntu doen't already include Vim. Lets fix that:

```
sudo apt install vim
```

Now lets make it useful

## You Complete Me

[YCM](https://github.com/ycm-core/YouCompleteMe)

Compiling YCM with semantic support for C-family languages through libclang. Ubuntu 16.04 and later:
```
sudo apt install build-essential cmake python3-dev
cd ~/.vim/bundle/YouCompleteMe
python3 install.py --clang-completer
```

## Add Vundle

Install vundle plugin manager for Vim 
[Vundle](https://github.com/VundleVim/Vundle.vim)

```
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
vim +PluginInstall +qall
```

# Matlab

```
sudo ./install
```

# SHELL: ZSH

Making the terminal a bit more useful using the ZSH shell

On Ubuntu:
```
sudo apt install zsh
chsh -s $(which zsh)
```

## [Oh My Zsh](https://ohmyz.sh/)

```
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

https://github.com/sindresorhus/pure

https://github.com/agnoster/agnoster-zsh-theme

https://github.com/powerline/fonts


## Powerline -> Powerlevel9k

You then need to select this theme in your ~/.zshrc:

ZSH_THEME="powerlevel9k/powerlevel9k"

Please note if you plan to set a POWERLEVEL9K_MODE to use a specific font, as described in this section of the Wiki, you must set the MODE before OMZ is loaded (look for source $ZSH/oh-my-zsh.sh in your ~/.zshrc).

# Fonts

A good system needs a selection of strong fonts, specially on a lowres screen like mine. 

Installing fonts in Linux is incredibly simple, just move all .ttf/.tta files to the .fonts dir in the user home and run:

```
sudo fc-cache
```
Some fonts have their own packages though

| Sans-Serif |
|:---|
| Roboto ```sudo apt-get install fonts-roboto```|
| Microsoft Fonts ``` sudo apt-get install ttf-mscorefonts-installer ``` A must have when using LibreOffice|
| [Fantasque Sans](https://github.com/belluzj/fantasque-sans)  |
| [Inconsolata](https://github.com/googlefonts/Inconsolata)|

| Serif |
|:---|
|[Dejavu](https://dejavu-fonts.github.io/) |
|Noto Fonts ```sudo apt-get install fonts-noto```|

| Terminal (Monospace) |
|:---|
|~~[Iosevka](https://typeof.net/Iosevka/)~~ (Skip in favour of nerdfonts version) A personal favorite of mine, set "Iosevka Term Regular". |
|[Bitmap fonts](https://github.com/Tecate/bitmap-fonts) A collection of bitmap fonts|
|[Nerdfonts](https://www.nerdfonts.com/) Patched fonts for terminal, has many common ones|
|[Powerline Fonts](https://github.com/powerline/fonts) Specially made for usage with Powerline|
|[Awesome Font](https://fontawesome.com/) Used widely by many applications|

| Japanese |
|:---|
| [M+ Fonts](https://mplus-fonts.osdn.jp/about-en.html) ```sudo apt-get install fonts-mplus```|
| [Adobe Source Hans](https://github.com/adobe-fonts/source-han-sans/tree/release)|


# i3-gaps

BOY
//TODO: Reorganize config
//Fix font?

[link](https://github.com/Airblader/i3)
[install](https://github.com/Airblader/i3/wiki/Installation)
Add external repo and install

```
#Ubuntu
sudo add-apt-repository ppa:kgilmer/speed-ricer
sudo apt-get update
sudo apt install i3-gaps-wm
```

```
sudo apt-get install i3-wm dunst i3lock i3status suckless-tools
```

DPI shenanigans
http://www.idc-online.com/technical_references/pdfs/electronic_engineering/Change_DPI_in_Ubuntu.pdf
112 DPI 1.16666


## i3-bar
//TODO transition to i3 blocks

Config on normal file
i3status

```
sudo apt-get install i3-wm dunst i3lock i3status suckless-tools
```


## Terminal: St

Very interesting approach to personalization. The config file IS the source code! Cahnge and straight up recompile the program.

```
#Download, unzip, and run to install
sudo make install
```

## dmenu -> rofi
Copy config file



# Software

## Visual Studio Code

https://code.visualstudio.com/
(Also add wal extension) 

## Veracrypt

Very important for my workflow. Access to all my compressed files 

```
sudo apt install exfat-fuse exfat-utils libexo-1-0
```
Don't forget to save mounted volume as favorite!

### Google Chrome

Makes syncing with phone so much easier. Should I switch to Chromium or Firefox?

[Chrome](https://www.google.com/chrome/)

## Spotify

For better hack-abiliy, add Spotify repository instead of installing through the store. 

[Spotify Download](https://www.spotify.com/es/download/linux/)

```
#Add repo
curl -sS https://download.spotify.com/debian/pubkey.gpg | sudo apt-key add - 
echo "deb http://repository.spotify.com stable non-free" | sudo tee /etc/apt/sources.list.d/spotify.list
#Install
sudo apt-get update && sudo apt-get install spotify-client
```

## Gnome tweak tool

How else are we applying those custom fonts and icon packs?

```
sudo apt install gnome-tweak-tool
```

## Papirus icons


[Papirus](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme/)

```
sudo add-apt-repository ppa:papirus/papirus
sudo apt-get update
sudo apt-get install papirus-icon-theme
```



# Going the extra mile



## Pip3

[Pip3](https://pip.pypa.io/en/stable/)

Pip3 python package installer. Dont use `sudo` ...

```
# Ubuntu
sudo apt install python3-pip
```


## Pywal


```
sudo apt install imagemagick
```

User install (No sudo)
```
pip3 install --user pywal

# Add local 'pip' to PATH:
# (In your .bashrc, .zshrc etc)
export PATH="${PATH}:${HOME}/.local/bin/"
```
copy walp.sh into .config/wal/

To execute
Add shortcut:
```
.config/wal/walp.sh 
```
On terminal
```
bash "/home/ema/.config/wal/walp.sh"
```

Add this line to your shell startup file. (.bashrc, .zshrc, .mkshrc etc.)
```
# Import colorscheme from 'wal' asynchronously
# &   # Run the process in the background.
# ( ) # Hide shell job control messages.
(cat ~/.cache/wal/sequences &)

# Alternative (blocks terminal for 0-3ms)
cat ~/.cache/wal/sequences

# To add support for TTYs this line can be optionally added.
source ~/.cache/wal/colors-tty.sh
```




## Mutt
ncmpcpp
powerline

## gcalcli

```
sudo apt-get install gcalcli
```
## ncmpcpp
https://addy-dclxvi.github.io/post/configuring-ncmpcpp/

sudo apt-get install mpd mpc ncmpcpp


# Software
Inkscape
Krita
Blender
Gimp
Chrome
Spotify

# Wine

https://wiki.winehq.org/Debian
https://wiki.debian.org/Wine

sudo dpkg --add-architecture i386 && sudo apt update


sudo dpkg --add-architecture i386
wget -nc https://dl.winehq.org/wine-builds/winehq.key
sudo apt-key add winehq.key

add to 
/etc/apt/sources.list:	deb https://dl.winehq.org/wine-builds/debian/ buster main

sudo apt install --install-recommends winehq-stable
sudo dpkg --add-architecture i386 && sudo apt update
sudo apt install \
      wine \
      wine32 \
      wine64 \
      libwine \
      libwine:i386 \
      fonts-wine


# Matlab

```
#To install, extract and:
xhost +SI:localuser:root
sudo bash install
#use default paths

#If we need reactivation
/usr/local/MATLAB/R20XXx/bin/activate_matlab.sh
```

# Windows

Do we even need this here?

[Nanite installer with my base apps](https://ninite.com/7zip-blender-chrome-foxit-gimp-googlebackupandsync-inkscape-libreoffice-notepadplusplus-skype-spotify-steam-vlc-vscode/)  
7zip Blender Chrome Foxit Reader Gimp GDrive Sync Inkscape Libreoffice Notepad++ Skype Spotify Steam VLC Player VS Code,

INSTALL STUFF

CHROME
[VERACRYPT](https://www.veracrypt.fr/en/Home.html)
[MATLAB](https://matlabacademy.mathworks.com/)
[FUSION360](https://www.autodesk.com/)
GITHUB DESKTOP




