# Shell Script: dd Command is the Best Tool to Flash OS

```bash
dd if=whatever of=/dev/sdb bs=4M && sync
```

You cannot simply delete everything from the USB stick and copy the OS ISO image to it and expect it to automagically boot successfully. A boot sector needs to be properly created and that's why you need some kind of a flasher utility, which includes balenaEtcher, Rufus, and UNetbootin. Specialized flashers also exist like Fedora Media Writer for Fedora Linux distro.

But I always have a useful single-command utility at my disposal - dd. dd is similar to cp but it offers more features such as the ability to adjust write/read rate and the overall transfer size. It works every time in my experience so I don't need to download those media writer apps.

Then I wonder why people still use those apps? Some DuckDuckGo searches suggest that dd can erase a wrong partition if configured incorrectly. There is some truth in it but when I compare it to driving a car, the risk is very pale in comparison.

1. Car driving is real-time, unlike dd command that allows you to press enter at any time.
2. Car driving is riskier, you can kill somebody or even yourself in case of an accident.

By these logics, I, as a car driver, keep using dd command.
