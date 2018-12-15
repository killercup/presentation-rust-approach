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

*Pascal says:*

> A programming language is only as useful as what people actually use it for.

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
> – Graydon Hoare, inventor of Rust

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

We should embrace the ideas that are already out there,
and apply them in the current context.

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

# For example: The borrow checker

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

[Cyclone] (2002): "safe C", never-NULL pointers, region analysis

. . .

[ATS] (2005): ML-inspired language with a built-in theorem prover

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

> This work is about a functional language \(Λ_LA\), with a typable sub-set \(Λ^{T}_{LA}\). The types for \(Λ^{T}_{LA}\) are polymorphic formulas of Intuitionistic Light Affine Logic (ILAL in the following.) Polymorphism is a la ML: all the universal quantifications in any formula must occur as top-most operators.
>
> – Roversi, Luca. "Light affine logic as a programming language: a first contribution." _International Journal of Foundations of Computer Science_ 11.01 (2000): 113-152.

::: notes

People tried to define programming languages based on affine logic before.
Roversi describes \(Λ^{T}_{LA}\) ("Lambda L.A. T.") in 2000.

Similar to ATS and Cyclone this doesn't become recognized by mainstream programmers.

:::

- - -

> […] Rust finally nailed it down in a way that is accessible to everyone.
>
> – Stjepan Glavina

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

Niko's thoughts on [After NLL](http://smallcultfollowing.com/babysteps/blog/2018/11/01/after-nll-interprocedural-conflicts/)

::: notes

Just adapting affine logic is not enough,
we need to make it actually usable.
The first iteration was good,
but had known cases it didn't support.

This is where new research is being done.
Non-lexical lifetimes refined what the scope of an owned resource is.

But this is not the end, either.
Borrowing/mutating parts of structs can still be made smoother.

:::

- - -

# Another example: ripgrep

super fast competitor to `grep` and `ag`

uses Rust's regex crate

- - -

## Prior art (regex)

> - Pike VM
> - Boyer-Moore & Aho-Corasick
> - Lazy DFA
> - Teddy

- - -

## Adapting to the current context

> - Bringing it all together
> - Unicode support!

- - -

# It doesn't have to be big

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

## Why the current implementation of `sort()` was written

> I was reading the previous implementation of sort,
> just out of curiosity,
> and thought "wait, this could be done better".
>
> – Stjepan Glavina

::: notes

Sorting algorithms are one of the introductory topics in computer science.
That doesn't make them boring, though!

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

See [Rust PR #38192](https://github.com/rust-lang/rust/pull/38192) for details

::: notes



:::

- - -

## Community

This was Stjepan's first PR for Rust!

. . .

It took three days to get into `std`

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



- - -

> I think of Rust as fertile ground for growing cool stuff.
>
> – Stjepan Glavina

- - -

> Whenever something interesting comes out of academia, I try to think:
> How do we make this more practical?
> Can we reach a little bit farther?
> Can we bend the curve somewhere?
>
> – Stjepan Glavina

- - -

## "Can we bend the curve somewhere?"

The community is trying to find a way to overcome traditional trade-offs

- - -

> […] we still have to make compromises *somewhere* […],
> but we try to bend the curve as far as possible,
> to reach into areas that were previously thought of as unimaginable.
>
> – Stjepan Glavina

- - -

> so many awesome engineering projects can be pulled off by
> just taking a quick glance at where current research is at in a
> particular field.
> 
> Often, implementations are lagging by several decades.
> 
> – Tyler

- - -

# Making cool developments accessible

> 1. Curiosity
> 2. "I can do it"
> 3. Community and development transparency

- - -

# Thank you!

I'm Pascal and I want you to talk to me!

{[twitter],[github]}.com/killercup

Slides: [git.io/rust-approach](https://git.io/rust-approach)
