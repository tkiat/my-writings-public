# Btrfs Filesystem: Recover My Files from the Unbootable Disk

My Btrfs Guix System couldn't boot, and it happened the second time! I considered migrating to another hard disk but I would use a more boring filesystem like ext4 this time.

## Recover My Files

Common sense told me to use the first non-destructive method like `btrfs restore` before something dangerous like `btrfs check --repair`. I refer to the guide from [Btrfs wiki](https://btrfs.wiki.kernel.org/index.php/Restore).

```bash
btrfs restore /dev/sda1 /mnt/restore
```

This will back up all files from `/dev/sda1` to `/mnt/restore`. The problem was that my failed drive has 250GB whereas all of my other SSDs had 120-128GB which makes it impossible to transfer all the disk data. I found my 1TB hard disk but it would still take forever to transfer all the data. Luckily, `btrfs restore` offers a regex to filter the data transfer.

```bash
./btrfs restore /dev/sda1 /mnt/restore --path-regex '^/(|home(|/tkiat(|/Sync-Important(|/.\*))))$'
```

And it was fast! After that, I removed Guix System from the disk and installed Debian.
