---
title: "2023 in review"
date: 2024-01-01T09:00:00-00:00
---

I had a huge year.

And every year, around this time, when I have a well deserved opportunity to
take a break and prepare for the next year, I like to reflect:
think on the year's accomplishments, derive some lessons learned, and drink in everything from my experiences.

Herein are my musings and thoughts regarding the last year.

## Leaving AWS

I still think about my time at AWS:
it was a short, but very sweet and formative period for me.
Out of the ashes of the Broadcom / VMware acquisition news,
an announcement that many felt was deeply misaligned with VMware's Kubernetes vision,
I went searching for something else in mid 2022.
When I eventually joined the Amazon Linux and Bottlerocket team, I felt I had found a new home among people I related to:
peers who were passionate and deeply curious about programming, the art of computer science, and Linux.

But something I've come to grips with, and continue to digest since leaving,
is just how burned out I actually was at AWS.
The team was amazing, I got to work in Rust every single day, I was surrounded by individuals I looked up to and respected,
and I was living my dream of building a Linux distribution in the open source.
And yet, expectations were lofty. There was little room for error.
Constant shifting organizational priorities. Leadership re-orgs.
And a new, ambiguous return to office policy with the looming potential of having to eventually move to Seattle.
It was a very befuddling decision to me: the entire Bottlerocket team was distributed all around the world.
Why "return to team" when the team was fully remote and async to begin with?

