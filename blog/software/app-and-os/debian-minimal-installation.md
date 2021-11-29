# Debian: Minimal Installation (With and Without Full Disk Encryption)

I found myself with the prompt beyond my grasp when I was a total Linux noob who tried to install it the first time, something like "nonfree firmware needed". I swore off Debian that time. Anyway, Linus also said Debian is hard to install so I didn't feel very stupid back then (haha, we are in the same league!). A year later, I considerably upgraded my Linux suit and my penguin-shaped spectacles found the installation guide easy to read!

I went with the KDE installation the first time but I reinstalled with the base minimum quickly because I never used a desktop environment anyway. I had unsuccessful attempts to load my open-source Atheros firmware during the installation, since then I have always download an unofficial Debian image with non-free firmware. I will remove the non-free firmware later with the help of the vmrs package.

I use MBR instead of GPT because it is more supported by SeaBIOS payload on my coreboot system. GPT is recommended for UEFI based systems.

## With Encryption (Except /boot)

### GUI Installation

You can just select the guided partitioning with encrypted LVM option during the GUI installation. With this option, the default swap volume is only 1G so you might want to leave some space to extend this logical group later (via lvextend). Or you can do manual partitioning instead (if you dare!).

You can optionally skip system utilities (and all others) during the GUI installation. They are just some utility packages that Debian devs think an average user should have but you can always use apt to install them later.

### Comment Unwanted Lines from /etc/apt/sources.list

There is the first or the second line that you need to remove or else it will prevent you from installing anything. This looks something like

```
# deb cdrom:[Debian GNU/Linux 7.0.0 _Wheezy_ - Official amd64 CD Binary-1 20130$

deb cdrom:[Debian GNU/Linux 7.0.0 _Wheezy_ - Official amd64 CD Binary-1 20130$
```

### Configure Wireless Network

1. **ip a**. To determine the available network interfaces.
2. **i=your_interface**. Where <interface> is the one you choose from the step above. This will enable you to refer to the (likely long) interface name later on with just **$i**.
3. **ip link set $i up**. Set the interface up
4. **iw dev $i scan | grep SSID**. Find available wireless SSIDs.
5. **wpa_passphrase your_SSID your_password > /etc/wpa_supplicant/wpa_supplicant.conf**. To Supply SSID and password.
6. **wpa_supplicant -B -D wext -i $i -c /etc/wpa_suuplicant/wpa_supplicant.conf**. wext driver is old but still be widely used. You can try `nl80211` for new wireless cards.
7. **dhclient -v $i**. To resolve IP address. Try to toggle wireless on and off in the case of an error.
8. At this point, the status of your internet (using **ip a**) should include LOWER_UP. This means you can connect to the internet.
9. **apt install network-manager**. To make your life easier. You can then use nmcli and nmtui to connect to the internet.
10. **(Optional) Install open-source wireless driver like firmware-ath9k-htc and remove the proprietary iwlwifi, then reboot**. Reboot because the current firmware might still be in the memory.

`nmcli` should show your wireless interface status as either connected or disconnected. If it shows `unmanaged`, try to remove your interface from /etc/network/interface. If the status is `unavailable`, try to check if you connect your wireless adapter/card well enough.

### Add a User to Sudo Group

```
usermod -aG sudo <user>
apt install sudo
```

### (Optional) Install nix

Install `curl` and `rsync` using apt first before installing nix otherwise nix will complain and you have to start again.

### Once you have the internet, the rest is easy.

## With Full-Disk Encryption (on Libreboot)

I noticed a new Libreboot release (20210522) so I did a fresh Debian install to find out if LUKS2 now works (up to that time only LUKS1 worked). I just followed this [guide](https://libreboot.org/docs/gnulinux/encrypted_debian.html). **But LUKS2 was still not working**. The Libreboot doc said this new version (20210522) supports LUKS2 but it didn't seem to be the case to me. `cryptsetup luksDump /dev/sdX` revealed that the resulting Debian installation uses LUKS2 so I needed to downgrade to LUKS1.

```bash
cryptsetup luksConvertKey --pbkdf pbkdf2 /dev/sdX
cryptsetup convert --type luks1 /dev/sdX
```

After getting your barebone OS worked, the rest is the same as installing without full-disk encryption above.
