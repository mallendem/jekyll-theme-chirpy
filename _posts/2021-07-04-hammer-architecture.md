---
layout: post
title:  "Everything is a nail: System architecture when all you know is a hammer"
date:   2024-03-01 15:02:08 +0200
categories: [Architecture]
tags: [design]
author: Miguel
---

A few times I have encountered situations where people that don't posses extensive knowledge of
system architecture (and I'm not an expert) try their luck designing solutions with the tools they
had in hand. This inevitably led to **overly-complex systems** that are difficult to operate, maintain,
and diagnose when problems arise.


## Common characteristics of overly-complex systems
When one of these quacky systems get designed, I've observed that they share a bunch of common characteristics:
- It takes several standalone services to complete the task
- Everything is glued together with homebrew, single-purpose, adhoc scripts
- The services involved are fairly simple themselves under the hood
- Artificial hard dependencies are introduced between those standalone services
- The primary function of the services used doesn't correlate with the function they will need to perform in the new design
- There are solutions on the market that match 90%+ of your use case (either commercial or open source) but the glued
together system is still going ahead
- Requires a good amount of manual work to set up
- When questioned about the complexity, everything gets reduced to the simplicity of the underlying components (is a bash script, is just a ftp server...), deflecting answering the complexity of the dependencies

Obviously, not every system that shares a few of these is a candidate for being a overly-complex system, but it should raise
red flags if things start to match.


## Why this happens
As far as I've observed, this happens for three main reasons:
- Lack of technical skill on the team tasked with designing it
- Time constraints
- Minimal consequences

The first one may be discussed at a later date, but one thing I've observed regarding time constraints it that usually
these are self-inflicted wounds. This comes from a combination of overestimating the skill level of the team and
underestimating the amount of work that needs to be done to ensure that the system will work as intended
(a perfect example of the [Dunning–Kruger effect](https://en.wikipedia.org/wiki/Dunning%E2%80%93Kruger_effect)).

With little to no oversight, the system gets designed, deployed **and** put into production (usually this step is proudly announced).
After entering production earlier than it should, stakeholders start to notice several features that were promised aren't
yet implemented and there are minor setbacks almost constantly, but this has minor to no consequences.

![Desktop View](/assets/img/domino.jpg){: width="972" height="589" style="max-width: 200px" .right}
The general lack of consequences creates a trend of jive-ass architectures sprouting around one after the other that usually is caught too late.
The way external stakeholders see it, the system works and is doing what it was promised (although a few features are irremediably left
aside for the next iteration). It was done quickly, so there is no doubt that this was a masterwork of the highest level.

Eventually, as with any other system, **something will fail**. Artificial dependencies via homebrew scripts between simple, easy-to-deploy components creates a sense of false confidence that can be summarized as: this won't fail, but if it fails, it will be easy to fix. Funnily enough, this new system has artificially transformed a bunch of simple systems into a complex one without the safeguards or considerations the latter should have, and has introduced new requirements on the simple, precursor systems that weren't taken into account when those were deployed (bandwidth allocation, increased storage needs, maintenance windows, testing for edge cases...).


## Why (and how) these systems fail

The failure of this kind of systems is usually sudden and without any warning. If the behavior earlier mentioned has proliferated it will cause **widespread downtime**. The lack of technical skills will make the team to rely on the few systems they already know to use as building blocks for more of those overly-complex systems.

The main reason for failure is a combination of **perverting the primary function** of the simple ones (which introduces new edge cases the
systems are not prepared to handle) and the inter-dependencies between them.

Another thing that isn't considered at first glance is that **running maintenance or replacing the simple systems** will become more and more
difficult as this behavior progresses.


### The timeline of a janky system

From design to sunset, these badly designed abominations follow (almost to the letter) certain stages in their life:

1. A problem needs to be tackled
2. A solution is designed without considering deploying new products, but instead relies on extending functionality of current systems
3. After a short period, it enters production
    - By the time you reach this step you are relying on **production systems**, so testing is limited
4. Feature development/integration stops shortly after. The focus shifts to fighting "unexpected" problems
5. Any of the dependencies fails, bringing everything down
    - Usually the homebrew scripts won't take into account a service being down so depending on what is being done by them, the results can be catastrophic.
6. This particular failure gets fixed (if lucky!)
7. Repeat 5-7

Trying to maintain it long term will be an endless loop of steps 5 and 6. Once the most common failures are addressed, you will end up
with the mythical **legacy system** that no one wants to touch, except the one guy that deployed it some years ago.

## I have one of those systems, now what?

![Desktop View](/assets/img/you-got-this.jpeg){: width="972" height="589" style="max-width: 200px" .left}
I'm myself guilty of this, although I would like to think that I'm improving now that I'm aware and I've seen the consequences of this
process first hand. My finding is that, obviously, not allowing these systems to get into production and catch the faulty design early on is the best way to proceed, but if the system is already in place it may be difficult to remove it.

Once in production, a huge amount of work has been invested into it and probably **until it has some failure with high enough impact** it won't be considered for replacement (it works!). Although not impossible, my experience is that trying to fix the system without a start-from-scratch redesign isn't effective: too time consuming when you can have a fresh start and completely remove dependencies or substitute them with proper systems designed for the task.