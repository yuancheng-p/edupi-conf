# About this doc
This is a simple introduction for developer to install and configure
a rasbpian on a Raspberry pi 2.


## Materials to prepare

You should have at lease the materials in the following list:

* A Raspberry Pi 2
* A micro SD card
* A micro SD card reader
* A PC with Windows installed
* An Ethernet cable
* A LAN


## Install Official Raspbian

Step 1: Download and install Win32DiskImager on your PC.

Step 2: Then download the official raspbian that supports Raspberry pi 2.

Step 3: Insert your micro SD card inthe the SD card reader and insert into PC.

Step 4: Write the official raspbian image on the SD card with Win32DiskImager.
Since the official raspbian image is not large (about 3GB), this takes only
about 5 minutes for a 10MB/s write speed.

Step 5: Detach your micro SD card from PC and insert it into the Raspberry.
Connect the Raspberry to your LAN before plugging the power supply.
The Raspberry will then start automatically with Linux installed inside. :)


## Connect with SSH to the Raspberry

You need to know your Raspberry's IP first. For example, if you use livebox,
you can find it on the admin page of the local network.

Then connect to the server with SSH, it's enabled by default on Raspbian.
The default username and password:
 - username:pi
 - password:raspberry

Now you have a very clean Raspbian installed in the SD card and you can do any
further things as you want.