All of that _and_ I was coming off a relatively high pressure team at VMware shipping 
([and eventually deprecating](https://github.com/vmware-tanzu/community-edition)) TCE.

_"Customer obsession"_ is probably Amazon's deepest principle.
And it's embodied well throughout AWS:
people legitimately care about shipping the best stuff for customers
and delighting them with their work.
But for the individual contributor, like me, leadership can wield it
as justification to push beyond the bounds of what good work life balance is.

But, in the end, my burnout was no one's fault exactly: sometimes these things just happen from a stint of bad luck.
I joined AWS just before a tumultuous time in the market
where the software engineer career path would drastically contract,
layoffs would abound,
and the pressure would be on for teams to ship real value that made their engineering headcount make sense.
I was thankful to still have a job but
I also could have done a much better job of setting clear work / life boundaries
and finding balance: one of the tricky things I'm learning as I grow deeper into my tech career
is that, while I _deeply_ love and enjoy what I do,
_"variety is the spice of life"_ and finding a balance _outside_ of tech is key to having a fruitful
and enjoyable long term tech career.

I'm very proud of alot of the work I did at AWS: there were some _really_ tricky problems to solve. Here is some of the work I'm most proud of at AWS over the last year:

- [v1.0.0 GA release of the Bottlerocket update-operator](https://github.com/bottlerocket-os/bottlerocket-update-operator/pull/325): this was a pretty huge undertaking. When I first joined the Bottlerocket team, there was a big backlog of things that needed to be fixed in the kubernetes update operator before we could consider it GA. For those curious, the Bottlerocket update-operator, or more affectionately called "brupop", is a kubernetes operator system for automatic and continuous upgrade of Bottlerocket host nodes in a kubernetes cluster: this is great because someone operating a k8s cluster with Bottlerocket nodes will almost always want to consume the latest changes from our distro stream. These upgrades often included security patches and performance improvements. In order to cut this as GA, there were a number of huge refactors that needed to go in (including a deep dive on [enabling mTLS between our API and operator pods](https://github.com/bottlerocket-os/bottlerocket-update-operator/pull/340), [removing a long standing transient dependency on `openssl`](https://github.com/bottlerocket-os/bottlerocket-update-operator/pull/401), and [refactoring massive amounts of code to enable the use of `helm`](https://github.com/bottlerocket-os/bottlerocket-update-operator/pull/350) (which customers really wanted). _Huge_ shoutout to my counterpart, [Tianhao](https://github.com/gthao313), for partnering on this massive achievement with me and the team! I learned alot from working with you!!
- [Vending go modules using the custom Bottlerocket `buildsys`](https://github.com/bottlerocket-os/bottlerocket/pull/2378): the Bottlerocket `buildsys` is a mechanism to build the Bottlerocket operating system artifacts. Much easier said then done: because of Bottlerocket's unique security constraints, use of `selinux`, and containerization paradigm, we had to find ways to consume upstream files (often in RPMs) in a reproducible manner where we could be assured source files had not been tampered with. Several Go modules were used throughout the OS which presented unique contraints when consuming and building those targets. This PR enabled Go modules to be vendored, checked, consumed, and built, all from within our internal build system: while this was early work, it set the stage for my presence on the team. After this, I felt I became the sort of "Go guy" and I often fielded bumping the version of Go we built with when new security releases were dropped, owning a few of our bespoke first party Go modules, working with internal Go teams to get new features and fixes into Bottlerocket when necessary, and much more. Amazon has a very healthy Go ecosystem and I'm excited to see what the teams do with it in the future!

#### Lesson:

Recognize the cinders of burnout before it becomes an all consuming flame. And do what you need to do in your life to find balance.
Sometimes things happen. And you can't always control them: what you can control is how you react.

## Joining OpenSauced

Serendipitously, early in 2023, I had connected with [bdougie](https://twitter.com/bdougieYO),
CEO of OpenSauced, the self proclaimed [_"Beyonce of open source"_](https://thenewstack.io/after-github-brian-douglas-builds-an-open-source-startup/). We chatted a few times and I realized his vision for building tooling and platforms _for_ open source maintainers and enablers was exactly what I'd been missing _in my own_ personal open source contributions and work in AWS open source.

So many times I found myself asking _"who exactly is this?"_ or _"will this project accept contributions ..?"_ or _"is this project's community a welcoming one?"_ when working in open source.

Joining a very early stage startup is something I've always wanted to try: you hear these legendary stories of people in the early 90s and 2000s solving huge problems with technology out of their garages (thankfully, we're not running OpenSauced out of bdougie's garage, we're fully remote!) 

I followed my gut, trusted my instinct, and joined OpenSauced in mid 2023, leaving behind a very good and comfortable job at AWS: what an incredible decision! Since then, I've learned _alot_, been building _alot_ of things, and have shipped a number of big enhancements to our data pipelines, backend infrastructure, frontend, how we approach building metrics and insights around open source contributions, and much more. I deeply believe that in 2024, we will have some incredible things to show off.

Some public OpenSauced work I'm most proud of:

- [Efficiently caching and ingesting git repos](https://github.com/open-sauced/go-api): I've written about this before, but one of the many challenges in building ontop of Git and Git based platforms is how you efficiently pull down new changes from repos (without having to clone the whole thing over and over again. Such a waste!) We needed a mechanism that could introspect individual commits in git repos to then derive insights from: enter the pizza oven, a Go based web server for cloning repos to disc, introspecting commits, and upserting new commits it sees to a database. one of the major efficiency bumps it gets is my implementation of an LRU cache: a caching mechanisms that drops the "least used" member when the cache is full. I could go _very_  deep into this project, but i encourage you to read more about it here:
	- https://dev.to/opensauced/caching-git-repos-a-deep-dive-into-opensauceds-pizza-oven-service-49nf
	- https://dev.to/opensauced/how-we-made-our-go-microservice-24x-faster-5h3l
- [The OpenSauced `pizza` CLI](https://github.com/open-sauced/pizza-cli): OpenSauced isn't just a web app for metrics and insights. It's a software platform that is made to enable people building and consuming in the open. One thing we recognized was missing from our suite of tools is a CLI: the `pizza` CLI is a Go, Cobra based CLI that integrates with the OpenSauced API, bringing deeper capabilities to people who want to integrate OpenSauced into their CI/CD pipelines, scripts, or internal reporting tools.
	- Shipping an OpenSauced Go client: alongside the OpenSauced CLI is [a Go based client for the OpenSauced API](https://github.com/open-sauced/go-api). This enables _anyone_ using Go to build ontop of our API and integrate deeply with our platform.
- Integrating realtime, events driven data into OpenSauced: the cat's _abit_ out of the bag on this one, and there is _so_ much more to come, but I've been heads down over the last month or so shipping new infrastructure and data pipelines to integrate GitHub's realtime events data into OpenSauced. Much of this is possible through the magic of the Timescale time series database: this gives us the power of leveraging Postgres _alongside_ time series events data from GitHub. [Check out the initial integration](https://github.com/open-sauced/app/pull/2293) and be on the lookout for some _really_ incredible improvements to the platform through these new mechanisms.
 
#### Lesson:

In 1994, Jeff Bezos took a huge leap of faith, quit his well paying, comfortable job in New York City [to start Amazon](https://www.aboutamazon.com/news/policy-news-views/statement-by-jeff-bezos-to-the-u-s-house-committee-on-the-judiciary): _"... I decided that if I didn’t at least give it my best shot, I was going to regret not trying to participate in this thing called the internet that I thought was going to be a big deal"_.

Take a leap of faith once in awhile. Trust your gut, take that opportunity, especially if you've always wanted to and it makes sense.

## Cobra

In 2023, along with the help of the amazing Go and Cobra community, [we shipped 2 massive cobra releases](https://github.com/spf13/cobra/releases):
even while taking a break from maintaining Cobra,
I found it deeply rewarding to give back to the community
and continue maintenance of this incredibly important project.

Here are some of my favorite things we shipped in Cobra this last year:

- Support for usage of Cobra as a meta "plugin" framework: many tools, like `kubectl`
  can have "plugins" that you add to the top level CLI. These then get consumed through that top level CLI
  as a nice and comprehensive silo for your `kubectl` needs.
  We did something _very_ similar with the `tanzu` CLI (although we built alot of custom software to make it work),
  this now has much better support directly in Cobra for plugin completions, command paths, etc.
- Completions support keeps getting better: `powershell` 7.2+ is now supported, there's better
  support for `bash`, `zsh`, and `fish`, and we shipped _many_ fixes to improve the overall
  quality of life when using completions and writing CLIs for completions.

Here's to much more cobra joy in 2024!!

#### Lesson:

Taking breaks is a good thing. Come back to what brings you joy.

## Deeper into Neovim

Part of me wondered when I joined AWS if my workflows in Neovim would be able to scale and keep up: TLDR, they did and they still do. Although, it required some continous tweaking.

Here are a few of my favorite little tidbits of neovim goodness from 2023:

- [`mason.nvim`](https://github.com/williamboman/mason.nvim): Mason is what I personally would consider 
  one of Neovim's most important 3rd party projects. It would not surprise me if it eventually was
  integrated directly into Neovim itself. Mason is a sort of manager of editor tooling, primarily
  LSP servers, linters, formaters, and the like. It provides a thin, simple interface for
  installing, managing, upgrading, and integrating with those tools.
  You might not think this is a big deal ("_another_ package manager??"),
  but when you think about the effort and pain of setting up a new neovim environment
  (having to manually install and integrate `gopls` for Go development,
  having to manually install and integrate `cargo` for rust development,
  having to manually install and integrate `eslint` for Typescript development, etc. etc.),
  you realize that there is _alot_ of 3rd party tooling you rely on. Using `mason.nvim` makes it so simple and easy.
- [`oil.nvim`](https://github.com/stevearc/oil.nvim): Many people are familiar with `vim-vinegar`, a `netrw` enhancement for file explorering in vim.
  `oil.nvim` takes that concept and expands on it providing the ability to edit your filesystem
  _in a normal nvim buffer_. For a long time, I had been using a seperate tmux pane to do file system edits
  with `mv`, `cp`, and all the other traditional linux utilities. It was fine, but I really was missing
  the speed and power that `oil.nvim` gives you. This was sort of one of those things I didn't know
  I was missing until I started using it but wow has it enhanced my workflow greatly. Highly, highly recommended!
- [`nvim-llama`](https://github.com/jpmcb/nvim-llama): I built a small, basic plugin that integrates Ollama docker containers (see the LLM section below) into neovim.
  I really love the idea of using _local_ large language models and not ones as part of services:
  maybe it's my dogmatic, Stallman view of open source software and services out in the wild,
  gut this was a great exercise in building a neovim plugin, letting the world know about it,
  and getting some really good feedback to improve its usage.

#### Lesson

Building good habits around things that improve your workflow is an investment I'm still greatly benefiting from.
Take the time to know your tooling very well: these are compounding skills that can be applied to a wide range of disciplines.

## Using LLMs

I was pretty skeptical of AI technology towards the end of 2022:
could Large Language Models and their interfaces, like ChatGPT, really become apart of my workflows?

I think I've surprised myself: in some ways, using LLMs has indeed become a huge part of my workflows.
My original, fear based assumption that this meant I'd no longer be able to write as much code as before was baseless:
it's a tool, just like anything else. And if anything, it's allowed me to write _more_ code.
But I've hit many of the snags with using LLMs: I've gotten some nasty hallucinations and
I've found areas that LLMs just don't know about (for example, in early 2023, LLM's rust knowledge was pretty poor).
Still, I've found it to be a really useful tool and almost essential to quickly discovering new knowledge.

Here's how I used LLMs in 2023:

- Subscribed to ChatGPT plus. A month or so latter, canceled.
- Used Google's Bard on occasion: Google definitely seems to have some of the best training data (this shouldn't be a surprise to anyone).
- Started using [local LLMs with `llama.cpp` ](https://github.com/ggerganov/llama.cpp/) and Meta's Llama 2 and Code Llama models.
- Started using [Ollama](https://github.com/jmorganca/ollama) in Docker for a seamless DX and user experience. Much easier to integrate a docker container.
- Use https://huggingface.co/chat/ to experiment with open source, unfiltered, cutting edge models.

#### Lesson

The biggest shift in my mental paradigm around LLMs is that running them locally
is actually not as bad as you'd think: Apple's newest M chipsets are honestly powerhouses
and I've had amazing results with some of the 7B and 13B parameter models:
I believe the future of open source AI technology is very bright and I hope 
it grows to rival that of major tech companies building this technology on proprietary software.
Long live the open source movement!! And long live open source LLMs!

## Social media

I still don't know what the hell I'm doing with social media: some days it feels like a huge burden,
something I _have_ to do in order to stay engaged with people in the tech communities I'm apart of.

Other days, I feel so thankful to live at a time in history when I can connect with other technologists,
scientist, and engineers around the world seamlessly.

I'm not sure if it's a curse upon society or a blessing: but one thing I've realized,
somewhere through the torrent of tiktok videos I've consumed, at least for me, anything
more than very mild social media consumption is a detriment to my well-being.

I'm certain that being burned out at AWS was in some ways due to my social media use:
it was hard to not doom scroll news about layoffs, the stock market, or the waning tech job field.
It was hard to not see viral posts like _"how I became a 10x engineer"_ or _"how I made 1 million dollars as a software engineer"_. 
Eventually, unconsciously, those words start to change your mindset.
And overall, it just made me discontent: this all reminds me of the famous Theodore Roosevelt quote:

> Comparison is the thief of joy.

#### Lesson

Mass social media consumption isn't good for me. I'm still figuring a balance out, but for now, to start,
I'm limiting social media access on my phone.

---

Here's to many more years!
Good luck in the new year!!

---

{{< comments >}}
