---
title: "What to do with rotting software?"
date: 2023-12-03
---

If you were to visit one of the biggest React drag and drop libraries on GitHub,
[Atlassian’s react-beautiful-dnd](https://github.com/atlassian/react-beautiful-dnd), 
you would be greeted with the following message:

> This library continues to be relied upon heavily by Atlassian products, but
> we are focused on other priorities right now and have no current plans for
> further feature development or improvements.

The project has over 30 thousand stars on GitHub, “used by” over 97 thousand
dependent codebases, and is downloaded [over 1 million times _per week_ on NPM](https://www.npmjs.com/package/react-beautiful-dnd).

> It will continue to be here on GitHub and we will still make critical
> updates (e.g. security fixes, if any) as required, but will not be actively
> monitoring or replying to issues and pull requests.

This message was last updated well over 2 years ago, in October of 2021.

And what security fixes have landed in the library? Well, the last update to the
“package.json” was over a year ago, in August of 2022. There are 20 some odd
Dependabot pull requests unaddressed. And a quick “npm audit” of the library
shows 109 vulnerabilities; 1 low, 39 moderate, 51 high, and 18 critical (as of
the time of this writing).

Those familiar with contributing to open source software know this as project
“rot”: when something goes unmaintained and accumulates cruft over time.

> We recommend that you don’t raise issues or pull requests, as they will not
> be reviewed or actioned until further notice.

Or in other words, this library still exists, is depended on by legitimate
products, can be easily consumed via NPM, but is ostensively dead. A rotting,
zombie library that may spread dangerous vulnerabilities to downstream
consumers, unaware of the potential risks they’re bringing into their codebases
and exposing to their customers.

---

What should we do with libraries like react-beautiful-dnd?

_“Completely deprecate it, archiving in the process?”_ Atlassian, and often times
many other big tech companies, simply can not do this nor afford it. There may
be deep internal dependencies that could take months of engineering effort to
move tools off of. There could still be products (and customers) with long
support cycles that rely heavily on the libraries functionality for many years
in the future.

Or maybe the maintainers and teams that created the projects have moved on or
left the company: the burden on the business to maintain open source software is
often a neglected aspect of the FOSS movement. It can be very challenging to
find the right engineering allocation for projects like this.

Deprecating and removing open source projects can also backfire in really big
ways: giving a long notice to the community that the project will officially end
and no longer be made available after some point can be a massive disruption to
dependent, downstream projects. The supply-chain rot will end and unknowing
consumers of potentially critical vulnerabilities can choose something better,
but you essentially shatter the chain of dependencies forcing those downstream
projects to spend potentially critical engineering cycles on desperate efforts
to update packages before a dependency vanishes. Or, in the worst cases,
deprecation unknowingly break functionality for entire products, pipelines, and
businesses.

Software rarely exists in an isolated space: it almost always lives and breathes
within an ecosystem. And deprecation can be an incredibly disruptive to that
delicate, living balance.

_“Well, can’t they just maintain it then?”_ This is clearly an ideal solution for
the software supply-chain and the wider software ecosystem, especially with
existing business dependencies, but this may not be possible depending on
engineering allocations, headcount, if the original creators are still at the
company, interest within the engineering org, etc. For some companies, all this
engineering overhead is simply off the table due to cost and prioritization.

_“Lift and shift to something else?”_ For most people consuming a library like
react-beautiful-dnd, this seems to be the best choice: there are many drop in
replacements that work well, like dnd-kit. But it might not be that simple: what
if there are custom patches that you rely on that have not yet been upstreamed?
What if drop in replacements aren’t truly “drop in” and require more work to
pass tests and product requirements?

_“Ok, I really need this one though. Fork it?”_ Another excellent choice, but a
costly one: in and of itself, forking comes with alot of engineering overhead.
To fork and maintain a large React component library is not a trivial task. But
that’s sort of the beauty of open source software: if you or your business
really depend on this library, given that the license is permissive enough, you
can always fork the project, fix it, or enhance it in any way you see fit.

_“If they can’t maintain it, forking requires too much overhead, and deprecating
is too disruptive, just leave it as is and let it rot?”_

This seems to be the only unfortunate choice left to leadership of many
engineering orgs. It’s a sort of “between a rock and a hard place” decision. And
I personally don’t think this is necessarily a bad choice: all software has a
lifecycle and is inevitably destined to be re-written. Even the original creator
of the react-beautiful-dnd library agrees:

> Over time I am more comfortable with the notion that all software has a
> shelf life and that it's okay for open source maintainers to discontinue
> working on projects if they choose to 

I deeply empathize with any open source maintainers that face such a decision:
it’s not easy to find yourself with new priorities, reduced headcount, or tricky
dependency chains.

---

The provenance and stability of open source software within the secure software
supply-chain is an incredibly complex topic. And, in my opinion, is still
relatively unsolved today.

Having a deep understanding of where your software dependencies come from and
what’s happening in the ecosystem around the bits you need is a critical first
step. Supporting and sponsoring open source maintainers is a great second step.
And, as a triumphant step across the finish line, allocate engineering resources
into projects you or your business depend on.
