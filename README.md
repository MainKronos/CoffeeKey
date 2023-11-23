# CoffeeKey

> [!Warning]\
> I am not promoting any illegal activity here.\
> This is purely for educational purpose.\
> I am not responsible if you use it for illegal purpose.

## Hardware

- PN532 Module
- USB UART Adapter (es. Unicorn TTL)
- Wires
- RFID Card (**Mifare Classic**)

### Setup

The following table shows how to properly connect the PN532 with the USB UART Adapter:

|PN532|USB UART Adapter|
|:--|:--|
|GND|GND|
|VCC|3V|
|TXD|RXD|
|RXD|TXD|

> [!Note]
> Pay attention on the TXD and RXD configurations. The USB Adapter has to receive (RXD) what the PN352 is transmitting (TXD) and so on.

## Software

### Setup

Install the required softwares:

```
sudo apt-get install libnfc-bin libnfc-examples libnfc-dev
```

Connect the USB Adapter on the computer and find the attached port:

```
dmesg

[119764.212841] hub 2-2:1.0: USB hub found
[119764.213666] hub 2-2:1.0: 15 ports detected
[119764.533554] usb 2-2.2: new full-speed USB device number 4 using uhci_hcd
[119764.654743] usb 2-2.2: New USB device found, idVendor=0403, idProduct=6001, bcdDevice= 6.00
[119764.654746] usb 2-2.2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[119764.654748] usb 2-2.2: Product: FT232R USB UART
[119764.654761] usb 2-2.2: Manufacturer: FTDI
[119764.654762] usb 2-2.2: SerialNumber: AI02Z9LU
[119764.756830] usbcore: registered new interface driver usbserial_generic
[119764.756857] usbserial: USB Serial support registered for generic
[119764.772234] usbcore: registered new interface driver ftdi_sio
[119764.772338] usbserial: USB Serial support registered for FTDI USB Serial Device
[119764.772528] ftdi_sio 2-2.2:1.0: FTDI USB Serial Device converter detected
[119764.772624] usb 2-2.2: Detected FT232RL
[119764.777646] usb 2-2.2: FTDI USB Serial Device converter now attached to ttyUSB0
```

The last line of the dmesg command shows that the USB is attached to **ttyUSB0**

Edit the NFC settings:

```
nano /etc/nfc/libnfc.conf
```

The file should looks like this:

```
# configuration using file or environment variable
#allow_autoscan = true

# Allow intrusive auto-detection (default: false)
# Warning: intrusive auto-detection can seriously disturb other devices
# This option is not recommended, user should prefer to add manually his device.
#allow_intrusive_scan = false

# Set log level (default: error)
# Valid log levels are (in order of verbosity): 0 (none), 1 (error), 2 (info), 3 (debug)
# Note: if you compiled with --enable-debug option, the default log level is "debug"
log_level = 2

# Manually set default device (no default)
# To set a default device, you must set both name and connstring for your device
# Note: if autoscan is enabled, default device will be the first device available in device list.
device.name = "PN532 board via UART"
device.connstring = "pn532_uart:/dev/ttyUSB0"
```

Testing the connection:

```
nfc-list
 
nfc-list uses libnfc 1.7.1
NFC device: pn532_uart:/dev/ttyUSB0 opened
```

To read a tag, issue the following command:

```
nfc-poll
```

And bring the tag closer to the PN532 module just for a moment. If everything is right then the following information will appear:

```
nfc-poll uses libnfc 1.7.1
NFC reader: pn532_uart:/dev/ttyUSB0 opened
NFC device will poll during 30000 ms (20 pollings of 300 ms for 5 modulations)
ISO/IEC 14443A (106 kbps) target:
    ATQA (SENS_RES): 00  04  
       UID (NFCID1): DE  AD  BE  EF  
      SAK (SEL_RES): 08  
nfc_initiator_target_is_present: Target Released
Waiting for card removing...done.
```

### Find the first key using **mfcuk**

Install the required softwares:

```
sudo apt install pkg-config automake build-essential
```

Download **this** mfcuk github repository:

```
git clone https://github.com/DrSchottky/mfcuk.git
```

Compile mfcuk with these commands:

```
libtoolize
aclocal
autoconf
autoheader
automake --add-missing
automake
./configure
make
```

Put card on reader and run (as root):

```
mfcuk -C -R 0:A -s 250 -S 250 -v 3 -w 6
```

This will start cracking the first key of the first sector. This may take some time (up to hours).

When finished, the program will print something like (key censored as `XXXXXXXXXXXX`):

```
INFO: block 3 recovered KEY: XXXXXXXXXXXX
 1 2 3 4 5 6 7 8 9 a b c d e f


ACTION RESULTS MATRIX AFTER RECOVER - UID YY YY YY YY - TYPE 0x08 (MC1K)
---------------------------------------------------------------------
Sector	|    Key A	|ACTS | RESL	|    Key B	|ACTS | RESL
---------------------------------------------------------------------
0	|  XXXXXXXXXXXX	| . R | . R	|  000000000000	| . . | . .
1	|  000000000000	| . . | . .	|  000000000000	| . . | . .
2	|  000000000000	| . . | . .	|  000000000000	| . . | . .
3	|  000000000000	| . . | . .	|  000000000000	| . . | . .
4	|  000000000000	| . . | . .	|  000000000000	| . . | . .
5	|  000000000000	| . . | . .	|  000000000000	| . . | . .
6	|  000000000000	| . . | . .	|  000000000000	| . . | . .
7	|  000000000000	| . . | . .	|  000000000000	| . . | . .
8	|  000000000000	| . . | . .	|  000000000000	| . . | . .
9	|  000000000000	| . . | . .	|  000000000000	| . . | . .
10	|  000000000000	| . . | . .	|  000000000000	| . . | . .
11	|  000000000000	| . . | . .	|  000000000000	| . . | . .
12	|  000000000000	| . . | . .	|  000000000000	| . . | . .
13	|  000000000000	| . . | . .	|  000000000000	| . . | . .
14	|  000000000000	| . . | . .	|  000000000000	| . . | . .
15	|  000000000000	| . . | . .	|  000000000000	| . . | . .
```

This key can now be used together with mfoc to crack the remaining keys.

### Find the remaining keys using mfoc

Install the required softwares:

```
sudo apt install mfoc
```

Run the following command replacing `XXXXXXXXXXXX` with the key you got from **mfcuk** above:

```
mfoc -O carddump.dmp -k XXXXXXXXXXXX
```

When finished, mfoc will dump the contents of your card both to the screen and to `carddump.dmp`.
