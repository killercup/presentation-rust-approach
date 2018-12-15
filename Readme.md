---
title: "Rust’s approach of getting things right"
author: Pascal Hertleif
date: 2018-12-15
categories:
- rust
- presentation
progress: true
slideNumber: true
history: true
navigation: false
controls: 0
height: 600
width: 800
---
# Hi, I'm Pascal Hertleif

- Dev tools team
- [CLI WG]
- {[twitter],[github]}.com/killercup
- Rust-centric blog: [deterministic.space]

[CLI WG]: https://rust-lang-nursery.github.io/cli-wg/
[twitter]: https://twitter.com/killercup
[github]: https://github.com/killercup
[deterministic.space]: https://deterministic.space/

::: notes

I've been working with Rust since early 2014,
and I've been loving every minute of it.

Right now, I'm leading the CLI working group.
Our goal is to make writing command line applications in Rust _amazing_!

Okay.

First things first.

:::

- - -

> […] getting things right
>
> <cite>– Pascal, writing presumptuous talk titles</cite>

::: notes

Wow, such a presumptuous title!
What was I thinking?
Can I deliver a talk on how to write the _perfect_ program?

Of course not.
This is not even a technical talk in these sense that it'll contain code!
I'll talk about a bunch of cool projects,
but what I want to get at is:
How can we make more of those?

Why?
We are still trying to figure out how to write great Rust code.
There are some big ambitious projects in it that work;
but there is no 5 Steps guide to making one of those.

:::

- - -

# What is Rust?

> A systems programming language
> that runs blazingly fast,
> prevents segfaults,
> and guarantees thread safety.

::: notes

That's cool, but what does that mean _for me_?

:::

- - -

# What is Rust?

> Empowering everyone to build reliable and efficient software.

::: notes

Less of a feature checklist,
tells you how it helps _you_.

This is the slogan right next to the Rust logo on the current website.
I know it took quite a while to settle on this,
and I think it's super good.

:::

- - -

## What is Rust?

> Technology from the past come to save the future from itself
>
> <cite>– Graydon Hoare, inventor of Rust</cite>

::: notes

Woah, that's a completely different spin on it.

What did Graydon mean by it?

By the way, he goes on to say:

> Many older languages \[are\] better than new ones.
> We keep forgetting already-learned lessons.

:::

- - -

## Prior art

