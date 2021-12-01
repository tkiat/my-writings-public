# Vim: Write a Simple ftplugin

Being one of the most popular editors among software developers, there are a plethora of [Vim plugins](https://vimawesome.com/) available. Nevertheless, I prefer to use only as much as I need. I need NERDTree because netrw had too many bugs and the keymaps are not intuitive, coc.nvim because I need an all-in-one solution for linting and completion supports, and emmet.vim because closing the HTML tag manually is a pain. I also have some more syntax highlighting plugins like TOML and Dhall.

So why do I still want to write my own plugin? Of course, I want to do it for the sake of self-development (insert another BS here ...) but frankly, there are many real cases I need to write my own plugins.

- Customize a plugin to my need. Some plugins are based on a CLI tool and they generally don't put all the options available for that tool.
- Plugin for a less popular tool that no one has written it before.
- Organize my related scripts to a single plugin. This can be more organized than having many abbreviations, many templates, and many more in the same .vimrc file.

I will focus on a ftplugin in this post. An ftplugin, unlike a global plugin, is only applied when the file type match. You can set one single Haskell plugin at `~/.vim/ftplugin/haskell.vim` or create a haskell folder and put plugin(s) inside it like `~/.vim/ftplugin/haskell/brittany.vim`. An ftplugin will be executed whenever you open a Haskell (\*.hs) file. To check if Vim supports a particular language, type this

```vim
:setfiletype
```

then type space and finally CTRL+D.

## ftplugin Basics

First, I recommend you to learn the official introduction.

```vim
:help write-plugin
:help write-file-type-plugin
```

## My Haskell ftplugin

brittary is a source code formatter for Haskell. The below command will format the file `xmonad.hs` with 80 columns and 2 indent spaces.

```
brittany --columns 80 --indent 2 xmonad.hs
```

It is tedious to manually apply this command every time so we, lazy persons, write Vimscript to automatically format just before saving.

```vim
function! MyAutoformatter()
  echo('do something here')
endfunction

autocmd BufWritePre *.hs call MyAutoformatter()
```

Alternatively, we can overwrite equalprg (type `gg=G` in normal mode to indent the entire file).

```vim
autocmd FileType haskell let &l:equalprg = 'brittany --columns 80 --indent 2'
```

### Isn't this enough?

It is enough for a simple use case but it might be better to give a user the ability to pause or continue the autoformatter. The error checker to prevent something weird from happening before the actual save is also nice to have. Let's write a plugin for these! You can create `~/.vim/ftplugin/haskell/brittany.vim` and simply copy everything in the following order below in that file.

### 00: Command Options

Instead of declaring a variable for each command option, I just make everything fixed in the plugin for more flexibility and simplicity.

```vim
let s:command = "brittany"
let s:arguments = "
      \ --columns 80
      \ --indent 2
      \"
```

### 01: Script

This section is the high-level (exclude function details) of what we want to do. Let's begin by writing the check to prevent it from being executed more than once. This will also enable the plugin user to put this variable `g:haskell_brittany_skip` in his/her .vimrc and disable it as needed.

```vim
if exists("g:haskell_brittany_skip") && g:haskell_brittany_skip == 1
  finish
endif
let g:haskell_brittany_skip = 1
```

Execute the command when a user saves the file.

```vim
augroup brittany
  autocmd!
  autocmd BufWritePre *.hs call brittany#TryToApply()
augroup END
```

### 02: User Commands

Give a user the ability to execute commands at run time.

```vim
command! BrittanyContinue exe "call brittany#BrittanyContinue()"
command! BrittanyPause    exe "call brittany#BrittanyPause()"

function! brittany#BrittanyContinue()
  let g:haskell_brittany_pause = 0
endfunction

function! brittany#BrittanyPause()
  let g:haskell_brittany_pause = 1
endfunction
```

### 03: Function Declaration

We just declared functions without their implementation in the step 01. Now it is time to implement them. I separate the concerns by creating three functions: brittany#Apply(), brittany#ShouldApply, and brittany#TryToApply. I find this pattern useful and very reusable.

```vim
function! brittany#Apply() range
  silent! exe "keepjumps " . a:firstline . "," . a:lastline . "!" . s:command . " " . s:arguments
  call winrestview(b:winview)
endfunction

function! brittany#ShouldApply()
  if exists("g:haskell_brittany_pause") && g:haskell_brittany_pause == 1
    return 0
  endif

  if !executable(s:command)
    echoerr "brittany.vim: " . s:command . " not found in $PATH"
    return 0
  endif

  silent! exe "w !" . s:command . s:arguments . " > /dev/null 2>&1"
  if v:shell_error
    echoerr "brittany.vim: " . s:command . s:arguments . " is not valid"
    return 0
  endif

  return 1
endfunction

function! brittany#TryToApply()
  if brittany#ShouldApply()
    let b:winview = winsaveview()
    exe "%call brittany#Apply()"
  endif
endfunction
```

## Final Words

The next ftplugin I write has exactly the same format as this one. This means it is very easy for me to understand all plugins of this kind I write myself. Note that this kind of plugin is one of the easiest to write because it depends on an already polished 3rd-party tool (brittany in this case). I still have a lot to learn to write a more complex plugin.
