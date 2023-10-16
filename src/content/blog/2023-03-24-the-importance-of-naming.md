---
title: 'The Importance of Naming'
description: 'The importance of naming'
pubDate: 2023-03-24
publish: true
---

Sharing a great article on **naming**, one of the most challenging and important aspects of programming:
[Naming conventions in programming – a review of scientific literature][1]


> Names are layers of indirection, separating essential features of what things are, or what they do, from the unnecessary details.
> ...
> "Layers of indirection can be peeled away incrementally, allowing us to work within a codebase without understanding its entirety. Without indirection, we’d be unable to write software longer than a few hundred lines". <br>
> <cite>Tellman, Z., 2019. Elements of Clojure. 1st ed. lulu.com. via [article][1]<cite>

Code is written for other people.
Developing and maintaining software depends on being able to understand a codebase.
Naming is important because bad names can make understanding a codebase needlessly hard.

I love the quote above, about names being layers of indirection, because that cuts to the heart of the issue.
Poor names fail to create a meaningful abstraction for the thing they represent, or they do so in a way that is cognitively demanding to parse:
being inconsistent or ambigious or overly specific or any of the antipatterns discussed in the article.
On the other hand, good names provide legible abstractions, which ease the cognitive load of understanding a complex system.

[1]: https://makimo.com/blog/scientific-perspective-on-naming-in-programming/
