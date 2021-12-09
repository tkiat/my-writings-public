# NixOS: Encrypt Everything Except Boot

## Why Not Full Disk Encryption (FDE)?

A non-FDE disk is considerably more prone to an [evil-maid attack](https://en.wikipedia.org/wiki/Evil_maid_attack) but I don't think I need it considering my threat model. FDE sometimes doesn't work with SeaBIOS. Of course, some might be able to make it work but I don't think the benefits of FDE outweigh its inconvenience. Your needs might be different.

I came across an excellent [guide](https://ethulhu.co.uk/nixos) that you can apply the same method to other distros as well. I rewrite that guide again for my own reference just in case it is gone.

## Steps

1. Format disk and make the boot partition

    For UEFI

    ```bash
    $ parted /dev/sda
    > mklabel gpt
    > mkpart ESP fat32 1MiB 512MiB
    > set 1 boot on
    > mkpart primary 512MiB 100%
    > quit
    ```

    For MBR

    ```bash
    $ parted /dev/sda
    > mklabel msdos
    > mkpart primary 1MiB 512MiB
    > set 1 boot on
    > mkpart primary 512MiB 100%
    > quit
    ```

1. Format LUKS

    ```bash
    $ cryptsetup luksFormat /dev/sda2
    $ cryptsetup open /dev/sda2 cryptroot
    ```

1. Create virtual partitions: root and swap

    ```bash
    $ pvcreate /dev/mapper/cryptroot
    $ vgcreate vg /dev/mapper/cryptroot
    $ lvcreate -L 4G -n swap vg
    $ lvcreate -l '100%FREE' -n root vg
    ```

1. Format boot partition

    For UEFI

    ```bash
    $ mkfs.fat -F 32 -n boot /dev/sda1
    ```

    For MBR

    ```bash
    $ mkfs.ext4 -L boot /dev/sda1
    ```

1. Format root and swap partitions

    ```bash
    $ mkfs.ext4 -L root /dev/vg/root
    $ mkswap -L swap /dev/vd/swap
    ```

1. Mount everything

    ```bash
    $ mount /dev/vg/root /mnt
    $ mkdir /mnt/boot
    $ mount /dev/sda1 /mnt/boot
    $ swapon /dev/vg/swap
    ```

1. Install NixOS. Once you boot the installer, generate `/mnt/etc/nixos/configuration.nix`

    ```bash
    $ nixos-generate-config --root /mnt
    ```

1. The UUIDs in `/mnt/etc/nixos/hardware-configuration.nix` should match your physical `/dev/sda1` and logical `/dev/vg/root` and `/dev/vg/swap`. Check those then specify UUID of `/dev/sda2` from this command `ls -l /dev/disk/by-uuid` in your `configuration.nix`

    ```nix
    boot.loader.grub.device = "/dev/sda";
    boot.initrd.luks.devices.root = {
      device = "/dev/disk/by-uuid/831e92d5-4f9e-4f62-9ec4-0a649ab64ec9";
      preLVM = true;  # decrypt before LVM mounts
    };
    ```

1. Complete your other configs in `configuration.nix` then install and reboot

    ```
    $ nixos-install
    $ reboot
    ```
