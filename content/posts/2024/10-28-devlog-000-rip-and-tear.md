---
title: "Devlog 000 - rip-and-tear.nvim"
date: 2024-10-28T10:00:00-06:00
---

{{< youtube vU2Gj40v9II >}}

---

This is probably one of the stupidest Neovim plugins I've built.

And I'm having a ton of fun doing it.

[`rip-and-tear.nvim`](https://github.com/jpmcb/rip-and-tear.nvim) is a super-not-serious Neovim plugin that plays a configured mp3 when you're typing in the editor. The original idea, like so many great hacker scenes in movies, was to have an epic soundtrack starts playing as you engaged your mad Neovim skills.

Something akin to a Mr. Robot scene:

{{< youtube 67gYEK4FtzA >}}

---

["Rip and Tear"](https://www.youtube.com/watch?v=vyA1z2A-lhU&pp=ygUMcmlwIGFuZCB0ZWFy) is one of the title tracks for the 2016 Doom game; a chugy, heavy metal song that really gets you amped up. It felt like an appropriate name for a plugin intended for egregious hype and motivation. And _"I. Dogma"_ from the game, a preamble about the Doomslayer, seemed to fit how using Neovim sometimes feels:

_In the first age_\
_In the first battle_\
_When the bloatware first lengthened_\
_One stood_
 
_He chose the path of perpetual torment_\
_In his ravenous configurations he found no peace_\
_And with boiling blood he scoured the internet_\
_Seeking vengeance against the VScoders who had wronged him_
 
_And those that tasted the bite of his keyboard bindings named him_\
_The Neovimer._

## How the plugin works

Upon installing the plugin, in your Neovim configs, the following map can be passed in:

```lua
require('rip-and-tear').setup({
    mp3_file = '/Users/jpmcb/Downloads/rip-and-tear.mp3'
})
```

For obvious legal reasons, I couldn't include `rip-and-tear.mp3` in the plugin or git repo itself, so that piece is left up to an end user: you must provide the full path to an mp3 file in the `mp3_file` field which will play during typing.

You can also pass in `player_command` to denote which system mp3 player should be used and `delay` to configure how long to wait before starting a song as you're typing. The default player is `mpg123` so end users need to have that installed to get this plugin to work (easily installable via `brew install mpg123`). In the future, I hope to incorporate other system players.

## Detecting keypresses

Neovim provides a handy way to handle when a key is pressed via [`vim.on_key`](https://neovim.io/doc/user/lua.html#vim.on_key()):

```lua
vim.on_key(on_key_function, nvim_namespace_id)
```

In this arbitrary example, `on_key_function` is a defined function that takes a char (the key that was pressed) and performs some action: in our case, play the mp3. We don't need the char that was pressed, so we effectively ignore it. This API utility then maps our function to the system loop enabling us to capture every key press every time.

The `nvim_namespace_id` is the integer ID of a Neovim namespace (which can be useful for separating buffer highlights or other plugin specific entities).

## Managing process state 

By default, the plugin uses `mpg123` to play an mp3 file. We can execute that utility like so:

```lua
local mp3_process = nil
local cmd = 'mpg123'
local args = {'--loop', '-1', mp3_file}

mp3_process = vim.fn.jobstart({cmd, unpack(args)}, {detach = true})
```

[`vim.fn.jobstart`](https://neovim.io/doc/user/builtin.html#jobstart()) is apart of Neovim's broader "job control" schematic: it allows for plugins and users to control multiple jobs and tasks within Neovim without blocking the current editor instance. It's a great way to perform and manage background tasks or launch CLI tools. And it's what allows you to continue to type in your Neovim editor without having to wait for `mpg123` to finish executing.

The `mp3_process` gets assigned a "job ID" which is a Neovim managed identifying integer for that specific job running within that Neovim session. Importantly, this is _not_ the actual system's process ID.

And you might be saying _"John! Why not use [`vim.system`](https://neovim.io/doc/user/lua.html#vim.system())! It's so much better!"_ True, `vim.system` is preferred for running system commands and has built in asynchronous mechanics for running this type of background task without having to deal with Neovim managed job IDs. `vim.system` will even return to you a `vim.SystemObj` which has a PID integer for the real system process, not a job managed by Neovim.

Well, I'm lazy.

And for a small, silly proof of concept plugin, it stands to reason that I wouldn't spend abunch of precious time trying to get asyncrounous Lua within Neovim to work! `jobstart` is great for a quick and dirty processes spawning. Despite its drawbacks, it is simple and easy to work with compared to `vim.system`.

## Using timers

Neovim also includes some awesome timer utilities which makes stopping the mp3 when no keypress has occurred a breeze:

```lua
timer = vim.loop.new_timer()

timer:start(configured_delay, vim.schedule_wrap(function()
  stop_mp3()
  timer:stop()
end))
```

This little mechanism makes it simple to integrate with [the Neovim event-loop](https://neovim.io/doc/user/luvref.html#luv-event-loop) and kick off a timer that will fire an async function when the configured delay is reached. We can always reset the timer if typing continues since we are capturing every keystroke.

This gives that continuous hype "feel" I was going for that will continue to play the mp3 only as long as you're typing or hitting keys.

## Rough edges

But there are definitely still some big bugs to iron out.

For example, if you're typing, the song starts playing, and then you exit Neovim (with something like `:q` or `ZZ`) without waiting for the song to stop (because you stopped typing) the `mp3_process` doesn't get reaped or cleaned up. The song just keeps playing in the background. Worst case, you go _back_ into Neovim and start typing to only have _another_ `mp3_process` kick off too, overlapping the first.

You can imagine the headaches this can cause an end user.

And to play devil's advocate to my own point, utilizing `vim.system` and reaping any of the PIDs on exit / cleanup would likely be a more elegant solution to this.

I'd also like to include the capability to play a whole directory of mp3s, not just a single file. The same song over and over again can get very annoying.

Check out the plugin for yourself, and god speed: https://github.com/jpmcb/rip-and-tear.nvim
