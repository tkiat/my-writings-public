# coreboot: Boot from Minimal GRUB

Out of my ignorance, I selected GRUB payload in `make menuconfig` in the coreboot source directory without `grub.cfg`. This **just** built minimal GRUB payload with only minimal BASH-like CLI interface instead of a nice-looking boot menu. I was shocked at the time because GRUB was installed in the BIOS chip and there was no way to bypass it. If GRUB is installed in the disk, I could at least boot from another disk and chroot to this disk to fix the problem.

So, I had only two options: 1. flash again externally 2. try to boot from minimal CLI. The second option was way simpler and more adventurous as I hadn't done that before so I tried that option first :). I was not familiar with GRUB at all but fortunately I once followed the Libreboot Debian installation manual. It involves GRUB CLI a bit and does not directly relate to my problem but I could make use of it. This post describes how I boot from GRUB minimal.

Note: I tried to boot from Debian LUKS with LVM. It was encrypted without /boot.

1. Enter the classic `ls` command. Set root to the one containing both the kernel and initrd. They are required for boot.
```bash
grub> ls
(ahci0,msdos5)
grub> ls (ahci0,msdos5)
...
grub> set root=(ahci0,msdos5)
```

1. Specify the version of both kernel and initrd.
```bash
grub> linux /vmlinuz-<version> root=/dev/mapper/debian--vg-root iomem=relaxed
grub> initrd /initramfs-<same version as above>
```
The argument to `root` is your LVM root partition. If you don't know what it is, you can just omit that argument first and boot it. It will prompt BusyBox initramfs, from that type `vgchange -ay` to expose all volumes then type `/dev/mapper/`  then tab to get the root volume name. Then supply that argument with that name in the next boot.

1. Flash another ROM, this time you may add your own `grub.cfg` before building the new one. If you don't know any idea what it should be, you can copy from the one from the Libreboot project. The `iomem=relaxed` in the previous step allows you to flash another ROM (goodbye minimal GRUB!).

1. Done!
