# coreboot: How to Flash It on Top of libreboot Thinkpad X200.

coreboot and libreboot are open-source replacements of BIOS and UEFI, a firmware to perform hardware initialization during the boot. They are not only generally faster than their closed-source counterparts, but also can mitigate or even remove some potential backdoors (like Intel ME) from the hardware.

I had libreboot installed on my two laptops and it worked fine for my use case (Debian system) but then I wanted to get my hand dirty with blob-free coreboot. I heard that libreboot doesn't support Windows but coreboot does so it should give me more flexibility. coreboot also gives a selection of payload out of the box, unlike libreboot.

## coreboot vs libreboot

- libreboot is not a fork of coreboot, rather it is downstream of coreboot plus some other tools.
- coreboot supports more devices but mostly with some binary blob whereas libreboot strictly has no binary blob but it supports fewer devices.
- coreboot doesn't provide binary release which means you have to compile yourself (have fun!) whereas libreboot supplies both binary and source releases. I would recommend libreboot binary release for a first-time flasher as it reduces risks and you can always convert to coreboot later.
- Once you have libreboot installed, you can install coreboot without external flashing.

## How Did I Flash It?

I refer to these sources

- [https://operand.ca/2018/02/22/liberating-a-x200.html](https://operand.ca/2018/02/22/liberating-a-x200.html)
- [https://www.coreboot.org/Board:lenovo/x200](https://www.coreboot.org/Board:lenovo/x200)
- [https://www.coreboot.org/Build_HOWTO](https://www.coreboot.org/Build_HOWTO)
- [https://stafwag.github.io/blog/blog/2019/10/17/switch-libreboot-to-coreboot/](https://stafwag.github.io/blog/blog/2019/10/17/switch-libreboot-to-coreboot/)

I don't actually have much to say because I admit I don't really know what I am doing. coreboot menu has many selections of advanced stuff and I mainly just followed the guides. Nevertheless, these are the important things I would like to mention.

- By default, coreboot comes with some blob but some machines like Thinkpad X200 support blob-free coreboot. You may need some tools like ich9gen to do so and change the size of the CBFS filesystem in ROM to 0x7fd000.
- There are a lot of payloads to choose from and they are not created equal. They have different support for operating systems at the very least. For example, you need to modify GRUB2 to boot OpenBSD, unlike many Linux.
- I had a permission issue with the internal flash. It turns out you need to boot with `iomem=relaxed` in the kernel command line. This can be done by appending this option to the `GRUB_CMDLINE_LINUX_DEFAULT` field in `/etc/default/grub` in my Debian system. Please don't forget to update grub before restarting, the command is generally `sudo update-grub`.

```bash
GRUB_CMDLINE_LINUX_DEFAULT="whatever_your_default_value_is iomem=relaxed"
```

- I got `python not found` during the compilation, it turned out `which python` doesn't return anything on my Debian system. I needed to symlink it to python3 executable or installed the python-is-python3 package.
- You might want to read your current installed ROM two times and diff them (should have no difference) just for the simple check if things work correctly. I used `flashrom -p internal` command instead of `flashrom --programmer ch341a_spi` because I don't flash it externally.

```
sudo flashrom -p internal:laptop=force_I_want_a_brick -c MX25L6405 -r old.rom
sudo flashrom -p internal:laptop=force_I_want_a_brick -c MX25L6405 -r old2.rom
diff old.rom old2.rom
```

- Once you get the coreboot ROM compiled, use can use a tool like ifdtool (reside in your coreboot repo) to test the ROM.

```bash
$ ifdtool -x my_x200.rom
File my_x200.rom is 8388608 bytes
Flash Region 0 (Flash Descriptor): 00000000 - 00000fff
Flash Region 1 (BIOS): 00003000 - 007fffff
Flash Region 2 (Intel ME): 00fff000 - 00000fff (unused)
Flash Region 3 (GbE): 00001000 - 00002fff
Flash Region 4 (Platform Data): 00fff000 - 00000fff (unused)
```

- Finally, run

```bash
sudo flashrom -p internal:boardmismatch=force,laptop=force_I_want_a_brick -c MX25L6405 -w my_x200.rom
```
