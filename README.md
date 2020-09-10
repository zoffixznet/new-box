# new-box
Personal install steps to init a new box

## Initial Setup

User:

```bash
adduser zoffix
usermod -aG sudo zoffix
su zoffix
ssh-keygen -t rsa -b 4096 -C 'Box Name'
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
cat > ~/.ssh/authorized_keys # paste keys
```

Hostname:

```bash
sudo pico /etc/hostname # enter hostname
sudo pico /etc/hosts    # add it to localhost address
```

Keyed SSH (disable passwords and root login):

```bash
sudo pico /etc/ssh/sshd_config
# Open above config file and set these options:
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
PermitRootLogin no
```

```bash
sudo service sshd restart
```

## Main

*Initial steps missing*

```bash
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y install build-essential git curl aptitude libssl-dev \
        wget htop zip sqlite3 time gimp dolphin gnome-terminal libcwd-guard-perl \
        tree dnsutils sshfs
#
# Install Perls and load aliases
#
\curl -L https://install.perlbrew.pl | bash
git clone https://github.com/rakudo/rakudo/ ~/rakudo
mkdir ~/bin/
echo 'source ~/perl5/perlbrew/etc/bashrc' >> ~/.bashrc
echo 'export PATH="$HOME/rakudo/install/bin:$HOME/rakudo/install/share/perl6/site/bin:$HOME:/bin:$PATH"' >> ~/.bashrc
echo 'alias update-perl6='\''
    cd ~/rakudo && git checkout master && git pull &&
    git checkout $(git describe --abbrev=0 --tags) &&
    perl Configure.pl --gen-moar --gen-nqp --backends=moar &&
    make && make install'\''' >> ~/.bashrc
wget https://temp.perl6.party/.bash_aliases
echo 'source ~/.bash_aliases' >> ~/.bashrc
source ~/.bashrc

echo -e "set tabsize 4\nset tabstospaces" > ~/.nanorc

perlbrew install perl-5.32.0 --notest -Duseshrplib -Dusemultiplicity
perlbrew switch perl-5.32.0
perlbrew install-cpanm

update-perl6
git clone https://github.com/ugexe/zef /tmp/zef
perl6 -I/tmp/zef/ /tmp/zef/bin/zef install /tmp/zef/

zef install Inline::Perl5 WWW
```

## Manual

- [Download Atom](https://atom.io/)
- [Download Chrome](https://www.google.ca/chrome/)

```bash
cd ~/Downloads
sudo apt install -fy ./atom* ./chrome*
```

## Keys

### System

- `Win+R` - Run Everything
- `Win+G` - Run Command: `gimp`
- `Shift+Alt+Z` - Run Command: `gnome-terminal`
- `Ctrl+Shift+F` - Window State: Maximize
- `Ctrl+Shift+Alt+F` - Window State: Maximize Horizontal

## Atom

- Install package `sync-settings` and setup the settings using values from other setups. Then go to "Packages→Synchronize Settings→Restore"

## Restricted `scp`-Only Accounts

```bash
sudo apt install rssh
# Edit /etc/rssh.conf to uncomment allowscp

sudo useradd -d /home/<RESTRICTED-USER> -m -s /usr/bin/rssh <RESTRICTED-USER>
sudo usermod -a -G <RESTRICTED-USER> zoffix

sudo su
cd /home/<RESTRICTED-USER>/

mkdir .ssh
ssh-keygen -t rsa -b 4096 -C 'XCite Live Host'
# Enter:
# /home/<RESTRICTED-USER>/.ssh/id_rsa

touch /home/<RESTRICTED-USER>/.ssh/authorized_keys
# Enter KEYS to authorized_keys
chown -R <RESTRICTED-USER>:<RESTRICTED-USER> /home/<RESTRICTED-USER>/.ssh
chmod -R 400 /home/<RESTRICTED-USER>
chmod 500 /home/<RESTRICTED-USER>
chmod 500 /home/<RESTRICTED-USER>/.ssh

mkdir <TARGET-SCP-DIR>
chmod 770 <TARGET-SCP-DIR>
chown <RESTRICTED-USER>:<RESTRICTED-USER> <TARGET-SCP-DIR>
```

## Kali Setup

For various tooling tips, see [Kali-Tips.md](Kali-Tips.md)

```bash
sudo apt-get install ufw python3-pip rlwrap

# Setup Impacket tools
cd ~/bin/
mkdir impacket
cd impacket
git clone https://github.com/SecureAuthCorp/impacket .
git checkout $(git describe --abbrev=0 --tags)
pip3 install .
chmod +x examples/*
sed -i "1s/\bpython\b/python3/" examples/*
echo 'export PATH="$HOME/bin/impacket/examples:$PATH"' >> ~/.bashrc

# Setup dirsearch tool
cd ~/bin
git clone https://github.com/maurosoria/dirsearch.git dirsearch
ln -s ~/bin/dirsearch/dirsearch.py ~/bin/dirsearch.py


# Setup pyrit (optional req for wifite)
sudo apt-get install python-dev libssl-dev zlib1g-dev libpcap-dev
sudo pip3 install psycopg2 scapy                
t
git clone https://github.com/JPaulMora/Pyrit.git .
python setup.py clean
python setup.py build
sudo python setup.py install

# Setup hcxdumptool (optional req for wifite)
sudo apt-get install libcurl4-openssl-dev libssl-dev
t
git clone https://github.com/ZerBea/hcxdumptool.git .
make
sudo make install

# Setup hcxpcaptool (optional req for wifite)
sudo apt-get install libcurl4-openssl-dev libssl-dev zlib1g-dev
t
git clone https://github.com/ZerBea/hcxtools.git .
make
sudo make install

# Only needed for the above if wifite is still looking for non-"ng" hcxpcaptool
sudo ln -s /usr/local/bin/hcxpcapngtool /usr/local/bin/hcxpcaptool


# Wordlist
mkdir ~/wargaming/
cd ~/wargaming/
cp cp /usr/share/wordlists/rockyou.txt.gz .
gunzip rockyou.txt.gz

# Setup QTerminal theme color changes for executables
sudo cp /usr/share/qtermwidget5/color-schemes/Kali-Dark.colorscheme /usr/share/qtermwidget5/color-schemes/Zoffix-Dark.colorscheme
sudo sed -i '/\[Color2Intense\]/!b;n;cColor=255,242,0' /usr/share/qtermwidget5/color-schemes/Zoffix-Dark.colorscheme
# Open QTerminal and tell it to use Zoffix-Dark Theme
```

#### Desktop Config

* Set `Shift+Alt+Z` for terminal in Settings->Keyboard Application Shortcuts
* Set `Super->Up/Down/Left/Right` for Window tiling Up/Down/Left/Right in Settings->Window Manager->Keyboard
* Set `Super+Ctrl->Left/Right` for Window tiling Up-Left/Up-Right in Settings->Window Manager->Keyboard
* Set `Super+Ctrl+Alt->Left/Right` for Window tiling Down-Left/Down-Right in Settings->Window Manager->Keyboard


## Trouble-Shooting

Glitches with virtual box not loading after reboot: likely a failed video driver. Uninstall all `virtualbox*` Bodhi's packages, then mount the Guest CD from VirtualBox's menus, `sudo mount /dev/cdrom /media/cdrom; cd /media/cdrom` and then run the guest installer from there.