> all information
> that has been made available to the public
> in any form
> before a given date
> that might be relevant
> to a patent's claims of originality.
>
> – [Wikipedia](https://en.wikipedia.org/wiki/Prior_art)

::: notes

This talk is on how the Rust community embraces the idea of
looking at "prior art".

If you don't know this phrase, here is how Wikipedia defines it.
As you can see it's originally from patent laws.

:::

- - -

## We don't need to re-invent everything

Embrace existing ideas and put them into our current context.

. . .

~~Not invented here~~

::: notes

Some people I know have this tendency
to try to work through problem all on their own.
They sit down,
think real hard,
and come up with a solution.
That is a good idea when it comes to very domain-specific problems
but more general concepts
it really pays off to look at what is already out there.

:::

- - -

## This is not a "Rewrite it in Rust" talk

. . .

It's more of a "be curious and try out weird things" kind of talk

::: notes

But I don't mean this in the sense of:
Look at an existing piece of software
and rewrite it in Rust,
just because you think it being Rust makes it better.

We should always make sure to adapt to the current context.
What have we learned in the meantime?
What were the original designers not happy with?

:::

- - -

# For example:<br/>The borrow checker

Problem: Write memory-safe high-performance code

::: notes

I'll talk about some projects, as well as some people, and their stories.
The first one is about the borrow checker.
Or, the Ownership and Borrowing system
that is arguably the main features that differentiates Rust
from other mainstream languages.

The problem was writing memory-safe high-performance code.
C/C++ allow writing high-performance code but getting it right is very hard.

Rust is a try at making a language
that allowed both:
High performance, and memory safety.

:::

- - -

## Prior art

. . .

[Cyclone] (2002)

- "safe C"
- never-NULL pointers
- region analysis

. . .

[ATS] (2005)

- ML-inspired language 
- built-in theorem prover

[Cyclone]: https://en.wikipedia.org/wiki/Cyclone_(programming_language)
[ATS]: https://en.wikipedia.org/wiki/ATS_(programming_language)

::: notes

I specifically said "mainstream languages":
Because there are other languages that tried that before!

At AT&T Labs, the Cyclone language was developed as a "safe C" dialect.
If you look at its features you'll see a bunch of similarities with Rust:

- pointer types that are guaranteed to be not-NULL
- pointers must be initialized before they can be used
- only safe casts

Another language is ATS (Applied Type System).
It's inspired by ML,
and its main feature is the built-in theorem prover.
Using this, it can,
at compile-time,
prove the absence of buffer overflows and memory corruptions,
but also other properties like division by zero. 

:::

- - -

## Prior art

[Affine logic](https://en.wikipedia.org/wiki/Affine_logic)

::: notes

Those languages seem interesting
but let's have a little wider of a look.

> The logicians usually get there before the computer scientists.
>
> – Philip Wadler on [Corecursive #21](https://corecursive.com/021-gods-programming-language-with-philip-wadler/)

(Philip Wadler is Professor of Theoretical Computer Science at University of Edinburgh)

> 1. can be used any number of times (default)
> 2. can't be used more than once (affine)
> 3. must be used at least once (relevant)
> 4. must be used exactly once (linear)
>
> – Gankro in [The Pain Of Real Linear Types in Rust](https://gankro.github.io/blah/linear-rust/)

:::

- - -

## Prior art

> This work is about a functional language \(Λ_{LA}\), with a typable sub-set \(Λ^{T}_{LA}\). The types for \(Λ^{T}_{LA}\) are polymorphic formulas of Intuitionistic Light Affine Logic […]
>
> <cite>– Roversi, Luca. "Light affine logic as a programming language: a first contribution." _International Journal of Foundations of Computer Science_ 11.01 (2000): 113-152.</cite>

::: notes

People tried to define programming languages based on affine logic before.
Roversi describes \(Λ^{T}_{LA}\) ("Lambda L.A. T.") in 2000.

Similar to ATS and Cyclone this doesn't become recognized by mainstream programmers.

:::

- - -

> […] Rust finally nailed it down in a way that is accessible to everyone.
>
> <cite>– Stjepan Glavina</cite>

::: notes

And this is where Rust made the break-through:
It is based on the same, old concept of affine logic,
but packages it up in a practical and attractive way.

That Rust makes this (somewhat) approachable
is probably why we are here, at a Rust conference.

:::

- - -

## Adapting to the current context

Original `borrowck` doesn't understand all valid programs

. . .

Non-lexical lifetimes

. . .

Borrow parts of a struct, self-references

::: notes

Just adapting affine logic is not enough,
we need to make it actually usable.
The first iteration was good,
but had known cases it didn't support.

This is where new research is being done.
Non-lexical lifetimes refined what the scope of an owned resource is.

But this is not the end, either.
Borrowing/mutating parts of structs can still be made smoother.
See Niko's thoughts on [After NLL](http://smallcultfollowing.com/babysteps/blog/2018/11/01/after-nll-interprocedural-conflicts/).

:::

- - -

# Another example: ripgrep

[super fast](https://blog.burntsushi.net/ripgrep/)
competitor to `grep` and `ag`

uses Rust's regex crate

- - -

## Prior art (regex)

> - Pike VM
> - Boyer-Moore & Aho-Corasick
> - Lazy DFA
> - Teddy

::: notes

- Pike VM: executes regular expressions using deterministic finite automaton
- Find literal prefixes faster using Aho-Corasick or Boyer-Moore
- lazy: Build DFA as you go, and cache states, inspired by Russ Cox writing on this
- Teddy: Use SIMD instructions for substring matching, from Hyperscan project

cf. [Hacking](https://github.com/rust-lang/regex/blob/master/HACKING.md) doc in regex repo

:::

- - -

## Adapting to the current context

> - Bringing it all together
> - Unicode support
> - High concurrency

- - -

> I think of Rust as fertile ground for growing cool stuff.
>
> <cite>– Stjepan Glavina</cite>

::: notes

So far we've seen two big examples
of cool Rust projects;
which are arguably also the most famous ones.

But let's look at another story.
I've quoted Stjepan Glavia before.
You might know him as the author of `crossbeam-channel`
but Stjepan also wrote the implementation of `[T]::sort()`.

:::

- - -

# Why `sort()` is like it is

> I was reading the previous implementation of `sort`,
> just out of curiosity,
> and thought "wait, this could be done better".
>
> <cite>– Stjepan Glavina</cite>

::: notes

The most important part in this quote,
for me,
is "out of curiosity".
Being curious is an amazing trait!
We should embrace it,
and help people be curious.

:::

- - -

## Prior art

- current impl
- regular merge sort
- blocksort
- timsort
- …

(See [Rust PR #38192](https://github.com/rust-lang/rust/pull/38192) for details)

::: notes

Sorting algorithms are one of the introductory topics in computer science.
That doesn't make them boring, though!
There a bunch of real-world considerations
that are discussed on little in academia,
like cache locality.

:::

- - -

## Community

This was Stjepan's first PR for Rust!

. . .

It took just three days to get into `std`!

. . .

He followed this up with a RFC to add `sort_unstable`

::: notes

Imagine this:
You check the implementation of a standard library function for fun.
(Who doesn't click on "src" in the docs for fun?)
You write a 200 line PR (plus comments and benchmarks).
And three days later this is part of the std lib!

:::

- - -

# Where to find inspiration: Other ecosystems

- - -

## crossbeam-channel

An improvement for the channels in `std`

. . .

Take inspiration from Go's implementation

. . .

bounded/unbounded, MPMC, fancy `select!` macro

::: notes

Stjepan's next project was crossbeam-channels.

- `std::sync::mpsc`: single consumer, no stable `select!`, slow
- idea: bounded and unbounded mpmc channels
- [Designing a channel](https://stjepang.github.io/2017/08/13/designing-a-channel.html)
- [Lessons to be taken from channels in Go?](https://github.com/crossbeam-rs/crossbeam-channel/issues/39) - [design doc from go](https://docs.google.com/document/d/1yIAYmbvL3JxOKOjuCyon7JhW4cSv1wy5hC0ApeGMV9s/pub)
- [crossbeam-channel select macro](https://github.com/crossbeam-rs/crossbeam-channel/pull/41)
- [perf](https://i.imgur.com/tRI4HMO.png) ([src](https://twitter.com/stjepang/status/1006202765499125760))
    - avoid lock contention as much as possible - the typical "happy path" is lock-free.
    - Plus, there is a lot of small optimizations that add up.

:::

- - -

## Other examples

- [parking_lot](https://github.com/rust-lang/rust/pull/56410)
- [hashbrown](https://github.com/rust-lang/rust/pull/56241)

::: notes

parking_lot contains user-space synchronization primitives, taken from Webkit

hashbrown is a port of Google's SwissTable, a super fast hash table implementation

they work as drop-in replacements! both have open PRs to be used in `std`

:::

- - -

# Where to find inspiration: Academia

::: notes

How many of you studied something related to computer science?

How many of you liked reading papers? I know it's not easily accessible.

But…

:::

- - -

> So many awesome engineering projects can be pulled off by
> just taking a quick glance at where current research is at in a
> particular field.
> 
> Often, implementations are lagging by several decades.
> 
> <cite>– Tyler Neely</cite>

::: notes

There is a huge opportunity here,
and I feel like not enough people know about it!

:::

- - -

## Where to find interesting papers

- [Papers We Love](https://paperswelove.org/) (paperswelove.org)
- [the morning paper](https://blog.acolyer.org/) (blog.acolyer.org)

::: notes

You can find a lot of research on a huge variety of topics
for free.
The problem is most often curating.
For example, check PapersWeLove.org

:::

- - -

> Most of the things I read have no useful application for me right away.
> I need to let them simmer for a while.
>
> <cite>– Geoffroy Couprie</cite>

::: notes

So can you act like a machine that consumes research papers
and produce awesome Rust projects?
Probably not.
That's not how people or research work.

:::

- - -

> Whenever something interesting comes out of academia, I try to think:
> How do we make this more practical?
> Can we reach a little bit farther?
> Can we bend the curve somewhere?
>
> <cite>– Stjepan Glavina</cite>

::: notes

You can approach this with a certain point of view, though.
Like: What can we do with this that wasn't possible before?

> […] we still have to make compromises *somewhere* […],
> but we try to bend the curve as far as possible,
> to reach into areas that were previously thought of as unimaginable.
>
> <cite>– Stjepan Glavina</cite>

:::

- - -

## "bend the curve"

The community is trying to find a way to overcome traditional trade-offs

. . .

incl. putting brand-new research into non-intimidating Rust-flavored packages!

::: notes

- memory safety without garbage collection
- abstraction without overhead

but also:

> expert-only, researchy concurrency in a non-intimidating Rust-flavored package

(Stjepan)

:::

- - -

# Another tale: Green threads

> - Rust used green threads for async I/O
> - They were removed
> - Not the easy thing to do, but pays off in the long run

::: notes

To allow nice async I/O,
green threads and global event loop are good ideas.
There was a lot of prior art on this.
So that is what Rust had in 2014.

But:
It didn't fit well with the direction the language was being developed in.

So despite not having an alternative ready,
green threads were removed.

This is why now we're seeing futures and async/await being implemented
in totally different styles,
based on state machines and generators.

This wasn't the easy way,
but it was the one that gives Rust much of the flexibility it has today.

If you want to hear more about that,
go to Katharina's keynote later today.

:::

- - -

## It's okay to iterate on ideas

The first version might not be perfect

. . .

and that's totally okay

::: notes

- too complicated
- too weird/not a good fit
- not powerful enough

This is one of the reasons why there are so many features in Rust
that are marked as 'unstable'
-- people know/think that we can still do better!

:::

- - -

# Do ambitious things!

> 1. Be curious!
> 2. Try to bend the curve!
> 3. Help people discover awesome things!

::: notes

Let's recap.

This is the part of the talk where
I need to put a lot of exclamation marks on the slide.

Rust is in a very good place
for you to try and do ambitious things.
We are all trying to figure out how to use Rust.
There is much to be explored!

:::

- - -

# Where to look for inspiration

> 1. Other ecosystems
> 2. Academia
> 3. Completely different areas?

::: notes



:::

- - -

# Thank you!

I'm Pascal and I want you to talk to me!

{[twitter],[github]}.com/killercup

Slides: [git.io/rust-approach](https://git.io/rust-approach)
