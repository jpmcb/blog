---
title: "Editing Astro projects with Neovim"
date: 2024-03-16T10:00:00-06:00
---

[Astro](https://astro.build/) is a fast & dynamic web framework designed to be flexible
enough for small static websites, blogs, big interactive apps, and more.
I've been interested in checking it out since building silly little static websites
that are "Content-first" is a fun past-time of mine.

Since Neovim is my main text editor, it took _abit_ of configuration fu
to get things up and running.

Herein is my Astro setup:

---

## Prerequisites

You probably already have most of these tools installed since they have become
synonymous with most Neovim configs:

- A newer version of [Neovim](https://github.com/neovim/neovim) -
At the time of this writing, [the latest version is v0.9.5](https://github.com/neovim/neovim/releases/tag/v0.9.5).
I typically recommend staying on the latest Neovim version since it gets you the latest
features that various plugins may take advantage of.
- A package manager - these days, I (and it seems most of the Neovim community)
typically recommend [Lazy.nvim](https://github.com/folke/lazy.nvim)
- [Treesitter for Neovim](https://github.com/nvim-treesitter/nvim-treesitter) - an essential tool
for syntax parsing and definitions.

## Install Treesitter parsers

Astro files are actually amalgamations of several technologies.
Depending on your Astro configuration, there may be HTML, Typescript, CSS, JavaScript, Tsx, etc.

In order to get syntax highlighting and parsing, you'll need to install
a few Treesitter parsers that enable Treesitter to introspect the different
chunks of an Astro file:



On the Neovim command palate, first make sure Treesitter is updated:

```
:TSUpdate
```

Then, install the parsers:

```
:TSInstall astro
:TSInstall css
:TSInstall typescript
:TSInstall tsx
```

Depending on your Treesitter setup, you may choose to ensure these parsers are always
installed via the `nvim-treesitter` plugin:

```lua
require("nvim-treesitter.configs").setup({
	ensured_installed = {
		"astro",
		"css",
		"typescript",
		"tsx",
	},
}
```

This way, you don't have to manually remember to install these parsers:
they'll just be there thanks to Treesitter's config.

## Treesitter grammar plugin

Having the parsers alone won't give Treesitter everything it needs to correctly
parse and crawl your Astro files: you'll also need to install [this community](https://github.com/virchau13/tree-sitter-astro)
plugin that provides Treesitter with the appropriate grammar for how to _actually_
use the parsers we've installed to interpret those files.

In the future, this may eventually be upstreamed into Treesitter itself.
But for now, at the time of this writing, you'll need this additional plugin
to instruct Treesitter on how to understand `.astro` files.

In short, this ensures that the Astro specification is understood by Treesitter:

```
---
{typescript}
---
{html}
```

having Typescript highlighting and syntax definitions in the frontmatter.
And HTML / Tsx in the rest of the `.astro` file.

To install the plugin, using Lazy in your lua configs:

```lua
-- Astro treesitter grammar bindings
{ "virchau13/tree-sitter-astro" },
```

## Astro language server

As you may already know, language servers for Neovim are the bread and butter
of modern code editing. Without one, you're _almost_ back to the dark age.

In order to get modern functionality when editing your Astro files
(like inline suggestions, "Go to definition", "Refactor across project", "Find types", etc.)
you'll need an Astro language server.

To install the server for use by Neovim, you can get it globally via `npm`:

```sh
npm install -g @astrojs/language-server
```

#### Optional: Install language server through Mason

These days, I've moved away from installing one off bespoke editor tools
from a myriad of ecosystems.
And, instead, have chosen to unify my editor toolchain using [Mason](https://github.com/williamboman/mason.nvim):

```
:MasonInstall astro-language-server
```

This installs the language server through the Mason framework
and allows me to manage _all_ of my Neovim editor tools (LSPs, DAPs, linters, etc.)
from within Mason instead of through one off package managers.
More importantly, it gives me consistency across the many different machines
I may be using my Neovim configs with:
no more jumping to a new machine and having to remember
what commands I used to install some random, one-off tool.
Now, it's all just managed by Mason.

Further, within Mason's config (and the `mason-lspconfig` config helper plugin)
I can force the Astro language server to be installed automatically.

```lua
require("mason").setup()

-- Ensures the servers named in nvim-lspconfig are installed by Mason
-- github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md

require("mason-lspconfig").setup({
	ensure_installed = {
		"astro",
	},
})
```

## Configure your Astro language server

To actually enable and attach your Astro language server when editing `.astro` files,
you'll need to configure it via [the `nvim-lspconfig` plugin](https://github.com/neovim/nvim-lspconfig);
the configuration binding plugin for all language servers used by Neovim.

To install `nvim-lspconfig` via Lazy.nvim:

```
-- nvim LSP configs
{ "neovim/nvim-lspconfig" },
```

Configuring the language server is actually rather simple, but it's an important step
to ensure `.astro` files are "seen" by Neovim and attached to your installed Astro language server:

```lua
local lspconfig = require("lspconfig")

-- Astro langauge server
lspconfig.astro.setup({})
```

For a full understanding of the defaults and possible configuration options,
[read up on it here](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md#astro).

## Fin

And that's it! This gets you the basic setup with syntax highlighting, the Astro language server, etc.
Happy coding!

{{< comments >}}
