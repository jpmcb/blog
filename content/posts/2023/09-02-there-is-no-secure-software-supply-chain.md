---
title: "There is no secure software supply-chain."
date: 2023-09-03T09:00:00-00:00
---

Years ago, entrepreneurs and innovators predicated that
[“software would eat the world”.](https://a16z.com/2011/08/20/why-software-is-eating-the-world/)

And to little surprise, year after year, the world has become more and
more reliant on software solutions. Often times, that software is (or
indirectly depends on) some open source software, maintained by a group of
people whose only affiliation to one another may be participation in that open
source project’s community.

But we’re in trouble. The security of open source
software is under threat and we’re running out of people to reliably maintain
those projects. And as our stacks get deeper, our dependencies become more
interlinked, leading to terrifying compromises in the secure software
supply-chain. For a perfect example of what’s happening in the open source
world right now, we don’t need to look much further than the extremely popular
[Gorilla toolkit for Go.](https://github.com/orgs/gorilla/repositories)

In December of 2022, Gorilla was archived, a project
that provided powerful web framework technology like mux and sessions. Over its
lengthy tenure, it was the de facto Go framework for web servers, routing
requests, handling HTTP traffic, and using websockets. It was used by tens of
thousands of other software packages and it came as a shock to most people in
the Go community that the project would be no more; no longer maintained, no
more releases, and no community support. But for anyone paying close enough
attention, the signs of turmoil were clear:
[open calls for maintainers](https://github.com/gorilla/websocket/issues/370)
went unanswered, there were few active outside contributors, and the burden of
maintainership was very heavy.

The Gorilla framework was one of those “important dependencies”. It sat at the
critical intersection of providing nice quality of life tools while still
securely handling important payloads. Developers would mold their logic around
the APIs provided by Gorilla and entire codebases would be shaped by the use of
the framework. The community at large trusted Gorilla; the last thing you want
in your server is a web framework riddled with bugs and CVEs. In the secure
software supply-chain, much like Nginx and OpenSSL, it’s a project that was at
the cornerstone of many other supply-chains and dependencies. If something went
wrong in the Gorilla framework, it had the potential to impact millions of
servers, services, and other projects.

The secure software supply-chain is one of those abstract concepts that giant
tech companies, security firms, and news outlets all love to buzz wording
about. It’s the “idea” that the software you are consuming as a dependency, all
the way through your stack, is exactly the software you’re expecting to
consume. In other words, it’s the assurance that some hacker didn’t inject a
backdoor into a library or build tool you use, compromising your entire
product, software library, or even company. Supply-chain attacks are mischievous
because they almost never go after the actual intended target. Instead, they
compromise some dependency to then go after the intended target.

The classic example, still to this day, is
[the Solar Winds attack:](https://www.gao.gov/blog/solarwinds-cyberattack-demands-significant-federal-and-private-sector-response-infographic)
some unnamed, Russian state-backed hacker group was able to compromise the internal
Solar Winds build system, leaving any subsequent software built using that
system injected with backdoors and exploits.
[The fallout from this attack was massive.](https://www.nytimes.com/2020/12/14/us/politics/russia-hack-nsa-homeland-security-pentagon.html)
Many government agencies, including the State Department, confirmed
massive data breaches. The estimated cost of this attack continues to rise and
[is estimated to be in the billions of dollars.](https://www.nytimes.com/2020/12/16/us/politics/russia-hack-putin-trump-biden.html)

Product after product have popped up in the last few years to try and solve
these problems: software signing solutions, automated security scanning tools,
up to date CVE databases, automation bots, AI assisted coding tools, etc. There
was even a whole Whitehouse counsel on the subject. The federal government
knows this is the most important (and most critically vulnerable) vector to the
well being of our nation’s software infrastructure and they’ve been taking
direct action to fight these kind of attacks.

But the secure software supply-chain is also one of those things that falls
apart quickly; without delicate handling and meticulous safeguarding, things go
south fast. For months, the Gorilla toolkit had an open call for maintainers,
seeking additional people to keep its codebases up to date, secure, and well
maintained. But in the end, the Gorilla maintainers couldn’t find enough people
to keep the project afloat. Many people volunteered but then were never seen
again. [And the bar for maintainer-ship was rightfully very high:](https://github.com/gorilla#gorilla-toolkit)

> just handing the reins of even a single software package that has north of 13k
unique clones a week (mux) is just not something I’d ever be comfortable with.
This has tended to play out poorly with other projects.

And in the past, this has played out poorly in other projects:

In 2018, GitHub user FallingSnow opened
[the issue “I don’t know what to say.”](https://github.com/dominictarr/event-stream/issues/116)
in the popular, but somewhat unknown, NPM JavaScript package event-stream. He'd
found something very peculiar in recent commits to the library. A new
maintainer, not seen in the community before, with what appeared to be an
entirely new GitHub account, had committed a strange piece of code directly to
the main branch. This unknown new maintainer had also cut a new package to the
NPM registry, forcing this change onto anyone tracking the latest packages in
their project.

The changes looked like this: In a new file, a long inline encrypted string was
added. The string would be decoded using some unknown environment variable, and
then, that unencrypted string would be injected as a JavaScript module into the
package, effectively executing whatever code was hidden behind the encrypted
string. In short, unknown code was being deciphered, injected, and executed at
runtime.

The GitHub issue went viral. And through sheer brute force, abit of luck, and
hundreds of commenters, the community was able to decrypt the string, revealing
the injected code’s purpose: a crypto-currency “wallet stealer”. If the code
detected a specific wallet on the system, it used a known exploit to steal all
the crypto stored in that wallet.

This exploitative code lived in the event-stream NPM module for months. Going
undetected by security scanners, consumers, and the project’s owner. Only when
someone in the community who was curious enough to take a look did this obvious
code-injection attack become clear. But what made this attack especially bad
was that the event-stream module was used by many other modules (and those
modules used by other modules, and so on). In theory, this potentially affected
thousands of software packages and millions of end-users. Developers who had no
idea their JavaScript used event-stream deep in their dependency stack were now
suddenly having to quickly patch their code. How was this even possible? Who
approved and allowed this to happen?

[The owner of the GitHub repository, and original author of the code, said:](https://github.com/dominictarr/event-stream/issues/116#issuecomment-440927400)

> he emailed me and said he wanted to maintain the module, so I gave it to him. I
don't get any thing from maintaining this module, and I don't even use it
anymore, and havn't for years.

and

> note: I no longer have publish rights to this module on npm.

Just like that, just by asking, some bad actor was able to compromise tens of
thousands of software packages, going undetected through the veil of
“maintainership”.

In the past, I’ve referred to this as “The Risks of Single Maintainer
Dependencies”: the overwhelming, often lonely, and sometimes dangerous
experience of maintaining a widely distributed software package on your own.
Like the owner of event-stream, most solo maintainers drift away, fading into
the background to let their software go into disarray.

[This was the case with Gorilla:](https://github.com/gorilla#gorilla-toolkit)

> The original author and maintainer, moraes, had moved on a long time ago.
kisielk and garyburd had the longest run, maintaining a mix of the HTTP
libraries and gorilla/websocket respectively. I (elithrar) got involved
sometime in 2014 or so, when I noticed kisielk doing a lot of the heavy lifting
and wanted to help contribute back to the libraries I was using for a number of
personal projects. Since about ~2018 or so, I was the (mostly) sole maintainer
of everything but websocket, which is about the same time garyburd put out an
(effectively unsuccessful) call for new maintainers there too.

The secure software supply-chain will never truly be strong and secure as long
as a single solo maintainer is able to disrupt an entire ecosystem of packages
by giving their package away to some bad actor. In truth, there is no secure
software supply-chain: we are only as strong as the weakest among us and too
often, those weak links in the chain are already broken, left to rot, or given
up to those with nefarious purposes.

Whenever I bring up this topic, someone always asks about money. Oh, money,
life’s truest satisfaction! And yes! Money can be a powerful motivator for some
people. But it’s a sad excuse for what the secure software supply-chain really
needs: true reliability. The software industry can throw all the money it wants
at maintainers of important open source projects,
[something Valve has started doing:](https://www.theverge.com/23499215/valve-steam-deck-interview-late-2022)

> Griffais says the company is also directly paying more than 100 open-source
developers to work on the Proton compatibility layer, the Mesa graphics driver,
and Vulkan, among other tasks like Steam for Linux and Chromebooks.

but at some point, it becomes unreasonable to ask just a handful of people to
hold up the integrity, security, and viability of your companies entire product
stack. If it’s that important, why not hire some of those people, build a team
of maintainers, create processes for contribution, and allocate developer time
into the open source? Too often I hear about solving open source problems by
just throwing money at it, but at some point, the problems of scaling software
delivery outweigh any amount you can possibly pay a few people. Let’s say you
were building a house, it might make sense to have one or two people work on
the foundation. But if you’re zoning and building an entire city block, I’d
sure hope you’d put an entire team on planning, building, and maintaining those
foundations. No amount of money will make just a few people build a strong and
safe foundation all by themselves. But what we’re asking some open source
maintainers to do is to plan, build, and coordinate the foundations for an
entire world.

[And this is something the Gorilla maintainers recognized as well:](https://github.com/gorilla#gorilla-toolkit)

> No. I don’t think any of us were after money here. The Gorilla Toolkit was,
looking back at the most active maintainers, a passion project. We didn’t want
it to be a job.

For them, it wasn’t about the money, so throwing any amount at the project
wouldn’t have helped. It was about the software’s quality, maintainability, and
the kind of intrinsic satisfaction it provided.

So then, how can we incentivize open source maintainers to maintain their
software in a scalable, realistic way? Some people are motivated by the
altruistic value they provide to a community. Some are motivated by fame,
power, and recognition. Others still just want to have fun and work on
something cool. It’s impossible to understand the complicated, interlinked way
different people in an open source community are all motivated. Instead, the
best solution is obvious: If you are on a team that relies on some piece of
open source software, allocate real engineering time to contributing, being
apart of the community, and helping maintain that software. Eventually, you’ll
get a really good sense of how a project operates and what motivates its main
players. And better yet, you’ll help alleviate the heavy burden of solo
maintainership.

Sometimes, I like to think of software like its a wooden canoe, its many
dependencies making up the wooden strips of the boat. When first built, it
seems sturdy, strong, and able to withstand the harshest of conditions. Its
first coat of oil finish is fresh and beautiful, its wood grains smooth and
unbent. But as the years ware on, eventually, its finish fads, its wooden
strips need replacing, and maybe, if it takes on water, it requires time and
new material to repair. Neglected long enough, and its wood could mold and rot
from the inside, completely compromising the integrity of the boat. And just
like a boat, software requires time, energy, maintenance, and “hands-on-deck”
to ensure its many links in the secure software supply-chain are strong.
Otherwise, the termites of time and the rot of bad-actors weaken links in the
chain, compromising the stability of it all.

In the end, the maintainers of the Gorilla framework did the right thing: they
decommissioned a widely used project that was at risk of rotting from the
inside out. And instead of let it live in disarray or potentially fall into the
hands of bad actors, it is simply gone. Its link on the chain of software has
been purposefully broken to force anyone using it to choose a better, and
hopefully, more secure option.

> I do believe that open source software is entitled to a lifecycle — a
beginning, a middle, and an end — and that no project is required to live on
forever. That may not make everyone happy, but such is life.

But earlier this year, people in the Gorilla community noticed something:
a new group of individuals from Red Hat had been added as maintainers to the Gorilla GitHub org.
Was Red Hat taking the projected over? No, but ironically, the emeritus maintainers
had done exactly what they promised they would never do: at the 11th hour, they handed
over the project to people with little vetting from the community.

> To address many comments that we have seen - we would like to clarify that
Red Hat is not taking over this project. While the new Core Maintainers all
happen to work at Red Hat, our hope is that developers from many different
organizations and backgrounds will join the project over time.

Maybe Gorilla was too important to drift slowly into obscurity and Red Hat
rightfully allocated some engineering resources to the project.
Gorilla lives on. Here's hoping the code is in good hands.

---

If you found this blog post valuable,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}
