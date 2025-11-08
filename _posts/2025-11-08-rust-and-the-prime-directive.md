---
layout: post
title:  "Rust and the Prime Directive"
date:   2025-11-08
categories: thoughts
tags:
- rust
- stainless linux
---

The Prime Directive is _If it Isn't Broken, Don't Fix It_. Why are we rewriting
everything in Rust?

Taken from this post of [OMG Ubuntu][ubuntu-sudo]:

> Canonical’s Jon Seager, VP Engineering adds the company is “delighted to be
> investing in critical,low-level software utilities [and] by choosing to adopt
> sudo-rs I hope to accelerate the path to wider adoption across the Linux
> ecosystem”.

The engineering justification is given as follows:

> The change is being made with an aim to improving the security of the sudo
> command, thanks to the Rust programming languages built-in ‘memory safety’
> features.

This is a valid argument for a new project. It is not a valid argument for
a mature program that has been actively maintained for the last 
[46 years][history-sudo].

I think all this Rust enthusiasm is a lot more about making the Rust language
inextricably linked to Linux internals rather than addressing critical, _present_
vulnerabilities. In other words, it feels like the Rust version of utilities is
used just _because we can_. 

By all means, write new projects in Rust. But why replace existing ones? Every
rewrite risks re-discovering bugs that have already been solved in the original
version. We all know this. Why do it with core utilities such as sudo, and others?
It doesn't have to do with the language that you decide to re-write something in,
but it has everything to do with the fact that the incumbent has had decades of 
development behind it. 

At the time of writing, sudo has [35 open issues][sudo-issues]. sudo-rs, on the
other hand, has [63 open issues][sudo-rs-issues]. Can sudo-rs in time be just as,
or even more reliable than sudo? Yes. Is it right now? _I don't know_. It hasn't
been out for long enough. It hasn't been battle tested.

And how exactly, I hear you ask, are we supposed to battle-test a program if no
one adopts it? Excellent question. The answer is, I don't care. I want software
that works, not software that's written in Rust. Test it on someone else's
computer. Test it on yours. Not on mine. Not in my back yard, mister.

The people who share my thoughts are using the term _stainless linux_ to voice their
opposition to the replacement of core utilities written in the new fancy language,
whatever that language happens to be.

sudo is not broken. Why fix it?
 

[ubuntu-sudo]: https://www.omgubuntu.co.uk/2025/05/ubuntu-25-10-rust-sudo-rs-change
[history-sudo]: https://www.sudo.ws/about/history/
[sudo-issues]: https://github.com/sudo-project/sudo/issues
[sudo-rs-issues]: https://github.com/trifectatechfoundation/sudo-rs/issues
