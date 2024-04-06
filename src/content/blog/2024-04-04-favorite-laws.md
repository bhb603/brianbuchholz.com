---
title: "My Favorite Laws"
description: "Unicode"
pubDate: "2024-04-04"
publish: false
---

There is a category of "laws" that aren't strictly laws in the scientific sense
(like Newton's Laws of Motion) or the legal sense (you wont be punished for breaking them), but more like rules of thumb, heuristics, or observations about the world.

Popular examples include [Murphy's Law](https://en.wikipedia.org/wiki/Murphy%27s_law), [Moore's Law](https://en.wikipedia.org/wiki/Moore%27s_law), and [Occam's Razor](https://en.wikipedia.org/wiki/Occam%27s_razor).

This ["Hacker Laws"](https://github.com/dwmkerr/hacker-laws) GitHub repo has a fantastic collection of such Laws, Theories, Principles, and Rules.

The following post includes a discussion of my favorite laws, ones which I have found useful, particularly in my career, but also more broadly.
At best these can help frame conversations, inform thinking,
and have concrete impact on decision making.
Even if they don't deliver on such promises, at least they are fun to think about.

<em>Last updated 2024-04-04</em>

### Pareto Principle AKA 80/20 Rule

Roughly 80% of the effects come from 20% of the causes.

**History:** Italian Polymath Vilfredo Pareto originally observed in the late 1800s that 80% of Italy's land was owned by 20% of the population.
Management consultant Joseph M. Juran developed this concept in the 1940s in the context of quality control.

**My take:** I think about the 80/20 Rule all the time with respect to prioritization.
The numbers (80% and 20%) are just estimates and frankly not that important.
The key insight is that not everything has the same impact, and often a small minority of (features | bugs | lines of code | Italian land owners) have disproportionate impact on outcomes.

If there are 10 bugs in the backlog, there may be two-or-three which account for the vast majority of user complaints. Focus on those first.

If a feature or MVP has 10 acceptance criteria, often a few account for most of the user value. If possible deliver those first, and iterate on the other cases later.

### Maker's Schedule vs Manager's Schedule

The optimal schedule for Makers (ie programmers, writters, designers, etc) is very different, and often conflicting, with the ideal schedule for Managers.

**History:** Paul Graham introduced this concept in a [2009 blog post](https://www.paulgraham.com/makersschedule.html). It's short and well worth the read.

Basically he teases out the difference between Makers' and Managers' calendars:

- A manager's day is full of meetings. For example an 8-hour workday could be partioned into eight 1-hour blocks, and a manager could have eight seperate back-to-back meetings. This would be a productive day for a manager, whose goals include communicating and "managing" various teams and people.
- A maker, on the other hand, needs long, uninterrupted blocks of time. Too many meetings are highly disruptive. Even one or two poorly placed meetings in a day can kill productivity. The context switching is costly, and even just knowing a meeting is approaching can prevent a maker from delving into an ambitious task.

**My take:** I think the importance of "Focus Time" or "Deep Work" has become more well known since Paul Graham's essay was written.
Nevertheless, the conflict posed by the Makers vs Managers schedule is still relevant.

As a Maker: Ensure you have enough large blocks of time to focus on important work. Actually put "focus time" blocks in your calendar.
If meetings are killing your productivity, raise the issue with your team: ultimately they all, especially managers, have the same goal of team productivity, so it should be in everyone's interest to reach a solution.
Finally be flexible - there will be times when meetings disrupt your day, but if your presence is requested, you ought to attend in good faith and do your best to manage your time around it.

As a Manager: Remember that the ICs on your team do not operate on the same "appointment book" schedule as you. Respect team members' calenders and the cost of context switching. Periodically audit recurring meetings and prune those which are no longer useful.

### Brooks' Law (Mythical Man Month)

### Code is Read 10x more than it's Written
