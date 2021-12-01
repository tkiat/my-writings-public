# Shell: Command to Limit Number of Markdown Paragraph Columns

In addition to line-height, font size, the content itself, and so on, paragraph length is one of the most important factors for a readable and engaging blog post. Too long paragraph length makes it boring for the reader and also harder to skim through the content. Too short paragraph length will make the reader's eyeballs roll too quickly like [rick roll](https://www.youtube.com/watch?v=dQw4w9WgXcQ).

Therefore, I would like to write a tool to catch all the lines with more than X characters recursively in a certain directory. You can skip to the end for the final solution or suggest a better solution if any.

## Let's Create the Tool!

### Introduction to ripgrep

I prefer ripgrep, a modern replacement of grep, for this task because it simply tells me the line number and also supports regex.

```bash
$ rg '.{500,}'
```

This will catch all the lines of every file in the current directory with at least 500 characters long. Isn't this what we need. OK! This is the end of the post thank you very much for your attention.

### Wait a Minute

Nowadays, Markdown is a very common format and in there we usually have a lot of links and embedded HTML tags. We very commonly write something like

```
Please visit this [link](https://github.com/BurntSushi/ripgrep). You <a href="noob">link</a>.
```

And the thing is, a user doesn't see the URL and HTML tags right? We should exclude the link from the result like this.

```
Please visit this link. You noob.
```

### Filter Markdown Link

We can use the sed command to filter it out.

```bash
$ sed -E 's/\[([^]]*)\]\([^\)]*\)/\1/g'
```

### HTML Tags Removal

Again, we can use the sed command to filter it out.

```bash
$ sed -E 's/<[^>]*>//g'
```

This tag removal is not perfect because there might be some '<' or '>' symbols before the tag closing, like

```html
<img onload="console.log('5 + 6 < 5 ha> ha>')" />
```

This is a pretty contrived example but it can happen nevertheless. Anyway, no one would write a complex HTML tag or script in a simple markdown post so this solution should be generally good enough.

### Combine Everything

```bash
$ rg --line-number '.' | sed -E 's/\[([^]]*)\]\([^\)]*\)/\1/g; s/<[^>]*>//g' | rg "(.*?):(.*?):.{500,}" -r 'filename: $1, line number: $2';
```

The -r option at the end is to reformat the output so we get only what is important: file name and line number, without the file content.

### Add Fallback Value (500 Columns)

```bash
2grep-morethan() { rg --line-number '.' | sed -E 's/\[([^]]*)\]\([^\)]*\)/\1/g; s/<[^>]*>//g' | rg "(.*?):(.*?):.{${1:-500},}" -r 'filename: $1, line number: $2'; }
```

## What are Still Missing?

Markdown has many other syntaxes like bold, italic, etc. and it is best to filter those out also. But those are more trivial compared to Markdown links and HTML tags that are already filtered out. Our script is therefore not perfect but at least it never outputs false negatives.