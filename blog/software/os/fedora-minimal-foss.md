# Fedora: Minimal and Libre Installation

I already have fully-free Debian without non-free firmware on top of my libreboot/blob-free coreboot machines. It aligns rightly with my philosophy: provide a fully free system out of the box and offer the way to use proprietary software if the user wants.

The thing is, I also want to try Fedora as it is one of the most popular distros out there. It has relatively recent packages yet stable enough for many people. The default Fedora system, even with the minimal installation, contains firmware blobs in the kernel. I am going to take the challenge of removing all the blobs out of the system.

## Minimal Installation

The two most noticeable Fedora installations are Workstation and Server editions but we want to go to the alternative download section. Both Fedora Server Netinstall and Everything have roughly the same size, but they differ.

```
“They are in fact not the same.
The server net-install has server defaults by default… so xfs filesystem is used, free space over 50g is not added to /home, but left as unpartitioned lvm space, etc.”
```
<center>[From Fedora Forum](https://ask.Fedoraproject.org/t/Fedora-netinstall-or-everything/5703/6)</center>


You should select the `Everything` variant and flash it.

```bash
$ sudo dd if=Fedora-everything.iso of=/dev/sdb status=progress && sync
```

In the installation GUI, select the minimal installation probably with the additional NetworkManager submodule and then proceed to the end. Fedora has NetworkManager installed by default but it lacks `nmtui` command from `NetworkManager-tui` package. You might not be able to connect to the internet the first time. If that is the case, either connect to the ethernet or download WiFi firmware. Once you have an internet connection, I recommend you to install `NetworkManager-tui` immediately.

## Detect Non-Free Software with vrms-rpm

A clone of vrms for RPM packages, it lists all non-free packages to stdout. You should expect to get something here because Fedora doesn't ship with Linux-libre kernel by default. There are many definitions of "free software" in [its list](https://github.com/suve/vrms-rpm) but I am not sure what is the option compiled to the dnf repository. You might want to compile it yourself if you are serious about the license.

## Switch to Linux-libre with freed-ora

As I said earlier, Fedora ships the kernel with firmware blobs and we want to remove it. There is the excellent [Freed-ora project](http://www.fsfla.org/ikiwiki/selibre/linux-libre/freed-ora.en.html) from FSFLA to switch to Linux-libre. You can just follow the procedure over there.

One thing to bear in mind is that you need to have free firmware before switching, otherwise you won't have WiFi connectivity. It turned out dnf doesn't have AR9271 firmware so I compiled myself from this [repository](https://github.com/qca/open-ath9k-htc-firmware) and then simply copied the resulting firmware (don't change filename) to `/lib/firmware`. Alternatively, I could download AR9271 firmware from the Debian repository (FOSS version) and convert it to an rpm package using the [alien](https://wiki.debian.org/Alien) package.

## Remove All Nonfree Packages

Run `vrms-rpm` again and remove everything shown. This includes the `Fedora-logos` package. The proprietary logo is fine to me and so I don't want to delete it.

## About the 3rd Party Package Managers

The `vrms-rpm` will complain only RPM packages. For something like flatpak, you have to check the license before. However, you can install RPM fusion free repo or nix (without enabling unfree option) and have peace of mind about the license. I prefer nix because of its reproducibility, I use something else if something doesn't work on nix e.g. kdenlive.
