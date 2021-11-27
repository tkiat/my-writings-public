# Lineageos: How Did I Install It on My Tab 2 P3100 And P5100 and My Take on It

Replicant 6.0 installed on both Tab 2 devices was very inconvenient to use because of disabled wireless firmware. I could connect the device with an OTG cable and my AR9271 wireless adapter but the connection and the ergonomic weren't that nice. So I kept them offline most of the time. This was a totally different experience than my Replicant Note 2 that allowed cellular data.

I decided to give both devices to my father afterward (updated: I sold them!). The RAM is only 1 GB but still sufficient to play simple games and Youtube videos. Replicant on a tablet is too strict for an average user who wants some Google services so I flashed them LineageOS ROMs.

## What is LineageOS? And How Does It Differ from Replicant?

LineageOS is an alternative operating system for Android. It is based on AOSP which is the most barebone version of Android. It removes Google apps and services from the AOSP. Basically, LineageOS is just AOSP minus GApps (Google Apps). This doesn't mean LinageOS doesn't send anything to Google (see [this](https://lwn.net/Articles/872639/)).

Replicant goes further by removing all non-free firmware from the LineageOS where it is based on. This can disable all functionalities like camera and wireless depending on the model.

## LineageOS + GApps vs Android

As I said earlier, LineageOS is AOSP without GApps. So why would someone want to install GApps on top of it? There are two strong advantages on top of my mind.

- **More Recent Android Versions**: My Tab 2 had only Android version 4.xx but now it becomes 7.1.2 thanks to LineageOS. This means I can download many more apps for my tablets.
- **No Bloatware**: Bloatware from, for example, Samsung devices are notorious enough someone would want to install LineageOS just because of it.

Some disadvantages

- **Have to Spend Time to Install**
- **No Official Support**
- **Security can be Worse**. Unmaintained devices can certainly become less secured. I wouldn't recommend anybody to do banking with it.

## My Installation Experience

### My Failed Attempt to Build from Source (You may skip)

The LineageOS project doesn't maintain Samsung Tab 2 anymore but they still leave some [manuals](https://wiki!lineageos.org/devices/espresso3g) including the build instruction. I, a Debian user, could follow it quite easily. I finished installing the required packages and running

```bash
repo init -u https://github.com/LineageOS/android.git -b cm-13.0
repo sync
```

I was impressed by the size of my repo (around 50GB). But when I needed to run

```bash
source build/envsetup.sh
breakfast espresso3g
```

I got the error saying that I couldn't find some espresso3g folders and, even worse, they were not in my project. I was lazy enough to ask somebody because anyway my father won't log in to these devices with his super-secret credentials anyway so I decided to download the custom ROM.

### Custom ROMs, Too Many Choices!

I first tried the custom ROM (nightly build 2018-01-21) from [lineageosroms](https://lineageosroms.com/espresso3g/). For bootloader, I downloaded the latest [TWRP](https://dl.twrp.me/espresso3g/). Unfortunately, the ROM flashing was usually not successful (with the error code 7). I searched online and it looked like I had to disable the device check (with assert and getprop commands) somewhere in META-INF/com/google/android/updater-script. That didn't work. Running the below command seemed to show the correct model (espresso3g) so the check would pass anyway.

```
adb devices
adb -s <my device id> shell getprop
```

I didn't know what to do then. Luckily, it turned out I flashed it successfully (the same method after some attempts). It could be launched perfectly. But I wanted to install GApps on top of it so I first downloaded the [Open GApps](https://opengapps.org/). I reflashed both the ROM and GApps after that before the first boot but it stuck at "Preparing Google...". Another attempt proved futile.

### Android-Andi Comes to the Recue!

I download [TWRP](http://andi34.github.io/recoveries_tab2.html) and the [ROMs](https://andi34.github.io/roms_tab2_aosp.html) from the Android-Andi [website](https://andi34.github.io/). Everything worked fine for both LineageOS version 13 and 14.1 and corresponding GApps! With these right ROMs, I just followed the official [instruction](https://wiki.lineageos.org/devices/espresso3g/install).

## My Take on LineageOS on Tab 2

It contains much non-free firmware. The firmware makes every component works that proves it excellent for a non-techie. The upgraded Android version can mitigate the planned obsolescence problem. I would rate 10/10 for the ROM!
