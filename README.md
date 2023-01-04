# new-box
Personal install steps to init a new box

## Initial Setup

User:

```bash
adduser zoffix
usermod -aG sudo zoffix
su zoffix
ssh-keygen -t rsa -b 4096 -C "$(whoami)@$(hostname)"
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
# This is to speed up connect:
GSSAPIAuthentication yes
GSSAPIDelegateCredentials no
# This is to stop disconnects
ServerAliveInterval 60
ServerAliveCountMax 120
```

```bash
sudo service sshd restart
```

## Main

*Initial steps missing*

```bash
chsh -s /usr/bin/bash
chsh -s /usr/bin/bash zoffix # note: still need to edit .bashrc to remove the dumbass PROMPTALTERNATIVE two-line BS
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y install build-essential git curl aptitude libssl-dev \
        wget htop zip sqlite3 time gimp dolphin gnome-terminal libcwd-guard-perl \
        tree dnsutils sshfs gcc-multilib apache2 rsync nload rename libreoffice  \
        libpng-dev libgd-dev imagemagick pandoc texlive-latex-base texlive-fonts-recommended \
        texlive-extra-utils texlive-latex-extra gftp cifs-utils psmisc jq pv
        
# additional dev package
sudo apt-get -y install default-libmysqlclient-dev
#
# Install Perls and load aliases
#
\curl -L https://install.perlbrew.pl | bash
git clone https://github.com/rakudo/rakudo/ ~/rakudo
mkdir ~/bin/
echo 'source ~/perl5/perlbrew/etc/bashrc' >> ~/.bashrc
echo 'export PATH="$HOME/rakudo/install/bin:$HOME/rakudo/install/share/perl6/site/bin:$HOME/bin:$PATH"' >> ~/.bashrc
echo 'alias update-perl6='\''
    cd ~/rakudo && git checkout master && git pull &&
    git checkout $(git describe --abbrev=0 --tags) &&
    perl Configure.pl --gen-moar --gen-nqp --backends=moar &&
    make && make install'\''' >> ~/.bashrc
wget https://temp.zoffix.com/.bash_aliases
echo 'source ~/.bash_aliases' >> ~/.bashrc
source ~/.bashrc

echo -e "set tabsize 4\nset tabstospaces" > ~/.nanorc
sudo su # simple `sudo echo` doesn't work
echo -e "set tabsize 4\nset tabstospaces" > /root/.nanorc
exit

# Certbot auto
t
sudo apt install snapd
sudo snap install core
sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --apache

# Perl
perlbrew install perl-5.36.0 --notest -Duseshrplib -Dusemultiplicity
perlbrew switch perl-5.36.0
perlbrew install-cpanm
cpanm -vn Mojolicious IO::Socket::SSL Dist::Zilla::PluginBundle::Author::ZOFFIX Dist::Zilla::Plugin::Git::Contributors

update-perl6
git clone https://github.com/ugexe/zef /tmp/zef
perl6 -I/tmp/zef/ /tmp/zef/bin/zef install /tmp/zef/

zef install Inline::Perl5 WWW


# Basic bash terminal (no git branch). Generated using http://bashrcgenerator.com/
echo 'export PS1="\[\033[38;5;28m\][\T]\[$(tput sgr0)\] \[$(tput sgr0)\]\[\033[38;5;183m\]\u\[$(tput sgr0)\]\[\033[38;5;11m\]@\[$(tput sgr0)\]\[\033[38;5;70m\]\h\[$(tput sgr0)\]\[\033[38;5;7m\]:\[$(tput sgr0)\]\[\033[38;5;226m\]\w\[$(tput sgr0)\] \[$(tput sgr0)\]\[\033[38;5;198m\]\\$\[$(tput sgr0)\] \[$(tput sgr0)\]"' >> ~/.bashrc

# Create PAUSE file:
touch ~/.pause
chmod 600 ~/.pause
pico ~/.pause
# Enter:
user ZOFFIX
password *********************
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
- `Shift+Alt+Z` - Run Command: `gnome-terminal --full-screen`
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

#### Desktop Config

* Set `Shift+Alt+Z` for terminal in Settings->Keyboard Application Shortcuts
* Set `Super->Up/Down/Left/Right` for Window tiling Up/Down/Left/Right in Settings->Window Manager->Keyboard
* Set `Super+Ctrl->Left/Right` for Window tiling Up-Left/Up-Right in Settings->Window Manager->Keyboard
* Set `Super+Ctrl+Alt->Left/Right` for Window tiling Down-Left/Down-Right in Settings->Window Manager->Keyboard


## Trouble-Shooting

Glitches with virtual box not loading after reboot: likely a failed video driver. Uninstall all `virtualbox*` Bodhi's packages, then mount the Guest CD from VirtualBox's menus, `sudo mount /dev/cdrom /media/cdrom; cd /media/cdrom` and then run the guest installer from there.
