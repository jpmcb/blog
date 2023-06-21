---
title: "So long AWS! Hello OpenSauced!"
date: 2023-06-13
---

This is my last week at Amazon Web Services.

And while the last year has been an incredible journey diving deep into the world
of containers, Linux, Rust, Kubernetes, and building operating systems,
I've made the difficult decision to leave.

And I'm very excited to be joining the incredible team at [OpenSauced](https://opensauced.pizza/)
where we'll be building the future of open source insights, tooling, and innovation.

Since before I started my computer science degree (almost 7 years ago!)
I've wanted to cut my teeth on building a startup and working on a greenfield product.
These kinds of opportunities are far & few between.
And the timing is _just about_ right;
I can't wait to push myself, learn new skills, and build new products 
with a small, amazing tactical team.

To everyone at AWS and the Bottlerocket team:
Thank you for making the last year an incredible learning opportunity. 
Here's hoping our paths cross again soon!! I'm sure I'll be seeing you around
the open source ecosystem! Cheers!

---

If you're curious, here are a few personal reflections on the last year:

### ***The power of grit.***

There's something really incredible about a team of engineers dedicated to
creating wonderful customer experiences
(especially when there is alot of really challenging work to get done).

You could have the most talented people in the world working on your products
but when the times get tough, tapping into the grit superpower trumps all others.

What is grit?

Personally, I've found it to be the _resolve_ to do what needs to get done.
At times, no matter what.

I was constantly impressed with what could be accomplished at AWS through sheer
motivation and grit. It was pretty rare to hear _"it can't be done"_ or _"I can't do this"_.
More often then not, you'd hear _"how can we do this for our customers?"_
or _"what needs to change in order to accomplish this"_.

### ***The open source movement is alive and well.***

Just before joining Amazon, I was coming hot off of 
Tanzu Community Edition at VMware, an open source Kubernetes platform aiming to
be a simple and easy entry point for VMware customers to get introduced to the cloud
native ecosystem.

Unfortunately, sometime after the Broadcom acquisition was announced, the entire
project was scrapped. After well over a year of stealth development, user research,
release, community support, and more, all our effort essentially resulted in ... nothing.

To say the least, it left me abit skeptical (and sad) about the whole open source
_"idea"_. Maybe you could call it a crisis of faith. Did it really make sense
to ship software and platforms for free? Does it make for any kind of sustainable
business?

But while at AWS, I saw amazing projects get iterated on,
injected with critical engineering resources,
shipped,
and improved through Amazon's open source
office. Things like
Bottlerocket,
Containerd,
Finch,
and many more.

To say the least, the open source movement is strong and there's so much
awesome innovation happening out in the open wild.
I'm energized and hopeful for the future of the free _(as in freedom, not as in beer)_
and open movement.

### ***When customers give you lemons, make lemonade.***

The "first principle" of working at Amazon is to be customer obsessed.
And being customer obsessed means you should make all efforts to work backwards
from the customer needs.

I'm not gonna lie: at first, I really thought the whole "peculiar culture" and 
"customer obsessed" mantras were a load of b.s.

But seeing real customer's needs get met on a daily basis by the team was a pretty
incredible thing: there was little question on what the team was delivering and why.
It always revolved around the customers.

From an individual contributor perspective, that is incredibly empowering to be able
to hear of a real customer issue or need, make changes to address it, and ship it without question.
In software, customer needs, request, and issues are like lemons.
And when you have an abundance of lemons, make lemonade.

### ***Want to be understood? Write a document.***

Before working at Amazon,
I really, _really_ underestimated the power of technical writing.

Almost all decisions at Amazon get made through a well written document that
clearly lays out the narrative for how a decision should be made.

Writing has never been something I felt _"good"_ at.
In fact, for years throughout primary school, I struggled with reading
and writing _(see my previous post on using the spell checker in Neovim for
an understanding on how critical these tools are for me)_.

The first few docs I wrote were not well written docs (party because of how much
I underestimated how important writing is).

So, one of my biggest goals in 2023 became to become a better writer. Part of that
has been writing on this blog.
But more importantly, my _"goal within a goal"_ was to be better understood,
more organized with my thoughts, and in the end, be a better engineer.

Don't underestimate the power of writing: it's how you communicate technical
ideas.

### ***Make it boring. Making it scale. Make the right decision.***

New, sexy tech often gets in the way of building great customer products.

Amazon (maybe notoriously?) doesn't adopt new tech fast. And for good reason:
there are droves of engineers and existing stacks that couldn't possibly scale
to adopt new tech constantly.

And what customer needs is all this new tech really servicing?
If we already have well understood tools and stacks, why adopt something new
unless it really meets some customer need? _(see above on working backwards from the customer)_.

Early on, the Bottlerocket team adopted Rust for first party source code. And
for very good reasons: it provides unique memory safe capabilities and performance, there was an
existing paradigm of using rust at AWS, and among many other reasons,
writing new C or C++ systems code may not be unacceptable from a security standpoint.

When I first arrived on the team, my naive assumption for why Rust was adopted
was because it happens to be the cool new kid on the programming block.

When, in reality, adopting this new tech serviced some real customer and product needs.
In short, keeping the customer at the center of all decisions, including what tech gets adopted,
will continue to give you better and better results.

---

_So long AWS, and thanks for everything!_

---

If you found this blog post valuable, comment below,
[subscribe to future posts via RSS](https://johncodes.com/index.xml),
or [buy me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)
Your support means the world to me!!

---

{{< comments >}}

