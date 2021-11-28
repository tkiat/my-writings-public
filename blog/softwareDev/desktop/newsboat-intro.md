# Newsboat: A Terminal-Based Feed Reader

RSS feed is an aggregator of news, articles, etc. from many websites to a single place so that a user does'nt have to browse on a gazillion of websites. Inoreader, a web-based RSS reader, was the best I could find. It offered a compact UI, flexible feed grouping, read-anywhere with an account, and many more. On the other end, there are free and open-source terminal-based RSS readers. They have some pros and cons compared with web-based ones.

## Pros

- Generally less tracking than a typical web-based RSS reader.
- A user can choose any browser to open a feed. This includes a text-based browser.
- Can offer more tweaking possibilities like key binding, backup server, color, history, and many more.

## Cons

- Be typically more tedious to set it up at first.
- Not available as an Mobile app.

## Introduce Newsboat

Newsboat is a fork of the Newsbeuter, another feed reader. You can write a list of URLs and corresponding tags and Newsboat will automatically show them upon start. You can modify parameters like colors and keybindings. It supports a feed update via CLI command, this means you can use any cron job with it. The rest you can find in the doc. Lastly, you can use Newsboat as a client for the established RSS servers like Inoreader, NewsBlur, and NextCloud News.

## My ~/.newsboat/config

```text
unbind-key C
unbind-key h
unbind-key j
unbind-key k
unbind-key l
unbind-key o
unbind-key w

bind-key ^D pagedown
bind-key ^U pageup
bind-key d toggle-show-read-feeds
bind-key h quit
bind-key j down
bind-key k up
bind-key l open
bind-key w open-in-browser
browser w3m
color article             white   black
color background          white   black
color info                white   black      bold
color listfocus           white   color240   bold
color listfocus_unread    magenta color240   bold
color listnormal          white   black
color listnormal_unread   magenta black
datetime-format "%L"
history-limit 0 # search history
keep-articles-days 3
macro q set browser "qutebrowser %u"; open-in-browser ; set browser w3m
macro i set browser "icecat %u"; open-in-browser ; set browser w3m
show-read-articles no
show-read-feeds no
```
