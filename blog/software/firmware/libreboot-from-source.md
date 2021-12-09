# Libreboot: Build from Source

The official [instruction](https://libreboot.org/docs/build/) is only based on Debian-based distros. I can understand that it is hard for a maintainer to cater to a gazillion of Linux distributions. In theory, I can adapt the script to Fedora or something else but it is hard and error-prone to do so. That's why I recommend having Debian or at least a Debian-based distribution somewhere even though you don't use it. This guide is based on version `20210522`.

```
$ git clone <libreboot-source> && cd <dat folder>
$ git tag --list
20210522
$ git checkout 20210522
$ sudo make install-dependencies-ubuntu
```

Don't worry about the word Ubuntu here as it also works on Debian. As for the build, unless you open a Thinkpad shop (in which case, just type `make`) you should build only the model you need, something like

```
$ ./build boot roms x200_8mb
```

After finishing the build, you will get a lot of keymap and payloads. Read the [doc](https://libreboot.org/docs/install/#about-rom-image-file-names) for their difference.

```
$ sudo flashrom -p internal:boardmismatch=force,laptop=force_I_want_a_brick -c MX25L6405D -w my_x200.rom
```

You can switch out from tag and then clean everything.

```
$ git switch -
$ make clean
$ make crossgcc-clean
```
