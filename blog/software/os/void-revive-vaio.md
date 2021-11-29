# Void Linux: How I Revived My Old 2009 VAIO Laptop With It

I got Vaio VGN-CS16S when I was a university student (in late 2018) because it was one of the most beautiful laptops to me at that time! It came with Windows Vista which was horrendously slow but I didn't remove it because I was very proud of my `Microsoft Vista` logo. The boot time of more than one minute was a "feature" to me. It was a relatively inexpensive Vaio model (around 800$).

In 2012, I bought another cheap laptop to play games but I lost it in Berlin at the train station along with all the money, laptop, Samsung tablet, HDD, everything. That was a perfect opportunity to build my first custom desktop pc! I use it until I came back to Thailand only to find it that consumed energy more than I expected. I didn't like it so I began to search for an alternative.

This old Vaio is the only laptop I had, and I sometimes needed a portable advanced computing device, not just an iPad. I immediately remove Windows and replace it with Ubuntu18.04 with disappointment in performance. So I replaced it with Ubuntu14.04 but still experienced some problems up until a point where nothing worked after boot! I resorted to the desktop PC again.

## The First Installation - LXDE and XFCE

One day I didn't know what happen I decided to try this laptop again, this time with something more minimal. After researching for a while I came across Void Linux which is said to be a good independent distro. It doesn't use Systemd and is not a fork of any other OS. It's built with UNIX philosophy in mind: do one thing and do it well. Cool! I installed it on that day. I thought minimal install was too much of a hassle so I chose LXDE flavor.

I was surprised by the fast boot time but at the same time disappointed that the desktop UI had some bugs (no WiFi icon and a half-visible taskbar). It was probably because of my laptop model which couldn't wake up also. The minimal OS forced me to use CLI commands, and after some time I got hooked by the minimalism. I was surprised at first when I saw no Python and GCC installed out of the box. Not a big deal for me, I just use the excellent XBPS package manager to install them.

Without intensive applications, the performance was OK. I changed the desktop environment to XFCE that is a little more CPU-intensive but offered a lot more utilities. I began using many browser tabs and terminals. However, the computer hanged many times I thought it was because of RAM (no swap partition at the time). I installed psensor to measure performance and it convinced me so. Mind you my HDD was 11-year-old!

## The Second Installation - Go Minimal

I came across Luke Smith's Youtube video about DWM and that is my first impression of a window manager. I wanted a new clean minimalistic start so I downloaded a minimal ISO (around 400MB+). I also replaced my HDD to Intenso SSD, which was around 2.5mm less thick than the old HDD. It didn't trouble me much since the HDD touched the bottom (with the normal laptop orientation) but anyway I had unused rubber Roccat mousepad so I cut some part and padded it at the top of the HDD slot.

![vaio-pad-hdd.jpg](https://raw.githubusercontent.com/tkiat/my-writings-public/main/blog-data/image/vaio-pad_hdd.jpg)

<center>Unused Roccat Mousepad in HDD Slot</center>

The minimal distro forced me to learn about the concept of the display server. The installation took some hours because I wrote some Ansible on the fly to install every package there. Ansible is the automation program that makes the installation script reusable which is great. Now the laptop doesn't freeze any more thanks to the SSD. Now I feel much more freedom and become more peaceful. It makes me appreciate the once seemingly slow, chunky, and useless device. Thank you, Void Linux.

## The Third Installation - Try musl

Void Linux gives libc implementation choices: glibc and musl. It is said that musl is more minimal and faster so out of curiosity I tried musl. However, I don't understand the internals of any libc and also don't notice any difference in speed. The only thing I noticed was fewer packages are supported out of the box. The workaround is to choort to a folder and build a glic environment in it then launch the glibc app. I find that very inconvenient. I uninstalled it soon after trying.

## I Remove My Once Beloved Microsoft Vista Logo

I came across the [video where Richard Stallman removes the Microsoft logo from a laptop before signing his signature](https://www.youtube.com/watch?v=UdfY25gDjK8). That action was cool to me so I do the same. I then noticed a lot of color difference on the laptop surface! FYI, my laptop is white so it naturally turns yellow over time. I guess this is the only benefit of the logo lol.
