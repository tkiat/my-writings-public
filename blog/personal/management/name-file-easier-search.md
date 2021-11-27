# My Way to Name Files for Easier Search

There are excellent CLI programs like fzf to do a fuzzy search, however, we don't always have that luxury. Think of when you navigate through a file manager on a Samsung tablet or accessing a file on some cloud web clients, they might not provide sufficient search functionalities. Therefore, it is always helpful to name files in an organized manner. It lessens your search time and you will have more time to do other stuff. This is my opinion so take it with a grain of salt.

## How I Name My Files

1. **Use only alphanumeric, hyphen (-), and underscore (\_)**. Spaces in file name like "my file" make Linux commands interpret as two separate entities: "my" and "file". You need to escape space characters or wrap it up with a double quote to get it worked. Secondly, some characters are forbidden on Windows, Linux, and macOS but none of those are - and \_ so I limit to myself only these twos.
2. **Use \_ to join a group related words, - to separate them**. If you notice carefully when you click on `I_Love_You` section on the file `I_Love_You-John_and_Jane.txt`, only the parts connected by \_ will be highlighted, in this case, `I_Love_You`. Name things like this make it easier to copy information and paste it somewhere. For example, it is easy to copy the date from `2020_09_10-My_Homework_Copied_from_Others.odt`.
3. **Date format: YYYY_MM_DD**. This makes the sort-by-name operation also sort the files chronologically, making the search easier.
4. **Music**. I name a music folder containing tracks Artist-Year-Album inside the same folder, i.e. from the most general attribute to the most specific. Inside the folder I name each track `01-Track1.ogg`, `02-Track2.ogg`, and so on, I reserve 00 for `00-Playlist.m3u`.
5. **Movie**. I once tried to name by genre but it was very confusing, a movie can have multiple sub-genres like comedy and horror so I don't find it possible to do so. I just sort it by name.  
   This doen't mean that you cannot sort by genre. You can just create hard links to the main folder. Beware that the usual copy operation to another HDD may not preserve hard links, a Linux user might use the commands `cp` and `rsync` with some options to preserve them.  
   By the way, I don't think you should put an article in front of a movie name as you might end up not remembering it. A practice that I have seen on some websites is to move an article to the back from "The man in the wood" to "Man in the Wood, The".
