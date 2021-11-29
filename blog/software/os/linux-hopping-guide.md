# Linux Distro Hopping: My Journey and Guides

Anyone can fork Linux to make their own distribution because Linux is equipped with GPLv2 license. As a result, people sometimes suffer from [the paradox of choice](https://en.wikipedia.org/wiki/The_Paradox_of_Choice) because many Linux distributions exist. I hope this post can alleviate that.

## My Journey

I tried to install Ubuntu first time when I studied Master's degree but, being oblivious to the beauty of Linux, I kept using window. I installed it again when I learnt how to code. It crashed at times and I didn't know why so I hopped to Manjaro XFCE which worked really well. I later installed OpenSUSE and Arch Linux. The arch installation was broken and OpenSUSE was to me the same as Manjaro. I still had no ideas about how to choose the Linux distribution that time.

I suddenly got a blessing from the tech god when I decided to install Void Linux on the 11-year old low-end Vaio laptop. I couldn't imagine before that I could do anything with it. I could code and watched Youtube! What more do you want? I decided to try FreeBSD and deleted it after unable to find reasons why it is better than Linux (for my use case). I installed Arch Linux and use it alongside Void Linux. I only noticed crashes from Arch, not Void.

I got inspired by reading a [RMS](https://en.wikipedia.org/wiki/Richard_Stallman) post after spyware stuff to ditch my PCs and order two Thinkpad X200 to install Libreboot. I then try one of the FSF-endorsed distros: Guix System. The installation was successful with sweat and the help of the Atheros AR9271 WiFi adapter. I used that for around 8 months before deciding to settle with Debian. Reasons:

- **I prefer Debian kernel over Linux libre**. They are essentially free software (as per FSF 4 freedoms) but the Debian one allows loading of non-free modules, which offers flexibility while still adhere to freedom. I don't use non-free modules of course but I still value flexibility.
- **Guix System, a non-conventional distro, doesn't play well with some packages**. It is built around functional package managers, known as Guix. In my opinion, this is a superior way to handle packages such that no breakage can occur but that also makes AppImage and "make install" not working out of the box. Guix doesn't rely on Linux FHS and many packages that assume that simply doesn't work.
- **Absence of some packages**. The distro has a lot of packages but still less in comparison to its brother (NixOS). I wanted to use yarn, VSCodium, and Firefox Developer Edition also.

## My Guides

- The best way to hop distros, in my opinion, is to select a handful of distros with distinct philosophies. Ubuntu and Manjaro can offer you more convenience while Arch and Gentoo prioritize customizability. NixOS and Guix have different package management paradigms. Fedora and OpenSuse are corporate Linux so they are usually bleeding-edge and have available support. Alpine Linux has good support for embedded devices. Trisquel is a fully free (as in freedom) distro.
- The easiest to try a distro is to spin it up in a virtual machine. This is particularly useful if you try to run FSF-endorsed distros and you don't have a single free WiFi adapter.
- Popular distributions like Ubuntu and Manjaro (except Arch Linux) are worth trying first if you are a newcomer. This doesn't mean they are not capable of complex stuff because all Linux distros are just Linux kernel + desktop + packages so it is almost as hard to become an absolute expert at each of them. They are beginner-friendly because most people use them so tutorials and help are readily available.
- There is not much point in distro-hopping Ubuntu, Kbuntu, Xbuntu, Mint, and Elementary OS because they are all based on Ubuntu with some minor differences like desktop environments. Instead, install many desktop environments and windows managers on the same distribution. You can switch between each environment manually or using a graphical login manager.
- Few other things to look for in a distro are supported filesystems, libc, and kernel.
- You can try helping maintainers update packages as well. This way, you will get a general impression of how the distro functions.
