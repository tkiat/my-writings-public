# dwm: A Minimal Window Manager

dwm is a window manager for X window system developed by suckless.org. After the first try, I never look back to a desktop environment again. Seriously, all you need are a simple terminal and probably a simple search bar (I am using dmenu) to open an installed app. You can flexibly add or remove the default 9 tabs provided which is very powerful and liberating. I would also like to add that this window manager is very lightweight with only around 2500 lines of code! (using cloc utility).

dwm makes me doubt the need for a second monitor at all. I can imagine if you do jobs like animations you probably want two monitors because you might want to compare the flow and the result in real-time but I don't think I will ever need 2nd monitor. The switch from one window to another window is very easy. Other configs, for example, colors, time, etc. can be entirely configured and be shared with other people. This is a prime benefit of Free and Open Source Software.

dwm also makes me doubt the need for the pointing device like a mouse at all. The application is sized and located automatically upon open, unlike many desktop environments that require the need of a mouse dragging the application to a particular size and position.

## Tips

- The main config file is config.h. This is hidden until you compile the program the first time so compile it first then configure values
- dmenu is a very nice utility originally built for dwm. With a combination of keystrokes, the search bar appears and you can run your application from there easily
- You can find plenty of extensions on suckless's website. They are simply diff files that you can apply on your forked project
- You can assign each application to be opened by default on a specific tab in config.h but to do that you need to find a name recognized by dwm first. You install xprop package, run it, then click on the window of the opening app. xprop will output a bunch of strings, the second value in the field `WM_CLASS(STRING)` is the name you want
- If the st terminal doesn't fit the screen, just set `static const int resizehints = 0;`
- It also works in FreeBSD but you need to change the location of `X11INC`, `X11LIB`, and `FREETYPEINC` variables in config.mk. Here is my config

```Makefile
ifeq "$(shell uname)" "FreeBSD"
X11INC = /usr/local/include
X11LIB = /usr/local/lib
FREETYPEINC = ${X11INC}/freetype2
else
X11INC = /usr/X11R6/include
X11LIB = /usr/X11R6/lib
FREETYPEINC = /usr/include/freetype2
endif
```

- You can change the top-right text using xsetroot. Some common choice are the battery level, current RAM usage, and the current time. At first, I did something like this

```bash
while true; do
  xsetroot -name " $(date +'%a, %d %b %I:%M:%S%p') | This is my dwm "
  sleep 1
done &
```

The problem is that the loop still keeps running after I log out, this can lead to performance issues and display inconsistency. The code below exits while loop at the end of an xorg session.

```bash
while xsetroot -name " $(date +'%a, %d %b %I:%M:%S%p') | This is my dwm "; do
  sleep 1
done &
```

- Sometimes a file chooser is too big and you need to resize it. You can use float mode to resize. If that doesn't work (as it was to me), you can install dconf-editor and then navigate to org.gtk.settings.file-chooser to adjust the window-size. The last resort you can rely on is to install another WM/DE and resize it from there. It will remember the size.
