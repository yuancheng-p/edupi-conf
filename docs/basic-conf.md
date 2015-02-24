# About this doc

This documentation shows the basic programs to install on the a clean Rasbian
and the basic configuration to make a Raspberry as a Web application server.


## Objectifs

* Raspberry as hotspot and web server.

## Materials and environment needed

* Raspbierry pi 2.
* Micro SD card with a new Rasbian installed.
* Wireless USB adapter.


## Use all the SD card space

We assume that you are connected to your Rasbperry with another
PC via SSH.

You may notice that the system does not use all the SD card space
by typing `df -h` on the CLI.

To expends the file system, simply:

    $> sudo raspi-config

This program presents an interactive interface.
Just choose to use all the SD card space and reboot the system.

Then use `df -h` to verify the changes.


## Hotspot config

Well, this is a very long and difficult section.

1. Firstly let's install hostapd, an IEEE 802.11 AP management program,
and dnsmasq, a small DNS/DHCP server which we’ll use in this setup.
That's all we need for the configuration.

    ```
    $> sudo apt-get install hostapd dnsmasq
    ```

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
    It makes wlan0 stick to a static ip address at all boot time.


3. Create and edit file `/etc/hostapd/hostapd.conf.orig`:

    ```
    interface=wlan0
    ssid=SSID_PREFIX_
    hw_mode=g
    channel=6
    auth_algs=1
    wmm_enabled=0
    ```

    This is a basic hostapd config to produce the real `hostapd.conf`.
    The ssid in `hostapd.conf` varies in different machines, according
    to their CPU info.


4. Now let's start hostapd at boot up with a machine specific SSID.

    Edit `/etc/rc.local`, add the following code before `exit 0`:

    ```
    echo "---hotspot config---"
    MY_HOSTID=$(cat /proc/cpuinfo | grep Serial | cut -d: -f2 | sed 's/^[ \t]*//;s/[ \t]*$//')
    MY_HOSTID=$(echo -e $MY_HOSTID | tail -c8)
    sudo cat /etc/hostapd/hostapd.conf.orig | sed "s/SSID_PREFIX_/SSID_PREFIX_`echo $MY_HOSTID`/" > /etc/hostapd/hostapd.conf
    sudo /usr/sbin/hostapd -B /etc/hostapd/hostapd.conf
    ```


5. You can see the SSID from other device if you reboot Raspberry now.
However, you can't connect to this AP because the DHCP service is not
activated by default, though `dnsmasq` runs by default :(
    To do so, ...
    Edit `/etc/dnsmasq.conf` and append to the end of the file:

    ```
    address=/#/10.0.0.1
    listen-address=10.0.0.1
    dhcp-range=10.0.0.10,10.0.0.210,12h
    ```

    Reboot the system again and try to connect to it with another device, it should
    work now.

6. Enable packet forwarding for IPv4 in `/etc/sysctl.conf`
by uncommenting `net.ipv4.ip_forward=1`.


Now your Raspberry is well configured as a hotspot and ready to serve web applications.
