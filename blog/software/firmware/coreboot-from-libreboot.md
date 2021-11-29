# coreboot: How to Flash It on Top of Libreboot Thinkpad X200.

coreboot and Libreboot are open-source replacements of BIOS and UEFI, a firmware to perform hardware initialization during the boot. They are not only generally faster than their closed-source counterparts, but also can mitigate or even remove some potential backdoors (like Intel ME) from the hardware.

I had Libreboot installed on my two laptops and it worked fine for my use case (Debian system) but then I wanted to get my hand dirty with blob-free coreboot. I heard that Libreboot doesn't support Windows but coreboot does so it should give me more flexibility. coreboot also gives a selection of payload out of the box, unlike Libreboot.

## coreboot vs Libreboot

- Libreboot is not a fork of coreboot, rather it is downstream of coreboot plus some other tools.
- coreboot supports more devices but mostly comes with some binary blobs whereas Libreboot strictly has no binary blob but supports fewer devices.
- coreboot doesn't provide binary release which means you have to compile yourself (have fun!) whereas Libreboot supplies both binary and source releases. I would recommend Libreboot binary release for a first-time flasher as it reduces risks and you can always convert to coreboot later. Obviously, check yout laptop model to see if it is Libreboot-compatible.

## How Did I Flash It?

On top of what I am going to write below, I found these resources useful.

- [https://operand.ca/2018/02/22/liberating-a-x200.html](https://operand.ca/2018/02/22/liberating-a-x200.html)
- [https://www.coreboot.org/Board:lenovo/x200](https://www.coreboot.org/Board:lenovo/x200)
- [https://www.coreboot.org/Build_HOWTO](https://www.coreboot.org/Build_HOWTO)
- [https://stafwag.github.io/blog/blog/2019/10/17/switch-libreboot-to-coreboot/](https://stafwag.github.io/blog/blog/2019/10/17/switch-libreboot-to-coreboot/)

### Preparation

You can git clone the coreboot source code from their website. While you can use other distros to compile the source, I would recommend you to use Debian as the official guide is based on it. First, install essential dependencies.

```bash
$ git clone https://review.coreboot.org/coreboot.git
$ apt-get install git build-essential gnat flex bison libncurses5-dev wget zlib1g-dev
```

It is worth checking the [Supported Motherboards](https://www.coreboot.org/Supported_Motherboards) page to see if your mainboard is known to work with a particular version of coreboot. If your board is listed and you want to try a known-working version, click the "upstream tree" link to find the commit hash and then check it out in the git repo you just cloned.

```bash
$ cd coreboot
$ git checkout <hash>
$ git submodule update --init --checkout
```

Note: the submodule update may fail if you fork from coreboot into your own repository. This happens because of the URL misinterpretation. Just change the URL from something like `../nvidia-cbootimage.git` into `https://review.coreboot.org/nvidia-cbootimage.git` in .gitmodules. You might also need to change the origin URL in your .git/config into `https://review.coreboot.org/coreboot.git`.

### Compilation

You need to compile the cross compiler. The compilation might take long time so I strongly recommend you to perform this step first, then proceed to the next step while it is compiling. If you are on AMD64, do this

```bash
$ make crossgcc-i386 CPUS=2
$ make crossgcc-x64 CPUS=2
```

### Config

Go to root of your repository and type

```bash
$ make menuconfig
```

I don't have much to say here because I don't really know what I am doing. coreboot menu has many selections of advanced stuff and I mainly just followed the aforementioned resources. Nevertheless, these are the important things I would like to mention.

- By default, coreboot comes with some blob but some machines like Thinkpad X200 support blob-free coreboot. You may need some tools like ich9gen to do so and change the size of the CBFS filesystem in ROM to 0x7fd000.
- There are a lot of payloads to choose from and they usually don't work well in all operating systems. I would recommend sticking with GRUB2 for most Linux users. You need to insert your own `grub.cfg` in the payload menu using `make menuconfig` or else you will get the minimal GRUB on start (have fun!). You may copy `grub.cfg` from the Libreboot project if you are not that familiar to GRUB.
- I had a permission issue with the internal flash. It turns out you need to boot with `iomem=relaxed` in the kernel command line. This can be done by appending this option to the `GRUB_CMDLINE_LINUX_DEFAULT` field in `/etc/default/grub` in my Debian system. Please don't forget to update grub before restarting, the command is generally `sudo update-grub`.
- I got `python not found` during the compilation, it turned out `which python` doesn't return anything on my Debian system. I needed to symlink it to python3 executable or installed the python-is-python3 package.
- You might want to read your current installed ROM two times and diff them (should have no difference) just for the simple check if things work correctly. I used `flashrom -p internal` command instead of `flashrom --programmer ch341a_spi` because I don't flash it externally.

### Check Your Chip

```bash
$ sudo flashrom -p internal:laptop=force_I_want_a_brick -c MX25L6405 -r old.rom
$ sudo flashrom -p internal:laptop=force_I_want_a_brick -c MX25L6405 -r old2.rom
$ diff old.rom old2.rom
```

### Specify MAC Address (Optional)

By default, coreboot generates roms with the same MAC address. This will clash with another PC in the same Ethernet network with the same MAC address. To avoid the clash, you have to temporarily set the MAC address to something else which is not convenient.

To avoid potential conflict, you can use `ich9gen` utility from the Libreboot project by searching "Libreboot ich9utils" on a search engine and following what they said. You should finally get the `ich9gen` executable. Select the generated binary for your model and add to ROM.

```bash
$ ./ich9gen --macaddress XX:XX:XX:XX:XX:XX
You selected to change the MAC address in the Gbe section. This has been done.
.
.
.
```

### Wait Until the Compilation Finished

After the compilation step above finishes, coreboot will generate the final ROM (I will rename it to x200.rom here). Combine the generated binary from the previous MAC address step with this ROM.

```bash
$ dd if=ich9fdgbe_8m.bin of=x200.rom bs=12k count=1 conv=notrunc
```

### Check the ROM with ifdtool

The ifdtool resides in your coreboot repo.

```bash
$ cd util/ifdtool
$ sudo make install
```

Use it to test the ROM. It should return something like this.

```bash
$ ifdtool -x my_x200.rom
File my_x200.rom is 8388608 bytes
Flash Region 0 (Flash Descriptor): 00000000 - 00000fff
Flash Region 1 (BIOS): 00003000 - 007fffff
Flash Region 2 (Intel ME): 00fff000 - 00000fff (unused)
Flash Region 3 (GbE): 00001000 - 00002fff
Flash Region 4 (Platform Data): 00fff000 - 00000fff (unused)
```

### Keep Fingers Crossed and Flash

```bash
$ sudo flashrom -p internal:boardmismatch=force,laptop=force_I_want_a_brick -c <your chip e.g. MX25L6405> -w my_x200.rom
```

## (Optional) Chainload SeaBIOS and GRUB
This will allow you to boot from more operating systems because you have both SeaBIOS and GRUB to choose from. I followed this [resource](https://github-wiki-see.page/m/librecore-org/librecore/wiki/Chainloading-SeaBIOS-from-a-GRUB-payload). I might create a post for this later.
