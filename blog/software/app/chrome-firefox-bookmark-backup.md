# Chrome and Firefox: Let's Automate Bookmarks Backup

Bookmark is a collection of your saved web pages that can make your life more convenient. Unfortunately, the number of bookmarks can go up to 50 or even more so it would be wise to back it up somewhere.

## Option 1: Use Chrome & Firefox Accounts

All bookmarks will be synced with your accounts automatically as long as you have access to the internet. It is very simple but the downside is that you hand over control of your data to somebody else. All of your browsing activities will be linked to your accounts; this can cause privacy issues.

## Option 2: Write Scripts

This guide is based on Linux.

### Chromium

Just backup `/home/tkiat/.config/chromium/Default/Bookmarks` somewhere. The [cron utility](https://en.wikipedia.org/wiki/Cron) can help make you worry-free. You can later move this file back to the same place to restore your bookmarks.

### Firefox

Your bookmark is stored in `~/.mozilla/firefox/xxxxxx/bookmarkbackups/*.jsonlz4`. It is hard to know exactly what the aforementioned `xxxxxx` is so I decode and test it one by one. I then hardcode the exact path in my script and let it immediately exit if not found.

Note that the Firefox bookmark is stored in the `*.jsonlz4` format, which can be decoded by using a utility like `dejsonlz4` (I installed it using nix). You can also visit somewhere in the Firefox settings menu and select JSON format to back it up. This method is not automated of course.
