# PocketCHIP-note
A space to collect all the useful notes and experiment about PocketCHIP.

![](https://i.ytimg.com/vi/vGZIwxBuppw/maxresdefault.jpg)

## Flash-CHIP
Simplyfies the Flashing Process for the C.H.I.P and PocketC.H.I.P Computer.
[https://github.com/Thore-Krug/Flash-CHIP](https://github.com/Thore-Krug/Flash-CHIP)


## Erasing and re-flashing the CHIP
You can erase and factory flash a CHIP from your computer using this repo. It will completely erase the CHIP, so make sure you have anything important backed up.
First you need to jumper the CHIP in FEL mode. You can do this by connecting the FEL pin to any GND pin.

## Pocket Chip Debian 10 Upgrade Guide
A tutorial to upgrade NXT pocket C.H.I.P to Debian Buster
[https://gist.github.com/luzhuomi/526fbcc30f3522f09eacf20d0f776fa5](https://gist.github.com/luzhuomi/526fbcc30f3522f09eacf20d0f776fa5)

## SSH
Install the software.

`chip@chip:~$ sudo apt install ssh openssh-server`

Edit /etc/ssh/sshd_config

`* Fix line to be : PermitRootLogin no`

Enable and start it

```
chip@chip:~$ sudo systemctl enable ssh
chip@chip:~$ sudo systemctl start ssh
```

To determine your CHIP's IP address

`chip@chip:~$ ip addr show wlan0`

## Application Launching Through SSH
`chip@chip:~$ DISPLAY=:0.0 application`


## Touchscreen Calibration
Install the packet to calibrate

`chip@chip:~$ sudo apt-get install xinput-calibrator`

then run it

```
chip@chip:~$ xinput_calibrator
Calibrating EVDEV driver for "1c25000.rtp" id=6
    current calibration values (from XInput): min_x=3965, max_x=112 and min_y=3776, max_y=227

Doing dynamic recalibration:
    Setting calibration data: 3992, 182, 3694, 276
    --> Making the calibration permanent <--
  copy the snippet below into '/etc/X11/xorg.conf.d/99-calibration.conf' (/usr/share/X11/xorg.conf.d/ in some distro's)
Section "InputClass"
    Identifier    "calibration"
    MatchProduct    "1c25000.rtp"
    Option    "Calibration"    "3992 182 3694 276"
    Option    "SwapAxes"    "0"
EndSection
chip@chip:~$
```
## Taking Screenshot
Install

`chip@chip:~$ sudo apt-get install gnome-screenshot`

then run 

`chip@chip:~$ gnome-screenshot --display=:0`

## Set passwords

Out of the box, both the "chip" and "root" accounts have a password of "chip". Given that most people will enable WIFI, this is a Bad Idea (tm). Best security practices is to change "chip" password, and *remove* the root password. You don't want to log in as root, you want to enable "sudo" for select user accounts. Fortunately, out of the box, the "chip" account is enabled for "sudo". If you have a *lot* of root work to do you can always enter "sudo bash".

```
passwd    # change "chip" account's password
sudo passwd -l root    # lock the root account from direct login
sudo sed -i.old /etc/ssh/sshd_config -e'/PermitRootLogin/s/yes/no/'    # configure sshd to not allow root
sudo service ssh restart
```

## Generate missing locales
```
 sudo apt install locales
 sudo locale-gen en_US en_US.UTF-8 
 sudo dpkg-reconfigure locales          
 sudo dpkg-reconfigure tzdata
```

## Blink

```
sudo wget -O/usr/local/bin/blink.sh http://fordsfords.github.io/blink/blink.sh
sudo chmod +x /usr/local/bin/blink.sh
sudo wget -O/etc/systemd/system/blink.service http://fordsfords.github.io/blink/blink.service
sudo systemctl enable /etc/systemd/system/blink.service
sudo service blink start
```

## Adafruit GPIO library

LED anode is connected through 4.7K to +3.3V; cathode connected to CSID7.

On CHIP:

```
sudo apt-get update
sudo apt-get install build-essential python-pip python-dev python-smbus git
git clone https://github.com/xtacocorex/Adafruit_Python_GPIO.git
cd Adafruit_Python_GPIO
sudo python setup.py install
cd ..
git clone git://github.com/xtacocorex/CHIP_IO.git
cd CHIP_IO
sudo python setup.py install
cd ..
sudo python
>>> import CHIP_IO.GPIO as G
>>> G.setup("CSID7", G.OUT)  # LED turns on, meaning output is 0
>>> G.output("CSID7", G.LOW)
>>> G.input("CSID7")
0
>>> G.output("CSID7", G.HIGH)  # LED turns off, meaning output is 1
>>> G.input("CSID7")
1
>>> G.setup("CSID7", G.IN)  # LED turns off; output is floating
>>> G.input("CSID7")
1
>>> G.output("CSID7", G.LOW)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
RuntimeError: The GPIO channel has not been setup() as an OUTPUT
>>> G.cleanup()
>>>
```

## Poor-man's dropbox

On Mac, generate private and public keys:

```
$ ssh-keygen -t rsa -b 2048
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/sford_itunes/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/sford_itunes/.ssh/id_rsa.
Your public key has been saved in /Users/sford_itunes/.ssh/id_rsa.pub.
```

On Mac, transfer public key to CHIP:

`scp id_rsa.pub chip@10.0.0.21: authorized_keys`

On CHIP, set up ssh:

```
mkdir .ssh  # might say already exists if you've used ssh on chip already
chmod 700 .ssh
mv authorized_keys .ssh/
chmod 600 .ssh/*

```
On Mac, test (should not prompt for password):

`ssh chip@10.0.0.21 ls`

On CHIP, get rsync (may already be installed, but doesn't hurt):

`sudo apt-get install rsync`

On CHIP get and build fswatch (this takes a while)

```
wget https://github.com/emcrisostomo/fswatch/releases/download/1.9.2/fswatch-1.9.2.tar.gz
tar xzf fswatch-1.9.2.tar.gz
cd fswatch-1.9.2
./configure CXX=g++
make
sudo make install
sudo ldconfig
```
## Utility on terminal

`sudo apt-get install rolo nrss nast ncdu tpp iptraf-ng btscanner cmus radio wyrd tudu tty-clock`

- **btscanner** - ncurses-based scanner for Bluetooth devices
- **cmus** - lightweight ncurses audio player
- **nast** - packet sniffer and lan analyzer
- **ncdu** - ncurses disk usage viewer
- **nrss** - A ncurses-based RSS reader
- **rolo** - text-based (vCard) contact management software
- **tpp** - text presentation program
- **tty** -clock - simple terminal clock
- **tudu** - Command line hierarchical ToDo list
- **worklog** - Keep Track of Time worked on Projects
- **wyrd** - text-based calendar application
- **radio** - ncurses-based radio application

### Games
- **nsnake** - classic snake game on the terminal
- **nudoku** - ncurses based sudoku games
- **ninvaders** - A space invaders-like game using ncurses

## Bluetooth
Install requirements

`sudo apt install pulseaudio pulseaudio-utils pulseaudio-module-bluetooth`

Run bluetoothctl and run the following in the prompt:

```
power on
agent on
scan on
```
Wait until you see your device show in the scan list and note its hardware address

In the bluetoothctl prompt run the following:

```
pair <hardware address>
	Hint: type the first few digits and then tab-completion works.
trust <hardware address>
connect <hardware address>
```
If everything worked correctly it should say *connected: yes*. After that your PocketChip should remember the device. You may need to run connect again, to reconnect if you turn the device off and on, but you won’t need to re-pair.

In the prompt run:

`paired-devices`

You should see the device in the list. To exit:

`quit`

Example session:

```
chip@chip:~$ bluetoothctl
[NEW] Controller A0:2C:34:24:52:43 chip [default]
[bluetooth]# power on
Changing power on succeeded
[bluetooth]# scan on
Discovery started
[CHG] Controller A0:2C:34:24:52:43 Discovering: yes
[NEW] Device 2B:9E:56:AA:B2:84 2B-9E-56-AA-B2-84
[NEW] Device D1:73:56:5C:14:C2 Eve
[NEW] Device 5A:D8:BF:0E:DE:58 MydeviceName
...
[bluetooth]# pair 5A:D8:BF:0E:DE:58
Attempting to pair 5A:D8:BF:0E:DE:58

```

## Surfing the Web
PocketCHIP ships with a simple webkit-based browser called Surf. It’s a good browser but skates under most User-Agent browser sniffing so you get served lots of unusable, horizontal scrolling websites.

`surf daverupert.com`

If you crave something with more dependable browser detection (you do), I recommend installing the Firefox ESR (formerly Iceweasel).

```
sudo apt install firefox-esr
echo "alias fx='firefox-esr'" >> ~/.bashrc
fx daverupert.com

```

## Checking the Battery Level
There’s two magic files in the os that reflect your current battery state, you can use `/usr/lib/pocketchip-batt/voltage` to check how charged your PocketChip is and you can use `/usr/lib/pocketchip-batt/charging` to check if it’s charging or not. These are mostly useful for scripts and such.

The voltage range is from `3600` to `4200` from what I’ve seen.


## Using UART
If you want to use the built-in UART you need to shutdown getty:

`sudo systemctl stop serial-getty@ttyS0.service`

You can then use `/dev/ttyS0` as you normally would as a terminal or programmer and stty to check the config `stty -F /dev/ttyS0` or change it `stty -F /dev/ttyUSB0 9600`.

---
### Other links
- [The C.H.I.P. is dead](https://www.jfpossibilities.com/imo/chip-dead/)
- [Welcome to The CHIP Operating System](http://chip.jfpossibilities.com/docs/chip.html)
- [Next Thing, Co. Web Archive](https://archive.org/details/NextThingCo.WebArchive)
- [PocketCHIP](https://www.schotty.com/General_Linux/PocketCHIP/)
- [CHIP Hackster.io projects](https://www.hackster.io/chip)
- [PocketCHIP Hackster.io projects](https://www.hackster.io/chip/products/pocket-c-h-i-p?sort=trending)
- [JWM is a very nice window manager](https://github.com/aleh/pocketchip-jwmrc)
- [CHIP AND POCKET CHIP](https://sybarite.us/category/makingstuff/chip-and-pocket-chip/)
- [CHIP Community](http://www.chip-community.org/)
- [Chip Reddit community](https://www.reddit.com/r/ChipCommunity/)
- [PocketCHIP Launcher (Marshmallow Edition)](https://github.com/o-marshmallow/PocketCHIP-pocket-home)
- [JF Possibilities' NTC GIT project mirror.](http://chip.jfpossibilities.com/gits/)

---
Sources

- [http://www.geeky-boy.com/w/CHIP_do_once.html](http://www.geeky-boy.com/w/CHIP_do_once.html)
- [https://kallanreed.com/2021/11/10/connecting-pocketchip-to-a-bluetooth-speaker/](https://kallanreed.com/2021/11/10/connecting-pocketchip-to-a-bluetooth-speaker/)
- [https://daverupert.com/2017/02/pocketchip/](https://daverupert.com/2017/02/pocketchip/)
- [https://martianwabbit.com/2020/10/24/pocketchip-in-2020.html](https://martianwabbit.com/2020/10/24/pocketchip-in-2020.html)

