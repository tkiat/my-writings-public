# xmonad: How to Install and Configure this Minimal Window Manager

xmonad is a dynamic window manager for X window system inspired by dwm but it is written in Haskell instead of C. It is similar to dwm, though I prefer xmonad because the window fits the screen just perfectly on my X200 unlike some noticeable gaps in my dwm. I would recommend anyone to look at a [Distrotube video](https://www.youtube.com/watch?v=3noK4GTmyMw) for the introduction.

## Do You Need to Know Haskell to Use xmonad?

No. You can simply browse someone else's config like the one from [Derek](https://gitlab.com/dwt1/dotfiles/) and copy-paste from it. Though I did use some basic Haskell knowledge (and a Haskell linter) to help set the order of status bar items like `concat` command below. Without any Haskell knowledge, I would have had a hard time doing this.

```
DLog.ppOrder = \(ws : l : t : ex) -> ws : concat ex : [t]

```

## Installation

Better look at their doc. Note that, unlike dwm, it is not required to build it from source in order to configure xmonad. You can just modify `xmonad.hs` and recompile it. I would recommend anyone to use package manager first. xmonad is popular so you can expect that most popular package managers offer it. I prefer to install it using nix.

```
nix-env -i xmonad
```

Note that I needed `libghc-xmonad-dev` on my Debian to build it from source.

## Configuration

You can simply copy someone else's config. You can find the official config by searching "xmonad config archive" on a search engine which should lead to [this](https://wiki.haskell.org/Xmonad/Config_archive).

## Add Status Bar with Xmobar

xmonad is compatible with dmenu out of the box but it lacks a status bar. xmobar is a common and minimalistic status bar for xmonad. Simply install xmobar and use someone else config. I started by watching a [Distrotube video](https://www.youtube.com/watch?v=jCzuMHU3Qtw) about the basic setup and adjust further from there.

## Haskell Formatter and Linter

You might want to beautify your xmonad.hs file. I have Brittany (Haskell source code formatter) installed and I also wrote my own VIM plugin to format it on save. You can search plugins for the editor of your choice. For the linter, I simply installed haskell-language-server and configured it in my coc.nvim config. A linter can significantly reduce the recompilation error which helps me a lot.

## My xmonad.hs Config

```haskell
import Prelude
import XMonad
import qualified XMonad.Hooks.DynamicLog as DLog
import XMonad.Hooks.ManageDocks ( ToggleStruts(..)
, avoidStruts
, docks
)
import qualified XMonad.StackSet as W
import XMonad.Util.EZConfig ( additionalKeysP )
import XMonad.Util.Run ( hPutStrLn
, spawnPipe
)

main = do
xmobarProc <- spawnPipe "xmobar ~/.xmobarrc"
xmonad
$ docks def
{ borderWidth = 2
, clickJustFocuses = True
, focusedBorderColor = "#005577"
, focusFollowsMouse = False
, layoutHook = myLayout
, logHook = DLog.dynamicLogWithPP $ myLogHook xmobarProc
, manageHook = myManageHook
, modMask = mod4Mask
, normalBorderColor = "#222222"
, terminal = "st"
, workspaces = ["General", "Web", "3", "4", "Login", "6", "7", "Media"]
}
`additionalKeysP` myKeys

windowCount :: X (Maybe String)
windowCount =
gets
$ Just
. show
. length
. W.integrate'
. W.stack
. W.workspace
. W.current
. windowset

myKeys = [("M-b", sendMessage ToggleStruts)]

myLayout = avoidStruts (Full ||| Tall 1 (3 / 100) (1 / 2))

myLogHook h = DLog.xmobarPP
{ DLog.ppOutput = hPutStrLn h
, DLog.ppCurrent = DLog.xmobarColor "#00bbdd" ""
, DLog.ppTitle = DLog.xmobarColor "#00bbdd" "" . DLog.shorten 60
, DLog.ppHidden = DLog.xmobarColor "#ffa500" ""
, DLog.ppHiddenNoWindows = DLog.xmobarColor "grey" ""
, DLog.ppExtras = [windowCount]
, DLog.ppSep = "<fc=#82aaff> | </fc>"
, DLog.ppOrder = \(ws : l : t : ex) -> ws : concat ex : [t]
}

myManageHook = composeAll
[ className =? "Chromium" --> doShift "Web"
, className =? "Chromium-browser" --> doShift "Web"
, className =? "Firefox Developer Edition" --> doShift "Web"
, className =? "Tor Browser" --> doShift "Web"
, className =? "qutebrowser" --> doShift "Web"
, className =? "Firefox" --> doShift "Login"
, className =? "Icecat" --> doShift "Login"
, className =? "TelegramDesktop" --> doShift "Login"
, className =? "Blender" --> doShift "7"
, className =? "Gimp" --> doShift "7"
, className =? "Inkscape" --> doShift "7"
, className =? "kdenlive" --> doShift "7"
, className =? "FreeFileSync" --> doShift "Media"
, className =? "supertux2" --> doShift "Media"
]
```

## My .xmobarrc config

```haskell
Config {
  font = "xft:Comic Shanns:pixelsize=16",
  additionalFonts = [],
  borderColor = "black",
  border = TopB,
  bgColor = "black",
  fgColor = "grey",
  alpha = 255,
  position = Top,
  textOffset = -1,
  iconOffset = -1,
  lowerOnStart = True,
  pickBroadest = False,
  persistent = False,
  hideOnStart = False,
  iconRoot = ".",
  allDesktops = True,
  overrideRedirect = True,
  commands = [
    Run StdinReader,
    Run Cpu ["-L","3","-H","50", "--normal","green","--high","red"] 10,
    Run Memory ["-t","Mem: <usedratio>%"] 10,
    Run Date "%a %b %_d %Y %H:%M:%S" "date" 10
  ],
  sepChar = "%",
  alignSep = "}{",
  template = "%StdinReader% }{ %cpu% <fc=#82aaff>|</fc> %memory% <fc=#82aaff>|</fc> <fc=grey>%date%</fc>"
}
```
