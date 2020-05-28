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

```
sudo service sshd restart
```

## Main

*Initial steps missing*

```bash
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y install build-essential git curl aptitude libssl-dev \
        wget htop zip sqlite3 time gimp dolphin gnome-terminal libcwd-guard-perl tree dnsutils

#
# Install Perls and load aliases
#
\curl -L https://install.perlbrew.pl | bash
git clone https://github.com/rakudo/rakudo/ ~/rakudo

echo 'source ~/perl5/perlbrew/etc/bashrc' >> ~/.bashrc
echo 'export PATH="$HOME/rakudo/install/bin:$HOME/rakudo/install/share/perl6/site/bin:$PATH"' >> ~/.bashrc
echo 'alias update-perl6='\''
    cd ~/rakudo && git checkout master && git pull &&
    git checkout $(git describe --abbrev=0 --tags) &&
    perl Configure.pl --gen-moar --gen-nqp --backends=moar &&
    make && make install'\''' >> ~/.bashrc
wget https://temp.perl6.party/.bash_aliases
echo 'source ~/.bash_aliases' >> ~/.bashrc
source ~/.bashrc

echo -e "set tabsize 4\nset tabstospaces" > ~/.nanorc

perlbrew install perl-5.30.2 --notest -Duseshrplib -Dusemultiplicity
perlbrew switch perl-5.30.2
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


## Trouble-Shooting

Glitches with virtual box not loading after reboot: likely a failed video driver. Uninstall all `virtualbox*` Bodhi's packages, then mount the Guest CD from VirtualBox's menus, `sudo mount /dev/cdrom /media/cdrom; cd /media/cdrom` and then run the guest installer from there.
