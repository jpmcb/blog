---
title: "An elegant social media network for a more civilized age."
date: 2023-01-28
images:
  - /images/lite-saber-meme.png
---

![Mastodon, an elegant social media network](/images/lite-saber-meme.png)

_"This is the social media network of a software engineer.
Not as clumsy or random as Twitter; an elegant network for a more civilized age." ― obi-wan kenobi_

---

Over the last week, I've abandoned my Twitter account
in favor of diving head first into the world of Mastodon and the _"Fediverse"_.
So far, it's been a surprising, delightful, and enriching experience.

By the time I moved to Mastodon,
I had some _3,000_ followers on Twitter.
But the platform has atrophied and changed in many sad ways.
Long gone are the days of fun technical deep dives,
inside scoops on your favorite projects,
and starting conversations with your technical peers.
Engagement (at least for me. Maybe I'm very boring?)
is way down and the platform itself is breaking:
I haven't been able to reliably access my DMs for the better part of a week.

Instead, tech Twitter has been left with an exorbitant amount of _"influencers"_ saying things like:
_"As a developer, how many hours do you sleep"_,
_"10 reasons Next.js is the best thing since sliced bread!"_,
and _"How to get your first tech job in 6 months!"_.
All shameless attempts to groom the _all seeing algorithm_ in their favor.

For me, the interesting conversations had stifled and it was time to try something else.
Enter Mastodon; the blessed successor to many's beloved Twitter of a forgotten era!

Mastodon is abit weird though.

For one, there's no _"algorithm"_.
It's just a sequential timeline of stuff from people you follow.
For some who grew up in the age of never ending, dopamine dumping,
slot machine scrolling, _this might take awhile to get used to._
But what you'll find instead is real _conversation_
and the ability to engage with those _people_ directly.
I don't miss the days of inflammatory content designed to artificially drive up engagement.
I'm happy it's been replaced in my social media life with a slower, more intentional feed.

In that same vein, you'll also notice that Mastodon is not a centralized place where _everyone_ gathers
to share their hot takes. Instead, it's _"federated"_ which means there are
_many different_ Mastodon servers and services.
You can then _crawl_ these different
server instances to connect with a distributed network; they're all interlinked.
So, if I have an account on _"server A"_,
I can still search, follow, and see content from people on _"server B"_.
All of _my content and information_ lives on _"server A"_, but through the magic of the
internet and graph theory, a massive number of Mastodon servers can come together
to create _the great "Fediverse"_; independently hosted and maintained servers
that can all communicate together.

Or not.

It's also completely plausible to have a small Mastodon instance that is cut off from the Fediverse
where only people internal to that instance can interact with each-other.
That's the joy of open source technology
that you have the power to own, modify, and dictate the direction of.

To get started, you'll need to find a server that you want to join.
I picked [fosstodon.org](https://fosstodon.org) 
since its main focus is supporting people in the open source community.
Browsing the list of [indexed servers](https://joinmastodon.org/servers)
is a great way to start and find a place that makes sense for you to call home.

### Finding your Twitter network

When you first get started, it can be hard trying to find people,
especially if you're coming from a large network on Twitter.

One of the Fediverse's biggest downfalls is a lack of an efficient and sensible search.
Because there could be any number of different web publishing platforms linked into the broader Fediverse,
there's no good way to index, search, and serve all of that distributed content at once.

Thankfully, there are a few handy tools to make this transition easier!
My favorite is [Twitodon](https://twitodon.com/). You sign in with your Twitter,
sign in with your Mastodon account, and it crawls your Twitter following to find
people in your existing network who have a Mastodon account. Then, you can export a CSV of your network
and import it directly into Mastodon!
(Don't forget to revoke Twitodon's access to Twitter and Mastodon once your done.
Thankfully, they provide the steps necessary to do that).

### User experience

The default Mastodon user interface and experience is not amazing.
And who can blame them.
Mastodon is a non-profit foundation building the open source platform
and hosting some of the biggest instances for pennies on the dollar.
They probably have more important things to worry about ([like if they should support quote Toots](https://github.com/mastodon/mastodon/issues/20673)).

But, because the Fediverse is a thriving space full of tinkerers and hackers,
I have a few recommendations on taking your Mastodon experience to the next level:

#### Ivory

[Tweetbot was a favorite Twitter client for many people.](https://tapbots.com/tweetbot/)
Tapbots, the duo who created the iOS application, lovingly curated a _delightful_ Twitter user experience.
But suddenly, a few weeks ago at the beginning of January,
Twitter shut down third party client access to the API.
And in one fell swoop, Tweetbot was no more.
Instead of sulking, Tapbots _immediately_ got to work shipping their Mastodon iOS client, [Ivory](https://tapbots.com/ivory/).

And wow.

Before Ivory, Mastodon didn't really _"make sense"_ for me.
Now, it's everything I hoped for; a beautiful user interface,
customizable buttons and actions, and notifications that actually work.
Even in it's _very early_ access state, it's still a massive accomplishment.

For an iOS client (sorry Android people), I couldn't recommend Ivory enough.

#### Elk.zone

So, what about a web client?
Well, let me introduce to you [Elk.zone](https://elk.zone),
a web client from members of the core [Vue.js](https://vuejs.org/) team.

And it's really, _really_ good. I would argue maybe even better than the Twitter web client user experience.
It's intuitive, it makes tons of sense, it has native lite and dark mode, etc.
And I shouldn't be surprised; anytime I come across a page built with Vue,
I'm always impressed by the framework's output.

Huge shout out to this small team for accomplishing so much in such a short time!

---

In short, as Twitter falls apart, there is a lovely home for you somewhere in the Fediverse.
It's growing day by day. And with lots of people tinkering on the platform,
it's user experience, features, and possibilities will only continue to thrive from here.
I can't wait to see you there and start a conversation:
https://fosstodon.org/@johnmcbride

---

If you found this blog post valuable,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}
