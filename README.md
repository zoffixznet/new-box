# new-box
Personal install steps to init a new box

##### Main

*Initial steps missing*

```bash
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y install build-essential git curl aptitude libssl-dev \
        wget htop zip sqlite3 time gimp dolphin gnome-terminal

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
```

#### Manual

- [Download Atom](https://atom.io/)
- [Download Chrome](https://www.google.ca/chrome/)

```bash
cd ~/Downloads
sudo apt install -fy ./atom* ./chrome*
```

#### Keys

###### System

- `Win+R` - Run Everything
- `Win+G` - Run Command: `gimp`
- `Shift+Alt+Z` - Run Command: `gnome-terminal`
- `Ctrl+Shift+F` - Window State: Maximize
- `Ctrl+Shift+Alt+F` - Window State: Maximize Horizontal
