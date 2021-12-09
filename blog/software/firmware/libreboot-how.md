# Libreboot: How Did I Liberate My Thinkpad X200

Libreboot is a FOSS replacement for proprietary hardware initialization firmware like BIOS. It gets some code from coreboot, then deblobs it and bundles it with GRUB, flashrom, and others. Being blob-free, it supports a very few devices, mostly old Thinkpad laptops.

## Why Libreboot?

One great thing that Libreboot has to offer is the total elimination of Intel Management Engine. Intel ME is considered a backdoor by many. It has potential access to the network, the content of your display, etc. No computer user would like to have potential spyware in his/her computer right?

Currently, a fully-free system is impossible since at least there is blob firmware in SATA hard drive and many small ICs but I am already satisfied with Libreboot + a fully-free operating system. It is not 100% perfect but at least it is living proof of my preference to live with more freedom.

## General

You generally need an external flasher that connected directly to the BIOS chip on the mainboard except a few Thinkpad models such as X60. For Thinkpad X200, the official website has a how-to manual for Beaglebone Black only but a much cheaper flasher like CH341A is also possible. I use two guides: [the official one](https://libreboot.org/docs/hardware/x200.html) and [Wolfgang's Channel](https://www.youtube.com/watch?v=ktcvWkEVBE0). The latter uses CH341A so I mainly follow that and compare the PIN numbers with the official guide to double-check.

![libreboot-chip.jpg](https://raw.githubusercontent.com/tkiat/my-writings-public/main/blog-data/image/libreboot-chip.jpg)

<center>BIOS Chip on X200</center>

## My Pre-Flashing Guide

1. Find out if your laptop meets the requirements on the Libreboot website. The requirements are model numbers, display types, etc. To determine your display manually using Linux, get the display property by typing `xrandr --props`, then save the value from EDID field to `<filename>`, finally get the alphanumeric model number from `cat <filename> | edid-decode`. In my case it is LP121WX3-TLC1. Simple DuckDuckGo search shows that it utilizes TFT-LCD. Libreboot says LCD is compatible - voilà!
2. Update EC firmware. It handles tasks that OS is not responsible for such as battery handling. The easiest way is to use the Lenovo utility on Windows but you should make sure your computer has a secondary power source (battery or UPS) because if the power outage happens your laptop may not be booted again. There is currently no known way to update it from Libreboot.
3. Order a free (as in freedom) wireless adapter. I had a USB adapter AR9271 at home but since USB ports in a laptop are very limited in number I might consider buying an internal adapter. If you are unsure about which model to buy, I suggest that you take a look at a second-hand website and then search for Libreboot offer for your model and look at the description.
4. Order an external flashing device (if you need it). BeagleBone or Rasberry Pi is a better choice if you anyway want to do something else with it otherwise order just CH341A. Some CH341A has voltage mismatch (5 Volt instead of 3.3 Volt) so you might as well order a multimeter to check. I would recommend that you order two CH341A if you place an order from China due to long-distance shipping. Other options include Zerocat flashing but it is too expensive for me.
5. Order SOIC IC test clip. Your BIOS on the motherboard might support SOIC8 or SOIC16 so order the one you need. Note that SOIC16 also supports SOIC8 if placed correctly. Wolfgang's Channel video recommends cutting the wire connecting to SOIC clip and replaces it with shorter jumper wires. In that case, you also need female to female jumper wires and probably soldering iron also because when you cut the wire the lead at the tip is still there and it might be too big for a jumper wire.

![libreboot-mess.jpg](https://raw.githubusercontent.com/tkiat/my-writings-public/main/blog-data/image/libreboot-mess.jpg)

<center>A Mess</center>

| Warning: Flashing Libreboot on different devices results in the same MAC Address. This means two Libreboot machines cannot be on the same Ethernet network (you can change MAC address temporarily though). Look at the Libreboot website on how to avoid this. |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

## How Did I Flash It

1. Unscrew and connect everything (flasher, SOIC, wires). Then connect flasher to a laptop
2. Download flashrom. It is a utility to flash something to the ROM.
3. Back up BIOS two times and check if they are identical to make sure everything works. BIOS firmware is quite unique for each particular laptop model so it is wise to make a backup of it since maybe you want to sell a laptop to somebody else down the road and he wants to use Windows (only a free software traitor does that btw).
4. Clamp the SOIC chip at the BIOS chip on the mainboard. I had to clamp it again and again until the flashrom finally recognized the BIOS. In my case, it asks which Macronix flash chip model I have. It's the one on my BIOS which is MX25L6405D. Unfortunately, another X200 laptop of mine has very blurry text on the chip. However, with great effort, I could finally decipher it.
5. Download Libreboot and select a suitable image (8192 kB or something else, you know this number in the previous step).
6. Use flashrom utility to flash it.

```
$ sudo flashrom --programmer ch341a_spi -w x200_8mb_usqwerty_vesafb.rom -c MX25L6405
flashrom v1.2 on Linux 5.9.13-gnu (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
Found Macronix flash chip "MX25L6405" (8192 kB, SPI) on ch341a_spi.
Reading old flash chip contents... done.
Erasing and writing flash chip... FAILED at 0x00001000! Expected=0xff, Found=0x00, failed byte count from 0x00000000-0
x0000ffff: 0xdec
ERASE FAILED!
Reading current flash chip contents... done. Looking for another erase function.
Erase/write done.
Verifying flash... VERIFIED.
```

7.  Have fun. Libreboot doesn't work with all operating systems so check that out.

![libreboot-finish.jpg](https://raw.githubusercontent.com/tkiat/my-writings-public/main/blog-data/image/libreboot-finish.jpg)

<center>*Finished!*</center>
