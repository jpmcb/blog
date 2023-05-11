--- 
title: "So long windows 7!"
date: 2023-01-15
---

Welcome to the _"Sunday Edition"_ of my blog. This is my (occasionally) recurring weekly
news letter where I highlight some interesting things from across the tech industry, share a
few insights from the week, and give you a chance to catch up on some
worth-while reads from around the internet.

If you'd like to subscribe via RSS and receive new posts,
you can find [the atom feed here.](https://johncodes.com/index.xml)

---

## News

Microsoft ended long term support for Windows 7 this week. 

This means that both the Windows 7 Professional and Enterprise editions will no
longer receive any kind of security updates. And while “support” for Windows 7
ended in January of 2020, Microsoft has had a hard time putting this operating
system down; many deeply entrenched industries (like healthcare, manufacturing,
defense, and governement) still used it heavily. By some estimates, some 11% of
all Windows users are still using Windows 7.

I’d anticipate this version of Windows becoming a major target for bad actors
out in the wild. Major exploits of older Windows versions have been discovered
long after edtended support ended and with Windows 7 massive footprint, it
remains a very high value target for many.

If you still use Windows 7, now is the time to update.

## Good reads from across the internet

- _This girl is going to kill herself - Krista Diamond on Long Reads_

Before I got into software engineering, I worked a few summers in the outdoor
rec industry as a mountain guide. I lead rock climbing trips, rafted rivers,
hiked 14-ers, and bushwacked in the backcountry. And like this story from
Krista Diamond reflects on, I too faced unprecedented life or death situations
in the backcountry. But too often, time and time again, I would brush off my
experiance as  

We as humans are very bad at assessing risk. People don’t conceptualize
statistics, especially when it pertains to them personally. Whether it’s the
risk of weather on the mountain or the risk of missing delivery deadlines at
work, people don’t really understand the risk until it’s usually too late.

One of the best ways to combat this in my own life and work is to make the risk
more digestible and specific: “The backcountry is dangerous and risky” is
difficult to conceptualize. But “crossing a river at peak flow while wearing
your pack is dangerous and risky” is way easier to reason about. “Re-writing
the entire app is risky” is difficult to conceptualize. But “re-writing the app
to use a newest web framework would require we adopt a new database schema
which entails doing risky and costly migrations” is easier to reason about.

- _Jeff Beck, Guitarist With a Chapter in Rock History, Dies at 78 - The New York
Times_

When I was 12, I got my first guitar. I always wanted to play like my dad and
my big brother. I learned the basics from my dad but then (like any teenager
would), I wanted to find my own path. I would go to the local library, browse
their collection of CDs, find albums with interesting enough covers, and check
them out. I’d then head home, put the CD into my disc changer, pick up my
guitar, and try my best to play along.

I stumbled onto Jeff Beck’s Wired album, put it on, and was immediately
introduced to Led Boots:

I’d never heard anything like it; a mix of sweeping rock solos, complex jazz
changes, nuanced beats, & a mixing of melody and rythmn.

I went back to the library and I checked out ever Jeff Beck album they had. For
me, his music was something very special. It was so different from any of the
radio rock I’d heard before. It’s what got me interested in exploring jazz. And
to one midwest teenager with a guitar, it helped inspire a lifetime love of
music and creating.

# Command line tip

If you’re anything like me, you have a highly configured command line
environment with alot of aliases. And sometimes, you need to escape using an
alias in favor of its original intent. A good example in my environment is:

```txt
$ which vim
vim: aliased to nvim

$ vim --version
NVIM v0.8.2
```

I use NeoVim almost exclusively so the “vim” alias makes sense 99% of the time.
But whenever I need _actual_ vim, I can escape my alias using a backslash:

```txt
$ \vim --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Nov 24 2016 16:44:48)
```

This (unfortunately) won’t work on fish but is a great little backdoor
alias escape for zsh, bash, etc.
