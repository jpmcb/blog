---
title: "Revisiting the Core-JS Situation"
date: 2023-04-22
---

Weeks ago, Denis Pushkarev, the author of core-js, published [“So, what’s next?”](https://github.com/zloirock/core-js/issues/1179).
While a long and lengthy stream of consciousness on the state of the project, I
believe it is something that anyone and everyone who interacts with open source
software should read. It chronicles an emotional tale of his passion project,
distrust & hate for him, his seemingly selfless solitary quest for a better web,
and a plea for financial assistance.

[The post rightfully went viral](https://www.reddit.com/r/programming/comments/111k9aq/corejs_maintainer_so_whats_next/)
and [donations started flowing in](https://www.patreon.com/zloirock).

However, boiling just under the surface, much like any other large open source
project with a solo developer, there are some real and scary implications to
this entire situation.

_But first, what is core-js?_

After all, the project is at the center of this discussion, so it’s worth
understanding it deeply. Core-js is a JavaScript library that focuses on
providing cutting edge web APIs, standardization, and _“polyfills”_. At the time
of this writing, it has over 50 thousand dependent projects and some 40 million
downloads _weekly_ on NPM, a popular JavaScript module hosting service.

In short, it’s _the_ JavaScript glue for web applications.

It enables modern JavaScript to work on an array of different browsers,
including Internet Explorer. And it constantly tracks the latest web standards.
This way, JavaScript developers can take advantage of the latest and greatest
ECMAScript standards, ensuring interoperability of web pages and applications
across different browser platforms. Things like collections, iterators, and
promises can simply and easily be used through the core-js polyfills. All
without having to re-invent the wheel and worry about broken builds across the
many different browsers and JavaScript interpreters.

Like any project that attempts to implement a “standard”, this also means that
it’s a “living” project; without constant update, which usually requires
interplay with the upstream browsers and web-standard-setters, core-js would
quickly fall apart. One small change in a web browser’s JavaScript interpreter
without an update to core-js could mean a whole swath of web applications stop
working and break.

And for years, the project has existed in the depths of front-end dependencies,
where Denis worked tirelessly. Many projects consumed core-js, usually not
directly, but rather, somewhere in the nether of the NPM dependency hellscape.
Its code, at least indirectly through dependency poisoning, is used almost
everywhere. Massive multi-billion dollar companies like Apple, Amazon, Netflix,
and many more have it embedded somewhere in their front-end dependency chains.

To say the least, it’s a really important project used by nearly every
front-end.

So when did the trouble start? Around 2018, if you tried to NPM install core-js
(or a project that depended on core-js), you would be greeted with the following
message after the installation:

```
Thank you for using core-js for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open
Collective or Patreon ...

Also, the author of core-js is looking for a good job
```

While, admittedly, this was a fairly unconventional way to ask for support, it
was a heartfelt attempt by the author to find financial means for a project he
believed was worth all his time. Many in the JavaScript community did not
[respond well](https://github.com/zloirock/core-js/issues/548).
So much so that 
_“the author of some library is looking for a good job”_
[sort of became a meme unto itself](https://github.com/zloirock/core-js/issues/708).

At this point, many in the JavaScript, front-end open source community should
have looked a little closer and seen the potential disastrous future incoming;
the author was in financial trouble (“the project needs your help!”) and the
author was taking extreme measures to find any financial support (by adding a
very unconventional message embedded [within a post-install script](https://github.com/zloirock/core-js/blob/381c366b8cdc84050bb0ef7184a6e80f45bf5903/packages/core-js/scripts/postinstall.js).
But instead
of responding accordingly by financially supporting the project, adding
additional maintainers, forking the project, or moving it to a foundation, the
broader JavaScript open source community instead turned to slander and hate;
Denis received numerous distasteful comments in the core-js repository, via
email, and everywhere else he had a presence online.

In 2019, as a response to a growing number of projects using the
post-install-script as a way to raise funds and advertise their commercial
product, NPM made the unilateral decision to
[ban post install console output that included “ads”](https://github.com/zloirock/core-js/issues/635).
This impacted core-js and removed Denis’s plea for support.

His response:

> If NPM will ban the postinstall message, it will be moved to browsers console.
> If NPM will ban core-js - it will cause problems for millions of users. I warned
> about it.

[_And what was that warning?_](https://github.com/zloirock/core-js/issues/548#issuecomment-510684777)

> If for some reason will be disabled ability to publish packages with this
> message - we will have one more left-pad-like problem, but much more serious.
> And after that 2 options - or core-js will not be maintained completely, or it
> will be maintained as a commercial-only project.

> Yes, I am ready to kill it as a free open source project, if it will be required
> by the protection of my rights.

Through these warnings that attempted to appear genuine on the surface but
really, were just thinly veiled threats, Denis was making it clear to anyone
looking closely enough that he was more than ok nuking the project out of
existence (or at least, hard pivoting it to a commercial product).

_But what is left-pad?_

And what does it have to do with core-js anyways?

Left-pad was a very small JavaScript library authored by Azer Koçulu. It was
only 11 lines of code long and added additional white space to the beginning of
a string (or in other words, it would “pad” the left side of a string).

And much like core-js, it was also distributed through NPM
_(I’m seeing a common theme here …)_. After a legal dispute with NPM over the name of Azer’s package
“kik” (a different side project which happened to also be the name of a popular
messaging app), Azer removed all of his packages from NPM. Suddenly, in one fell
swoop, across the world, JavaScript developers started seeing errors when
building their projects:

```
npm ERR! 404 ‘left-pad’ is not in the npm registry.
```

Almost no one knew what the “left-pad” module was or what it did. And it didn’t
even really matter. Somehow, through the swamp of NPM dependency chains,
left-pad had become a project with 10s of millions of downloads a week and
thousands of dependent projects. Azer effectively “broke the internet” by
removing his packages that happened to be used across many other packages (and
those packages used by other packages, so on and so forth).

Some time latter, [in emails that were widely published](https://arstechnica.com/information-technology/2016/03/rage-quit-coder-unpublished-17-lines-of-javascript-and-broke-the-internet/), Azer wrote:

> I want all my modules to be deleted including my account, along with this
> package. I don’t wanna be a part of NPM anymore. If you don’t do it, let me know
> how do it quickly.

> I think I have the right of deleting all my stuff from NPM.

Yes, it is well within the rights of a package owner to remove their packages
from the NPM registry. They are, after all, just pieces of open source software,
freely distributed with no contract to their working order; often a fact that
corporate consumers of open source software forget.

By invoking the name of “left-pad”, Denis insinuates that he has considered
following in Azer’s footsteps and doing the same. Although, the impact would
likely be far greater.

_What about commercialization?_ Instead of completely obliterate the project, why
not start selling licenses for it? Or somehow turn it into a product.

I find this unlikely. If Denis, a Russian national, commercialized the library
over night, it would essentially have the same effect as deleting it: core-js is
used by thousands of large businesses around the world, and if they suddenly had
a Russian corporate dependency 
([where there are currently many sanctions, including against “advanced technologies”](https://www.state.gov/the-impact-of-sanctions-and-export-controls-on-the-russian-federation/)),
this would force drastic action to
remove core-js from any and all front end dependencies. More likely than not,
NPM themselves would remove the package if this hard pivot was made. If I had to
guess, this is why Denis has not yet attempted to commercialize core-js; it
would destroy a library he is passionate about without providing him the
financial windfall he desires. A lose, lose situation.

But this is a sort of _“Tale of Two Cities”_ - despite the clear and present
danger the project was in and regardless of veiled threats leveraged against the
community by it’s sole maintainer, JavaScript developers disregarded this risk,
big businesses consumed it as a deeply integrated dependency, and everyone
increased their usage of the library, ignoring a potentially worsening
situation.

And, unfortunately, things did get worse.

Sometime in 2019-2020, Denis found himself in prison. And the core-js project
went dark.
[Many found themselves asking _“What happened?”_](https://github.com/zloirock/core-js/issues/767),
_“What’s the state of this project?”_, and _“Is there any governance?”_:

> The JavaScript community should be a bit concerned because @zloirock looks like
> to be the “only” maintainer. Does somebody else have admin privileges to write
> on this repo? Publish on npm and make this project not to die?

Compounding a risky situation, Denis had made himself the sole maintainer of the
GitHub repository, despite frequent requests to donate the project to a
foundation or to add others with administrative privileges. At the time, and
still to this day, he had no interest in giving up authority over the project.
This means that during the time of Denis’s absence, there were no changes. No
security fixes. No new features. No commits to the main branch.

The project, for all intents and purposes, was dead.

Yet, still, the open source community and many multi-billion dollar companies
did nothing. They didn’t attempt to mitigate the risk of using this critical,
solo maintainer project and no alternatives emerged. Funny enough, at the time,
the usage actually increased, by some estimates, to over 25 million downloads a
week.

In the lifecycle of “important” projects, once they die or their sole maintainer
abandons them,
[usually a prominent fork emerges from within the community](https://github.com/ryanelian/ts-polyfill/issues/4#issuecomment-599227863):

> Babel maintainer here 👋 We are probably not going to fork core-js because we
> don’t have enough resources to maintain it.

Unfortunately, despite many requests, one of the most qualified JavaScript
organizations in the entire ecosystem, Babel, who had worked closely with Denis
and core-js in the past, would not take the onerous of protecting their secure
software supply-chain by forking core-js. Either because core-js was too
complicated, they truly didn’t have allocations, or there was existing bad blood
with the project, no useful alternative to core-js emerged.

And unfortunately, at the point when a critical, solo-maintainer, open source
project becomes so complex and so intertwined with the foundation of your
product, you’ve effectively “lost”. When it becomes impossible to fork,
maintain, or contribute back to the upstream project, you’ve effectively entered
a deadlock hostage situation. Providing community support becomes impossible,
yet, your software’s well-being is now directly linked to a solo maintainer
who’s incentives are completely out of your control. One day on their own
volition, they may up and abandon the project, leaving you the impossible task
of picking up all the pieces.

At this point, major JavaScript organizations like NPM or the V8 engine team at
Google should have recognized the problem, stepped in, forked the project into
an organization with a community, and enabled people to start contributing back.

But Denis has never wanted to give up core-js to the community - he’s fought
back on allowing others to have administrative privileges, he doesn’t enable
others to make large meaningful contributions, and he won’t share the burden of
shepherding an important project.
[He’s only ever seen two potential futures for core-js](https://github.com/zloirock/core-js/issues/139);
make enough money (through donations or a job) to work on core-js full
time or let it die. Any requests from Denis for outside contributions are
general asks to report issues, improve testing, and write better documentation.

If I had to criticize Denis for something, it would be this deliberate decision
to castrate his open source community. The overwhelming majority of the over
5,000 commits to the repository are exclusively from Denis, mostly committed
directly to the main branch; no pull requests, no discussion, no feedback, just
straight to the mainline. And a great open source leader should eventually
evolve beyond making code contributions; they should be effectively delegating
tasks to the community, grooming the backlog, discussing proposals with
community members, creating safegaurds to ensure the safety & security of the
software assets, and guiding the general direction of everything. Core-js never
evolved past a simple pet project. Yet, to this day, the JavaScript ecosystem
treats core-js like it’s a well maintained project with the support of an entire
community. In reality, it’s one person with all the power making all the
decisions and pushing all the changes.

This, finally, brings us to this week: Denis is out of prison. He appears to be
in insurmountable debt to some Russian authority. And he publishes his call for
financial assistance
[directly to the core-js README](https://github.com/zloirock/core-js).
It’s a harrowing story. A
story that I believe it, fills me with sympathy but also scares me.

Denis ends his writing with the following, quoted at length for brevity:

> This was the last attempt to keep core-js as a free open-source project with a
> proper quality and functionality level. It was the last attempt to convey that
> there are real people on the other side of open-source with families to feed and
> problems to solve.

> If you or your company use core-js in one way or another and are interested in
> the quality of your supply chain, support the project

_Again, his final statement:_

> If your company uses core-js and are interested in the quality of your supply
> chain, support the project

is not the crescendo of someone asking for help. This is, like before, a thinly
veiled threat. And this time, it’s a threat against the security of the
JavaScript supply chain at large.

If you know anything about me, you know that the secure software supply-chain is
a topic I am deeply passionate about. I believe it is the most important
technological hurdle of our modern area and I believe is at incredible risk.
There are many avenues to disastrous supply chain attacks, but widely used
projects that have solo maintainers are probably the largest and most blatant
risk of them all. They’re sort of like unicorns, difficult to believe they’re
real, but here we see one; a solo maintainer project that Amazon, Netflix,
Apple, LinkedIn, PayPal, Binance, and tens of thousands of others have a
dependency on.

Worse yet, [through Denis’s own words](https://github.com/zloirock/core-js/blob/master/docs/2023-02-14-so-whats-next.md#accident),
we can now clearly see the massive financial trouble he is in:

> I received financial claims totaling about 80 thousand dollars at the exchange
> rate at that time from “victims’” relatives. A significant amount of money was
> also needed for a lawyer.

And for a solo maintainer who has administrative, force push powers on a very
complex, very popular software library, that few other people understand, his
claims are a troubling reality. In the worst case, he could easily embed a
malicious piece of code deep in the commit log and publish a new package to NPM
for his financial gain. But more realistically, I worry for his safety; someone
    with crushing debt who presides over an incredibly valuable technological
    resource with little oversight is a prime target for state-sponsored hacker
    groups.

Ironically, to this day, many well respected security and supply-chain companies
would call core-js “healthy”. Snyk, a developer security platform company,
[gives core-js a score of 94/100](https://snyk.io/advisor/npm-package/core-js)
noting it’s “Popularity” rating as a “Key Ecosystem
Project” and its “Maintenance” rating as “Healthy”. I personally find this
surprising given the years of solo maintainership of core-js, refusals by that
sole maintainer to donate the project to a reputable organization, where that
solo maintainer disappeared for well over a year, where threats of extinguishing
the project were levied against the community, and where financial problems have
been a reoccurring theme since the project’s conception.

Still, potentially worse, are the thousands of massive companies that saw no
problem freely commercializing Dnnis’s work despite the clear call for help.
Again, this is a sort of _“Tale of Two Cities”_: Denis should be criticized for
how he’s handled the open source community around his project but the software
industry should be equally ashamed of how they’ve turned their back on this
maintainer and their own software stability.

_“This all sounds bad. What do I do?”_ Here are my recommendations for consumers
of core-js:

* Make a financial contribution - To start with, show Denis your support for the
solo work he’s done on core-js and the incredible functionality he’s brought to
the web. It’s the least we, as a software ecosystem, can do.

* Pin your core-js dependency - While not a long term solution, pinning your
dependency will keep you from consuming potentially malicious upstream changes
that get made to new versions of core-js. Generally, it’s not a great idea to
blindly take every new package or track “latest”. You should attempt to
independently verify critical projects and packages you consume, pinning to the
ones that pass your screening.

* Cache versions of core-js you do rely on - In general, it is a mistake to
blindly take dependencies from upstream package registries. In other words, you
should never install an NPM package directly to your production environment. You
may find yourself in a “left-pad” situation where a module owner one day decides
to remove that package from the face of the earth. Or worse, where the package
owner publishes a new malicious package under the same version that flows down
to consumers. Those packages should, instead, be installed through a cache that
you and your security team have independently created, validated, and control.
Yes, this is another service you’d be running internally, but it’s well worth
the cost in order to mitigate an entire class of supply chain attacks.

* Raise this concern with your CISO - Chief Information Security Officers are
tasked with tracking, monitoring, and assessing the risk to all security vectors
your company may be vulnerable to. It’s clear that Denis is in financial
trouble. That, compounded with the fact that he has admin access to force push
onto the main branch and unilaterally publish new packages, should be
concerning. Work with your security team and CISO to determine the threat level
of this risk and what impact it has on your code bases.

* Get involved with the project - I’ve generally advocated for this in the past.
And while core-js appears to be a difficult project to get involved with, there
are still issues on GitHub you can raise, a few pull-requests to be commented
on, and the commit log to be validated. If it’s a critical project to your
company, spend the time, money, and engineering resources to protect your
companies assets by getting involved.

* Find a reputable alternative and move to it - This is the best long term
solution. But would require significant engineering resources.

A quick note on making a financial contribution - by donating, you are not
supporting a project. You are not providing funds to a well defined
organization. And you are not entitled to technical support. You are simply
bank-rolling an individual. An individual who has brought massively useful
usability features to the web and JavaScript developers. Someone who needs help.
Someone who, at will, on their own volition, may abandon the project, inject
malicious commits deep into the commit log, or outright sell their GitHub
account to nefarious third parties. And for a massively critical project like
core-js, this is a terrifying solution to propose: “just pay him and forget
about it” won’t fix the problem in the long term and will never scale. If
anything, it may exacerbate the problem by enabling a single, solo developer to
keep working on a critical piece of web infrastructure by themselves. In that
scenario, [the bus factor](https://en.wikipedia.org/wiki/Bus_factor)
is still 100%; Dennis working on the project alone means
that at any time, he could disappear again and leave the project to rot.

If you’ve read this far, I hope you understand why I am worried for core-js’s
future. And yet, I am also sympathetic to Denis’s plea. Commercialization of
free and open software by large, multi-billion dollar companies has gone
unchecked for decades. Denis worked tirelessly for years to provide what he
believed to be a good solution to a massive problem on the web. And the
ecosystem took advantage of that, using his project with little recognition.
While I disagree with and criticize some of his decisions, in the end, it is his
project, it’s just gotten out of control and is used everywhere. He has every
right to do with it what he wants.

But that’s also the beauty of open source software. Denis could completely
disappear tomorrow and there would be zero real world consequence to him doing
that; most open source licenses indicate that the software is provided “as is”
with no support, no contract, and no assurance of its good working order.

It also means that anyone can fork the project and maintain it themselves. If
there’s anyone to be ashamed of, it’s the JavaScript open source ecosystem that
perpetuated an increasingly bad situation for too long.

Now is the time to step up. Now is the time to support Denis. Now is the time to
fork core-js. Now is the time to prevent another “left-pad-like problem”.
