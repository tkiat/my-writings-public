# How to Automate Bookmarks Backup from Chromium and Firefox

Bookmark is a collection of your saved web pages that can make your life more convenient. Unfortunately, the number of bookmarks can go up to 50 or even more so it would be wise to back it up somewhere.

## Option 1: Use Chrome & Firefox Accounts

All bookmarks will be synced with your accounts automatically as long as you have access to the internet. It is very simple but the downside is that you hand over control of your data to somebody else. All of your browsing activities will be linked to your accounts, this can cause privacy issues.

## Option 2: Write Scripts and Use Cron Services

This guide is based on Linux.

### Chromium

Just backup /home/tkiat/.config/chromium/Default/Bookmarks somewhere, it is in a JSON format. I store it in my (private?) Git repository. Here is my script

```sh
#!/usr/bin/env dash
bookmark_filename="bookmark-Chromium-$(date +'%Y_%m_%d').json"
cd $MY_PRIVATE_DIR
rm -f bookmark-Chromium-*
cp ~/.config/chromium/Default/Bookmarks $bookmark_filename
```

The script is already considered "automation" but it is even better to automate this "automation" by using any [cron utility](https://en.wikipedia.org/wiki/Cron).

### Firefox

Your bookmark is stored in `~/.mozilla/firefox/xxxxxx/bookmarkbackups/*.jsonlz4`. It is hard to know exactly what the aforementioned `xxxxxx` is so I hardcode the exact path in my script and let it immediately exits if not found. Note that the Firefox bookmark is stored in the `*.jsonlz4` format, which can be decoded by using a utility like `dejsonlz4` (I installed it using nix). You can use the JSON equivalent to restore your bookmarks from Firefox settings.

```sh
#!/usr/bin/env dash
bookmark_old=~/.mozilla/firefox/1lyg6uc1.default-default/bookmarkbackups/*.jsonlz4
if ! [ -f $bookmark_old ]; then
  echo "Cannot find Firefox bookmark file. Exiting..."
  exit 1
fi

# can use e.g. dejsonlz4 later to convert jsonlz4 to json
bookmark_filename="bookmark-Firefox-$(date +'%Y_%m_%d').jsonlz4"
cd $MY_PRIVATE_DIR
rm -f bookmark-Firefox-*
cp $bookmark_old $bookmark_filename
```
