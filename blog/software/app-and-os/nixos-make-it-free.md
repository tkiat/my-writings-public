# NixOS: Make it Free (as in Freedom)

The default NixOS installation with a vanilla kernel includes a lot of non-free firmware, you can inspect these using `ls /run/current-system/firmware`. Fortunately, with some configurations, it can be made libre similar to [those](https://www.gnu.org/distros/free-distros.html).

This post is meant as a guide on how to liberate NixOS like this nice [post](https://pure-hack.com/posts/2017-09-25-libre-rate-your-nixos/) with some of my additional inputs. NixOS seems to lack the utility to detect non-free programs like `vrms` in Debian or `vrms-rpm` in Fedora so if I miss something please contact me.

Let's digress a bit. I actually prefer NixOS software distribution policy over FSF endorsed distributions mainly because it has Firefox. Unlike an operating system, Firefox is not that critical so I am fine with a restricted redistribution policy as long as it is open-source. Your need might be different though.

## Step-by-step Guide

1. Blacklist unfreeRedistributableFirmware
    ```nix
    hardware = {
      enableRedistributableFirmware = false;
    };
    ```
I couldn't rebuild with this setting. The culprit was `imports = [ (modulesPath + "/installer/scan/not-detected.nix") ];` in `/etc/nixos/hardware-configuration.nix`. This module contained `hardware.enableRedistributableFirmware = lib.mkDefault true`; that caused the conflict. I just commented it out and all the non-free firmware were gone.

1. Install linux-libre kernel. The linux-libre kernel is marked as broken here (update: now [unbroken](https://github.com/NixOS/nixpkgs/pull/141854) but still have to compile from source which takes like 3 hours for my X200).
    ```nix
    boot = {
      kernelPackages = pkgs.linuxPackages_latest-libre;
    };
    ```

1. Buy a libre-compatible wireless card like Atheros AR9271 or Ar9285 Ar5B95. [Some chips](https://wiki.debian.org/ath9k) don't even require the firmware to be loaded from the userspace i.e. it just works! If you need the firmware, put a (compiled) libre firmware to the system. Something like
    ```nix
    hardware = {
      firmware = [
        (
          pkgs.runCommand "open-ath9k-htc-firmware" { } ''
            mkdir -p $out/lib/firmware
            cp ${./firmware/htc_9271.fw} $out/lib/firmware/htc_9271.fw
          ''
        )
      ];
    };
    ```

1. Remove all non-free programs from the system. Unless you explicitly allow non-free programs, you won't have any non-free programs installed.
