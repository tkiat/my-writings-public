# Shell Script: Demystify du command

`du` command in Linux estimates file and directory space usage. I used it lately and was confused about the format of the outputs (why it is different than using `ls -l`) so I did some research and hence this post.

1. Create an empty folder and cd to it

```bash
mkdir temp && cd temp
```

2. Create a file of size 123456789 bytes

```bash
dd if=/dev/zero of=testfile bs=123456789 count=1
```

3. Without further ado, run

```bash
du

120564 .
```

4. du only lists folder. Use -a to list files as well.

```bash
du -a

120564 ./testfile
120564 .
```

5. We are interested in `testfile` here not the current folder. To filter out this, add awk

```bash
du -a | awk -F' ' '{ if ($2 != ".") {print} }'

120564 ./testfile
```

6. Why 120564? Remember we created 123456789 bytes right? It turns out du, by default, compute size in 1024 bytes block (123456789/1024 gives us around 120563.27)
7. Now we know what is going on. Try to change the block size from 1024 to 1 byte

```bash
du -a --block-size=1 | awk -F' ' '{ if ($2 != ".") {print} }'

123457536 ./testfile
```

8. This is quite closer to 123456789 bytes. What is still missing? By default, du displays disk size, not the apparent size. The apparent size is the number of bytes in the file while the disk size is how much space it uses on the disk (they are not equal, think of a fat person whose width is 1.5 chair but occupies two chairs). To print the apparent size, do

```bash
du -a --apparent-size --block-size=1 | awk -F' ' '{ if ($2 != ".") {print} }'

123456789 ./testfile
```

9. This is it! How about printing it in a human-readable format? du provides a handy option `-h`

```bash
du -a -h | awk -F' ' '{ if ($2 != ".") {print} }'

118M ./testfile
```

10. Wait, 118M is very different than 123456789. du, by default, uses IEC format (1Kb = 1024 bytes). To use SI format (1Kb = 1000 bytes), do

```bash
du -a --si | awk -F' ' '{ if ($2 != ".") {print} }'

124M ./testfile
```

Now the output makes more sense
