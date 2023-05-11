---
title: "Vim Tips!"
date: 2017-03-22T21:19:16-07:00
draft: false
---

(Note: this is a post from a legacy blog. This post was intended to help new OSU students get started with Vim)

I'd consider myself some sort of Vim - evangelist. It's an incredible tool and has ALOT of power. If there's something you wish Vim could do, there's probably a plugin for it or a way to make Vim do it with scripting (in its own language!). Moderate proficiency in Vim is a skill that nearly every developer could benefit from. Being able to modify files directly on a server is necessary in almost every development sphere. 

## Get Vim

Most unix like operating systems (including MacOS) should come pre-packaged with Vim. If not, you can install it with yum:
```
yum install vim
```
Or apt-get
```
sudo apt-get update
sudo apt-get install vim
```

On windows you'll want to use the installation wizard [provided by the vim organization](https://www.vim.org/download.php)

On MacOS, if for some reason you're missing Vim, you can install it with the Homebrew installer (a great [package manager and installer](https://brew.sh/)):
```
brew install macvim
```

## Getting started:

### Command cheat sheets:
Cheat sheets are really great to have printed off at your desk for quick reference. Here are a few of my favorites:
* [fprintf.net](https://www.fprintf.net/vimCheatSheet.html)
* [Linux Training Academy](https://www.linuxtrainingacademy.com/vim-cheat-sheet/)
* [VimSheet.com](http://vimsheet.com/)

### Interactive Tutorials 
* [The Vim browser game](https://vim-adventures.com/) 
This is a great way to learn the movement keys to get around a file and do basic operations. Here are some other great resources on getting started in Vim:

* vimtutor
Vim is packaged with its own tutorial named vimtutor! To start the tutorial, simple enter the name of the program! You can exit vimtutor the same way you would normally exit vim (see the section below)
```
vimtutor
```

* [Vim in 4 weeks](https://medium.com/actualize-network/how-to-learn-vim-a-four-week-plan-cd8b376a9b85)
A comprehensive, in depth plan to learning the various aspects of Vim. This article gets talked alot about when people are learning Vim.

* Only use Vim!
If you only use Vim, and don't let yourself use anything else (like sublime text or VS Code), you'll learn fast (but I would recommend going through one of the interactive tutorials first)!

## Exiting Vim:

Alot of people start up vim and then get frustrated by not being able to save and exit. It's confusing initially! Here are a few different ways to save and exit!

### Saving and Exiting
1. Hit esc to ensure you're in normal mode
2. Enter the command palette by hitting ` : `
3. Type `qw` and hit enter. This will "write" the file and than "quit" Vim

Alternatively: in normal mode, hitting `ZZ` (yes both capitalized) will save and exit vim for you!

### Making a hard exit
1. Hit esc to ensure you're in normal mode
2. Enter the command palette by hitting ` : `
3. Type `q!` and enter to force vim to quite without writing (saving) anything. Danger! All things you typed since your last "write" will NOT be saved 

### Just saving
1. Hit esc to ensure you're in normal mode
2. Enter the command palette by hitting ` : `
3. Type `w` and enter to "write" your changes

## Customize Vim: 

When starting Vim, it will search for a `.vimrc` file in your home directory (based on your home path name). If you don't have one, you can create one (right in your home directory, usually the same directory as your .bashrc) and use it to customize how vim functions on startup! The following are some basics that everyone should have (The reader should note that " are comments in Vimscript):

```vimscript
" Turns on nice colors
colo desert
" Turns on the syntax for code. Automatically will recognize various file types
syntax on
```

Placing these (and other vimscript things) into your `.vimrc` will change the behavior of vim when it starts. Here's a vimscript for setting tabs to be 4 spaces!

```vimscript
filetype plugin indent on
" show existing tab with 4 spaces width
set tabstop=4
" when indenting with '>', use 4 spaces width
set shiftwidth=4
" On pressing tab, insert 4 spaces
set expandtab
```

This next one is more involved, but it auto creates closing parenthesis for us! We can see that the `h` and `i` in this vimscript are the literal movement commands given to vim after auto completing the parenthesis to get the cursor back to the it's correct position. 

```vimscript
" For mapping the opening paran with the closing one
inoremap ( ()<Esc>hi
```

This should give you a small taste of what vimscript is like and what it's capable of. It can do alot and it's very powerful. If there's something you want Vim to do (like something special with spacing, indents, comments, etc), search online for it. Someone has likely tried to do the same thing and wrote a Vim script for it. 

[This cool IBM guide](https://www.ibm.com/developerworks/library/l-vim-script-1/index.html) goes into some depth with how vim scripting works and what you can build.

## Search in Vim:

Vim makes it super easy to search and find expressions in the file you have open; it's very powerful.

To search, when in normal mode (hit esc a few times):
1. hit the forward-slash key ` / `
2. Begin typing the phrase or keyword you are looking for
3. Hit enter
4. The cursor will be placed on the first instance of that phrase!
5. While still in normal mode, hit `n` to go to the next instance of that phrase!
6. Hitting `N` will go to the previous instance of that phrase
7. To turn off the highlighted phrases you searched for, in normal mode, hit the colon ` : ` to enter the command palette 
8. Type `noh` into the command palette to set "no highlighting" and the highlights will be turned off

## Split window view!

You can have two instances of Vim open at once in a split window on the terminal. This is like tmux, but it's managed exclusively by vim!

### Horizontal split
When in normal mode, enter this into the command palette to enter a horizontal split. The "name of file to load" is the path to a file you want to open. The path is relative to where Vim was started from.
```
:split <name of file to load> 
```

To achieve a vertical split:
```
:vsplit <name of file to load>
```

To change the current active panel, (when in normal mode) hit `Ctrl w Ctrl w` (yes, that's ctrl w twice)

## Inception
Start a bash shell (or any other unix-y command) right in Vim! (in other words, yes Inception is real). When in normal mode, start the command palette and use the following command to bring up a bash shell

```
:!bash
```
Note the exclamation mark telling Vim to execute the command.

Here's where it gets crazy. Your initial shell you used to enter Vim is still running. On top of that shell, Vim is running. Now, on top of that, a bash shell instance is now running! It's sort of like an onion with all the layers you can go down into. To get back to Vim, exit your bash instance with the `exit` command. If you than exit Vim, you will be back to your original shell. A word of warning though, all this job handling and nested processes can get fairly processor hungry. So, if your noticing some chugging, back off alittle on the inception. 

You can execute almost any unix command like this. For example:
```
:!wc sample.txt
```

This will run the word count program for the sample.txt file! Command inception is crazy cool!

## Block Comments
I find this extremely helpful when doing full Vim development. This is taken from the following [Stack Overflow discussion](https://stackoverflow.com/questions/1676632/whats-a-quick-way-to-comment-uncomment-lines-in-vim) 

For commenting a block of text:
 
 "First, go to the first line you want to comment, press Ctrl V. This will put the editor in the VISUAL BLOCK mode. 
 
 Now using the arrow key, select up to the last line you want commented. Now press Shift i, which will put the editor in INSERT mode and then press #. 
 
 This will add a hash to the first line. (if this was a C file, just type //). Then press Esc (give it a second), and it will insert a # character on all other selected lines."

Un-commenting is nearly the same, but in opposite order using the visual block mode!

## Time traveling!

Yes, you heard that right, vim makes time travel possible! Note, this ONLY works within current Vim sessions. So, if you exit vim, you will lose your current session's stack of edits. 

On the Vim command palette, which you can enter from Normal mode by hitting the colon `:`, you can type 'earlier' and 'later' to go back and forth in your current session stack of edits. This is super helpful if you need to revert a few small changes you've made in the last minute or want to revert everything you did in the last hour. Or if you decide you do want those changes, go forward in time too! 

``` vimscript
:earlier 3m
:later 5s
```

## Plugins

One of the reasons Vim is so great is that there are TONS of awesome plugins for Vim. If you're having a hard time scripting something on your own with vimscript, there's probably a plugin for it! They range anywhere from super useful to super silly. Some of my favorites include the file system NERD tree, the fugitive git client, and ordering pizza with Vim Pizza (yes that's right, you can order pizza with Vim! It can really do it all!)

Check out [this great resource](https://vimawesome.com/) for discovering Vim plugins, instructions to install them, and buzz around the Vim community.

# Conclusion:

This by no means is a comprehensive guide. There are a ton of great resources for Vim out there and its capabilities. This guide should serve more as a small taste to what Vim can do and maybe peaked your interest to learning more about it.

Take heart! Vim has a steep learning curve, and, like any complex tool set, it takes alot of time and practice to get good with. Google is your friend here. 

Feel free to reach out to me if something from this guide was not super clear!

---

If you found this blog post valuable,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}
