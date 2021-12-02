# coreboot: Customize GRUB Message

I can include my contact information in the footer of the GRUB menu like this.

```
If found, please contact john.doe@whatever.com or +6612345678
```

People who find my laptop can then return the laptop to me correctly without contacting any lost and found service. This message is embedded at the BIOS level so it needs some technical knowledge to overwrite it.

## How to Do It?

I am using Debian because the coreboot doc is based on that. You can fork GRUB repository and checkout at the same version as your coreboot config, in this case, 2.04.

```
git clone git://git.savannah.gnu.org/grub.git grub
cd grub/
git checkout grub-2.04
```

Adjust message as you want and store the diff file.

```
git diff > my-grub-conf
```

Now go to the coreboot repository. Select GRUB as payload and compile coreboot. coreboot will fetch the GRUB repository somewhere inside its root directory in the process. At the end of the compilation, move the diff file to the fetched GRUB folder and apply it.

```
git apply my-grub-conf
```

Compile coreboot again and, voila, flash and reboot the machine.
