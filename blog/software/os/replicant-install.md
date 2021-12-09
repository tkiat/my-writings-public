# Replicant: Install It on My Samsung Phones and Tablets

Replicant is a fully free mobile operating system based on LinageOS which is in turn based on Android. Replicant, being libre, supports a very few number of devices (currently around 10, mostly Samsung devices). You might also need a WiFi adapter with free (as in freedom) firmware. My unbranded AR9271 USBs with OTG cable work well but I find it very cumbersome to use unlike a smaller form factor from ThinkPenguin. A cellular internet works out of the box (at least for Samsung Note 2) though.

## Why did I Install Replicant?

I watched a [**Youtube video**](https://www.youtube.com/watch?v=FY7DtKMBxBw) about the iPhone12 anti-repair design where switching an identical component such as a camera or a logic board makes things not working at worst. This is not justifiable to me so I decided to sell my only Apple device: a 4-year-old Ipad Air 2. I sold on Kaidee.com for 3800 Baht and used that to buy Replicant-compatible devices: Samsung Tab 2 7.0 cellular (750 Baht), Samsung Tab 2 10.1 cellular (1290 Baht), and two Samsung Note 2 N7100 (800 and 1000 Baht each).

As a side note, the power button of my Samsung Galaxy Note 2 is broken but there is a jump-start technique. You charge it while it is turned off, then suddenly discharge and at the same time remove and insert the battery again quickly. A phone will automagically start without the power button!

I inspected the default operating system of my devices: Samsung flavor of stock Androids where useless Samsung apps crowded together on the screen. What really surprised me is the fact that Android 4.x doesn't support most modern applications! Replicant, on the other hand, allows you to install newer packages from F-Droid. Flashing a new ROM would void the warranty but the phone was so old so I didn't care about it.

## Install Replicant

The official manual was clear enough for me. First, you download (and verify) Replicant recovery image and then the operating system of the same version. The latest version might not be better than the previous one (they list the issues clearly so check that out). On Linux, you need a Heimdall package to flash ROM and also ADB to install the OS. I used this command when ADB complained about insufficient privilege.

```bash
$ adb kill-server && sudo adb start-server
```

Don't forget to backup EFS (modem data including IMEI). One thing also worth noting is a software renderer in Replicant: libagl vs llvmpipe. libagl was much faster but also lacks many functionalities (night light included in my case). llvmpipe was extremely slow (around 5 seconds delay for each click which was unusable to me). For details of how to switch them, visit [https://redmine.replicant.us/projects/replicant/wiki/Graphics](https://redmine.replicant.us/projects/replicant/wiki/Graphics).
