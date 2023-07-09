---
title: "On maintaining spf13/cobra"
date: 2023-06-29
---

There's something I feel like I need to acknowledge
around [my maintainership of](https://github.com/spf13/cobra)
spf13/cobra.

During my time at AWS, I had a _really_ hard time contributing to open source
projects that were important to me (and important to the broader ecosystem).
I didn't have the energy, but more importantly, I didn't have the bandwidth.

There's alot of red tape to get through when it comes to open source at Amazon.
After all, AWS alone is a massive business unit with thousands of products
and tens of thousands of engineers all around the world.

And I totally get it: there's legal & licensing considerations,
there's staffing calculations, there's non-competes, there's product commitments, 
there's other competing companies working on the same projects in the open source, etc. etc.
All this leaves very little room for individual contributors to give back to the community
where they have the autonomy and the power to do so.

Eventually, I did get the _"all clear"_ to work on cobra, but I wasn't given any flexibility
to find time to maintain the project.
Which was fine. I wasn't hired to work on cobra. And it's more or less always been
a "bonus" thing I've worked on.

But it really pained me to not be able to dedicate _some_ time to an important project
within the Cloud Native and Kubernetes ecosystem.
In the last 3 months alone, [cobra's PR velocity has crawled to a standstill](https://insights.opensauced.pizza/pages/jpmcb/363/dashboard), 
there have been only a few merged PRs,
and we've neglected to keep up with triaging new issues.

`spf13/cobra` is abit of a weird project (at least from an _"enterprise open source office"_ perspective).
It's a code library with basically no way to _"product-ize"_ it.
It gives you the Go APIs and frameworks to build modern and elegant command line interfaces.
It's one of those deep dependencies that can go unnoticed for years until something goes terribly wrong
but in itself, it isn't anything you can give to people; you must build on top of it.

I tried my best to pitch my case to get some amount of allocation into the project.
But no luck. Maybe it was the economy, maybe it was the layoffs, I'm really not sure.
Regardless, my allocation didn't change.
Whenever I've tried to explain to a product manager, an engineering manager, or a business person
why leaving cobra to rot risks our entire secure software supply chain,
I'm often met with blank stares.
_"Why work on a CLI framework when you should be working on products?"_
or
_"This doesn't seem critical to our bottom line."_

Compare that to something like core Kubernetes ([_which in itself uses cobra_](https://github.com/kubernetes/kubernetes/blob/c78204dc06d5b0bc02fc2f6bb7dbf98552180d26/go.mod#L62)),
a platform for running and managing containerized workloads and services in the cloud.
Now _that_ sounds like a product you can ship!
You can easily see how AWS justifies having entire teams allocated to maintaining upstream open source projects, like Kubernetes,
when the wellness and maintenance of those projects directly correlates to the bottom line of a product.

But `spf13/cobra` is used _throughout_ several important AWS led open source projects.
Just to name a few:

- [containerd's nerdctl](https://github.com/containerd/nerdctl/blob/29fd529c8a684be58c67c052b4842221542212a7/go.mod#L48)
- [finch](https://github.com/runfinch/finch/blob/f7e091670fb2ac5377423e72f98aa8be33aa41c8/go.mod#L18)
- [eksctl](https://github.com/weaveworks/eksctl/blob/dce1ef8f39223db7ab45419eac0c9b1fdaea7a44/go.mod#L61)
- [the copilot CLI](https://github.com/aws/copilot-cli/blob/5b6f75d457bff8d13563fb6034c5d3b9ce157e39/go.mod#L22)

Maybe the _"engineering allocation chain"_ only goes one or two layers deep.
Not deep enough to notice a dependency like cobra and its lack of maintenance.

In 2022, I gave a talk at KubeCon EU about maintaining cobra with a very small group,
what the "solo" maintainer experience is like, and why solo maintainer projects are incredibly dangerous
to the wellness of the entire ecosystem and broader secure software supply-chain.

{{< youtube YBsDnXXW_d8 >}}

---

I think about that talk alot.
And it keeps me up at night sometimes:
what would it take for a bad actor to hack my GitHub account and inject some
malicious dependency into cobra (therefore poisoning the dependencies in
[Kubernetes](https://johncodes.com/posts/2023/06-13-goodbye-aws/), 
[Helm](https://github.com/helm/helm/blob/03911aeab78290394e589cf7705d3fd542a236c9/go.mod#L32), 
[Istio](https://github.com/istio/istio/blob/36e6875994e53ddb28e86d6a5f13b56ca15a41d3/go.mod#L75),
[Linkerd](https://github.com/linkerd/linkerd2/blob/18755e45cc590c590eedcfa3d30ade09c8b8e7e1/go.mod#L35),
[Docker](https://github.com/docker/cli/blob/d2b376da9256df7d1d0c1fc310db621bd18dc21b/vendor.mod#L35), etc. etc.).
How long before people would notice?
How much damage would be done, even in a short amount of time?

I sort of feel like I've let the cobra and Kubernetes community down.
And I feel like I've become the exact sort of open source maintainer that I cautioned against in that talk:
_distant, difficult to reach, not engaging with the community, jaded, burned out._
But I know it all doesn't rest on my shoulders: there are other people
in the community that keep very close eyes on cobra.
I care deeply about the security and well-being of this project,
but it's clear to me (and probably to you too) that I need a break.

So, what does all this mean for my work on cobra?

Well, thankfully, [I've found a breath of fresh air](https://johncodes.com/posts/2023/06-13-goodbye-aws/)
now working with a small team at [OpenSauced](https://opensauced.pizza/).

And, this summer, while I ramp up with the new job,
I plan to continue to maintain cobra,
but I'm going to take some time away from some of these open source responsibilities.
I think this will give me the rest I need to approach cobra and other projects
with a renewed sense of purpose latter this summer.

I'm taking a trip to Iceland this month where I'll unplug,
read a few books, take some pictures with my camera,
and forget about the world of the secure software supply-chain.

Until then, happy building!

---

If you found this blog post valuable, comment below,
[subscribe to future posts via RSS](https://johncodes.com/index.xml),
or [buy me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}

