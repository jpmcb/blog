---
title: "NeoVim: Using the spellchecker"
date: 2023-02-25
---

{{< youtube KoL-2WTlr04 >}}

---

_I know._

Any _sane_ person's editor already has spellchecking built in.
And enabled by default.
But I could never leave my beloved Neovim
(and all the muscle memory I've built) just to spell things correctly!
That's why I became a programmer dammit! Who needs to know how to spell correctly
when I can have single character variable names!
Besides. We have _tools_. Isn't that what computers are for!?
Automate the boring stuff! _(like spelling and grammar)._

Thankfully, the long awaited `spell` integration features have landed in the NeoVim APIs.
While `spell` has been around forever (or at least as long as Vim has been),
[only recently have the NeoVim Lua APIs](https://github.com/neovim/neovim/pull/19419)
been able to take advantage of it.
Now, by default, _without plugins_, nvim can make spelling suggestions and
treesitter can do the right things with misspellings in the
syntax highlighting, code parsing, and search queries.
Or in other words, `spell` is _waaay_ nicer to use since it'll ignore code (but not other stuff).

This has already _greatly_ increased my productivity when writing.
If you know anything about me (or have had the pleasure of working with me
and seeing my egregious spelling mistakes),
you know that I can _not_ spell.
My reliance on good spell checker tools has really evolved into a dependency.
But no longer! Now, I can continue to convince myself that nvim
is a superior editor because it finally has _spell checking_.

In all seriousness, shout out to the NeoVim community and maintainers
for getting this feature in!! It's already been a huge value add
and saved me on _several_ occasion from pushing an embarrassing commit message.

---

### Enabling it

Make sure you have a new-ish version of NeoVim.
I'm running with a newer nightly build, but the latest official release should do the trick.
```text
❯ nvim --version
NVIM v0.9.0-dev-699+gc752c8536
```

In your nvim configuration files, you'll want to set the one of the following options:

- _For those who've ascended to using Lua_:
```lua
vim.opt.spelllang = 'en_us'
vim.opt.spell = true
```

- _Or good, ol trusty Vimscript_:
```vim
set spelllang=en_us
set spell
```

Alternatively, you can use the command prompt to enable `spell` in your current session:
```text
:setlocal spell spelllang=en_us
```

Note that `en_us` is _US English_.
But there are _tons_ of supported languages out of the box:
`en_gb` for Great Britian English,
`de` for German, `ru` for Russian, and more.

Now, you should see words that are misspelled underlined! Nice!!

### Using it

There are 3 default key-mappings my workflow has revolved around
for fixing spelling mistakes when I'm writing.

#### Finding words:

`]s` will go to the _next_ misspelled word.

`[s` will go to the _previous_ misspelled word.

Easy as that! These default key-mappings are designed to be composable
(or heck, modified in any way you like - this is NeoVim after all!)
so spend some time thinking about what re-mappings, key bindings,
or macros might make sense for you and your workflow.

#### Fixing words:

When the cursor is under a word that is misspelled,
`z=` will open the list of suggestions.
Typically, the first suggestion is almost always right.
Hit `1` and `<enter>` in the prompt to indicate you want to take the first suggestion.
And the word has been fixed!

There's also
```
:spellr
```
which is the "spell repeater". It repeats the replacement done by `z=`
for all matched misspellings in the current window.
So, if there's a word you _frequently_ misspell, using `:spellr` is a quick and easy
one stop shop for fixing _all_ the misspellings of that type.

### Adding words to the spellfile

If you've typed a word that doesn't appear in the default dictionary,
_but is spelled correctly_, you can easily add it yourself to the internal spell list.
Especially in programming docs, there are _lots_ of words not loaded into the default dictionary.
With your cursor under the _correctly_ spelled word that is underlined as misspelled,
use the `zg` mapping to mark the word as a "good" word.

Doing this, you'll notice that NeoVim will automatically create a `spell/` directory
in the runtime path (typically under `~/.config/nvim`).
And in that directory, you'll find two files:

```text
~/.config/nvim/
  |-- spell
  |    |-- en.utf-8.add
  |    |-- en.utf-8.add.spl
```

The `.add` file is a list of words you've added.
For example, my `.add` file has _tech_ words like "Kubernetes"
which don't typically appear in the default English dictionary.

The `.spl` file is a compiled binary "spellfile".
And it's what is used to actually make suggestions and crawl the dictionary graph.
Creating spellfiles is ... _rather_ involved.
But, for most people, simply using `zg` to mark "good" words gets you 99% of the way there.

---

As with most things NeoVim,
there are _excellent_ docs and APIs for using the spell interface: https://neovim.io/doc/user/spell.html
Especially if you plan to generate your own spellfiles
or programmatically modify text via the spell APIs,
these doc resources are a must read!

---

If you found this blog post valuable, comment below,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)
Your support means the world to me!!

---

{{< comments >}}
