# Shell: Command to Limit Number of Markdown Columns

There are many properties of how to make a blog post more readable and engaging. They are line height, font size, font color, the content quality just to name a few. One thing that stands out and is very important is the paragraph length. Too long paragraph length makes it boring for the reader and also harder to skim through the content.

Therefore, I would like to write a tool to catch all the lines with more than X characters (500 as a default value). It is tedious to check all the files one by one so I need to recursively check the directory. You can skip to the end for the final solution, or you may suggest a better solution or modification if you have any of it.

## Let's Create the Tool!

### Introduction to ripgrep

I prefer ripgrep, a modern replacement of grep, over grep in this context because it tells me the line number out of the box which helps me to locate the exact spot. It supports RegEx input like

```bash
rg '.{500,}'
```

This will catch all the lines of every file in the current directory with at least 500 characters long. Isn't this what we need. OK! This is the end of the post thank you very much for your attention.

### Wait a Minute

Nowadays, Markdown is a very common format and in there we usually have a lot of links and embedded HTML tags. We very commonly write something like

```
Please visit this [link](https://github.com/BurntSushi/ripgrep). Thank you.
```

And the thing is, a user doesn't see the URL right? We should filter out the Markdown link syntax to only

```
Please visit this link. Thank you.
```

because this is what a user actually sees.

### Filter Markdown Link

We can use the sed command to filter it out.

```bash
sed -E 's/\[([^]]*)\]\([^\)]*\)/\1/g'
```

### HTML Tags Removal

Again, we can use the sed command to filter it out.

```bash
sed -E 's/<[^>]*>//g'
```

This tag removal is not perfect because there might be some '<' or '>' symbols before the tag closing, like

```html
<img onload="console.log('5 + 6 < 5 ha> ha>')" />
```

This is a pretty contrived example but it can happen nevertheless. Anyway, no one would write a complex HTML tag or script in a simple markdown post so this solution should be generally good enough in my opinion.

### Combine Everything

```bash
rg --line-number '.' | sed -E 's/\[([^]]*)\]\([^\)]*\)/\1/g; s/<[^>]*>//g' | rg "(.*?):(.*?):.{500,}" -r 'filename: $1, line number: $2';
```

The -r option at the end is to reformat the output so we get only what is important: file name and line number, instead of getting all contents of every line in stdout.

Version with fallback (500 columns),

```bash
2grep-width-nolink() { rg --line-number '.' | sed -E 's/\[([^]]*)\]\([^\)]*\)/\1/g; s/<[^>]*>//g' | rg "(.*?):(.*?):.{${1:-500},}" -r 'filename: $1, line number: $2'; }
```

## What are Still Missing?

Markdown has many other syntaxes like bold, italic, etc. and it is best to filter those out also. But those are more trivial compared to Markdown links and HTML tags that are already filtered out.

## Bonus: Vim

In Vim, you can use

```vim
:set colorcolumn=500
```

This will show you when you have more than 500 characters in a single line. Note that you have to scroll in order to locate the spot so this solution is not as effective.
