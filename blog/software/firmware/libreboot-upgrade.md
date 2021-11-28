# Libreboot: How to Upgrade

I upgraded my Libreboot to a new release: 20210522. Once installed, it is easy to upgrade Libreboot. Use this to test if your system is ready

```bash
flashrom -p internal
```

## IF Not Yet Ready

It should output something like this if your system is not ready.

```
flashrom v1.2 on Linux 5.10.0-7-amd64 (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
Error accessing high tables, 0x100000 bytes at 0x00000000bfad6000
/dev/mem mmap failed: Operation not permitted
Failed getting access to coreboot high tables.
Error accessing DMI Table, 0x1000 bytes at 0x00000000bfaa0000
/dev/mem mmap failed: Operation not permitted
Segmentation fault
```

From https://flashrom.org/FAQ

```
On Intel systems, this is most often lpc_ich, so modprobe -r lpc_ich can help. A more invasive solution is to try again after rebooting with iomem=relaxed in the kernel command line.
```

So you can try a non-invasive solution first. For a more invasive solution,

1. Open this file `/etc/default/grub` in your favorite editor. This should apply to almost all Linux distributions.
1. Change from something like GRUB_CMDLINE_LINUX_DEFAULT="quiet" to GRUB_CMDLINE_LINUX_DEFAULT="iomem=relaxed quiet"
1. `sudo update grub && reboot`
1. After reboot, you might check for `iomem=relaxed` in /proc/cmdline. This file shows kernel parameters on start.

## If Ready

Many tutorials recommend reading two times and checking their diff, so I do the same

```
sudo flashrom -p internal:laptop=force_I_want_a_brick,boardmismatch=force -r dump1.bin -c "your chip name from step before"
echo "Do the same but this time dump2.bin"
diff dump1.bin dump2.bin
```

Now flash

```
sudo flashrom -p internal:laptop=force_I_want_a_brick,boardmismatch=force --ifd -i bios -c "your chip name on mainboard" -w "rom of your choice"
```

It should say `VERIFIED` if it succeeds. This Libreboot version `20210522` has no background image. This is an expected behavior.