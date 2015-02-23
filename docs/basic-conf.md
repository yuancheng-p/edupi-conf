# About this doc

This documentation shows the basic programs to install the a clean Rasbian
and the configuration to make the Raspberry as a Web application server.


## Objectifs

* Raspberry as hotspot.
* Basic web service (only an index page).
* Install Ka-lite (without downloading any video data).
* Install Kiwix (without downloading Gutenberg, Wikipedia, Wiktionary).


## Materials and environment nedded

* Raspbierry pi 2.
* Micro SD card with a new Rasbian installed.
* Wireless USB adapter.


## Use all the SD card space

You may notice that the system does not use all the SD card space.

To do so:

    $> sudo raspi-config

This program presents an interactive interface,
just choose to use all the SD card space and reboot the system.

Then use `df -h` to see the disk changing.



## Programs for better work

Vimers you know...

    $> sudo apt-get install vim

You can also use other editors, but the following document supposes vim
as default.

## Hostspot config

Well, this is a very long and difficult section.

1. Firstly let's install hostapd, an IEEE 802.11 AP management program,
and dnsmasq, a small DNS/DHCP server which we’ll use in this setup.
That's all we need for the configuration.

    $> sudo apt-get install hostapd dnsmasq

2. Let's modify the network interface config in `/etc/network/interfaces`.

You can notice there are already some configurations, just replace them
by the following:

```
auto lo
iface lo inet loopback
iface eth0 inet dhcp

auto wlan0
iface wlan0 inet manual
iface wlan0 inet static
    address 10.0.0.1
    netmask 255.255.255.0

#allow-hotplug wlan0
#iface wlan0 inet manual
#wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
#iface default inet dhcp
```

In fact the only changes is the wlan0 interface.
It makes wlan0 sticks to a static ip address at all boot time.


3. Create and edit file `/etc/hostapd/hostapd.conf.orig`:

```
interface=wlan0
ssid=FondationOrangeEdu_
hw_mode=g
channel=6
auth_algs=1
wmm_enabled=0
```

This is a basic hostapd config. The ssid will changes in different machines.


4. Start hostapd at boot up with a special SSID.

Edit `/etc/rc.local`, add the following code before `exit 0`:

```
echo "---hotspot config---"
MY_HOSTID=$(cat /proc/cpuinfo | grep Serial | cut -d: -f2 | sed 's/^[ \t]*//;s/[ \t]*$//')
MY_HOSTID=$(echo -e $MY_HOSTID | tail -c8)
sudo cat /etc/hostapd/hostapd.conf.orig | sed "s/FondationOrangeEdu_/FondationOrangeEdu_`echo $MY_HOSTID`/" > /etc/hostapd/hostapd.conf
sudo /usr/sbin/hostapd -B /etc/hostapd/hostapd.conf
```


5. You can see the SSID from other device if you reboot Raspberry now.
However, you can't connect to this AP because the DHCP service is not
activated by default, though `dnsmasq` runs by default :(

To do so, ...

Edit `/etc/dnsmasq.conf` and append at the end of the file:

```
address=/#/10.0.0.1
listen-address=10.0.0.1
dhcp-range=10.0.0.10,10.0.0.210,12h
```

Reboot the system again and try to connect to it with another device, it should
work now.


6. Enable packet forwarding for IPv4 in `/etc/sysctl.conf`
by uncommenting `net.ipv4.ip_forward=1`.


## Nginx

Install nginx

    $> sudo apt-get install nginx

Run it!

    $> sudo nginx

Test it by typing the IP adress of the Raspberry server on a PC's navigator,
You should then see a welcom message:

    Welcome to nginx!

### static page config

Create application directories:

    $> mkdir /home/pi/webapps
    $> mkdir /home/pi/webapps/www
    $> echo "Hello Raspberry" > /home/pi/webapps/www/index.html




