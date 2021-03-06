# pyhandscanner 

## About
pyhandscanner connects to serial hand-held barcode scanner and print the bar-code to stdout.

## History
I own a Metrologic MS9535 Bluetooth hand-held scanner, the scanner connects to a base-station 
via Bluetooth, the base station is then connected via USB to the computer. Recently the base-station
failed and the scanner was useless. I decided to connect the scanner directly to the computer via
Bluetooth and bypass the base-station. I wrote this script to read the output of the scanner and
display it to the focused windows (console, libreoffice, ...). This should also work with serial
scanners.

## Requirements 
- Bluetooth or serial scanner
- optional: Bluetooth stack (bluez)
- optional: Bluetooth dongle
- crikey software installed (used to display the bar-code to the focused windows)

## Install

### Ubuntu

Install crikey:

    sudo apt-get install libx11-dev x11proto-xext-dev libxt-dev libxtst-dev
    cd /usr/src
    sudo wget http://www.shallowsky.com/software/crikey/crikey-0.8.3.tar.gz
    sudo tar zxvf crikey-0.8.3.tar.gz
    cd crikey-0.8.3
    sudo make
    sudo make install

test crikey (should write "hello" in the console):

    crikey "hello" 

download pyhandscanner:

    git clone https://xens@github.com/xens/pyhandscanner.git

copy and make pyhandscanner executable:

    cd pyhandscanner
    cp pyhandscanner /usr/local/bin
    chmod +x /usr/local/bin/pyhandscanner
    
test:

    pyhandscanner -d /dev/yourSerialPort


### Bluetooth specific
Scan the Bluetooth devices and scan the available options for the device:


    xens@testvm:~$  hcitool scan
    Scanning ...
        00:0C:A7:00:90:6C	VoyagerBT 5604210356


    xens@testvm:~$ sdptool browse 00:0C:A7:00:90:6C
    Browsing 00:0C:A7:00:90:6C ...
    Service Name: Serial Port
    Service RecHandle: 0x10000
    Service Class ID List:
      "Serial Port" (0x1101)
    Protocol Descriptor List:
      "L2CAP" (0x0100)
      "RFCOMM" (0x0003)
        Channel: 1

So here it's the channel 1

edit /etc/bluetooth/rfcomm.conf and put the following (with your settings):

	rfcomm0 {
		bind yes;
		device 00:0C:A7:00:90:6C;
		channel	1;
		comment "Metrologic Voyager";
	}

restart the bluetooth services:

    /etc/init.d/bluetooth restart
    
rfcomm should return the following:
    
    rfcomm0: 00:0C:A7:00:90:6C channel 1 closed
    