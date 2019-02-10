---
layout: post
title:  "Interview Test: The Change Dispenser"
date:   2019-02-10 21:00:00 +0000
categories: interviews
header:
 external: https://images.unsplash.com/photo-1531449862596-16e4c62e2b10?ixlib=rb-0.3.5
tags:
- interview test
- java
- retrospective
---

In December 2015 I was looking for a new job. I found a company through a
recruiting agency, which required me to submit to a technical test for the
role of _Junior Developer_. I won't name the company because it's not relevant,
and although I passed the test, I didn't interview with them because decided to
accept the offer from Dotmatics.

I decided to write a post about it as I was going through my Github account, where I
noticed a couple of private repositories. Those repos needn't be private anymore - 
and this is one of them.

The test is simple enough: Write the logic for a change dispenser, and figure out
the least number of coins you can give for change if a customer pays over the price.
My submission can be found [here][code].

# The Ethics

At the start of writing this I had some qualms about publishing what is, and has been for
some time, a private exercise. The code has been sitting in a private Github repository
ever since, doing absolutely nothing, because I completely forgot about it.

But I really shouldn't have worried: The code is from late 2015, which makes it a
little more than three years at the time of writing. I sincerely doubt the nameless company
uses the same test. But my strongest argument is that I'm not responsible for their hiring
decisions. They issue a take-home test, so they should account for the possibility of 
cheating applicants, and should have measures in place to disqualify them. One such 
measure I can think of is asking them for details about their implementation.

# Some Issues

I noticed a few things I didn't like while re-reading my submission and I'd like to go
over them before I get started with the code.

First of all, naming conventions. I was too influenced by C and Spartan Programming at the
time and my variable names are as small as possible. I've learned it's better to call a
spade a `spade` rather than a `s`, or indeed a transaction `transaction` rather than `t`.
My eyesight isn't what it used to be, so, longer, context-relevant variable names are much 
appreciated. As an added bonus IntelliJ will highlight all occurrences of a variable if you 
click on it and giving it a properly long name will only help you spot it faster in a large
method.

Then we have `introduction.html`, the file I wanted the interviewers to read before going
through the code. The language I used is extremely bad. It's way too formal and pretentious.
I remember thinking as much back then. I edited a couple of times for clarity, but it looks
like I went for terseness instead. In the end I called it _good enough_ and gave up.
As a matter of fact I completely ignored it while writing this post; I read it all, didn't
understand a word, and went to the code instead. So much for wanting to impress the 
interviewer with fancy HTML.

Finally, the test itself is executed in the main method. I wouldn't do that today, because
what was actually requested of me simply doesn't require a main method. Instead I would've
used a bunch of unit tests to examine each aspect the submission.

In the `Main` class there's a method called `getRandomNominalPrice` accepting one parameter: 
`atLeast`. Apparently, my intention was to provide a monetary value of _at least_ that much, 
but the way I implemented it I'm actually returning _at most_ the requested value. To my 
horror, I just realized now by looking at it. A unit test would've caught that.

```java
/**
 * Get a random price. The value is guaranteed to be representable in PseudoSterling.
 * @return
 */
private static int getRandomNominalPrice(final int atLeast) {
	return (r.nextInt(atLeast) + 1) * PseudoSterling.getMinimumFace().getValue();
}
```

One thing I did like though is the aggressive condition checking and exception throwing
when something goes wrong. There's almost no public method accepting parameters that doesn't
first do a sanity check on what it's got. I still practice this habit, because I prefer code 
that fails loudly and early, rather than playing detective when something goes wrong.

# The Exercise

Right then, on with the exercise. The currency is _Pseudo Sterling_, which is defined in _Pseudo Pounds_:

* 1/4p
* 1/2p
* 1p
* 3p
* 15p
* 75p

It's clear to me that the interviewer is trying to help me. All denominations are multiples of
at least one other, so there's no logic required to detect imperfect divisions. It's all 
business logic for this one. 

## Defining the Currency

This leads us to the most important class, `PseudoSterling`, where I define the denominations.
I opted to use 100 pseudo-pence for each pseudo-pound, so our `QUARTER` starts with a value
of `25`. All other coins are multiples of that, or of `PSEUDO` (which I should've named `ONE` 
or `POUND`). This means that the base value of the quarter can change, and it won't affect the
ratio of the other denominations. I was quite proud of that.

## Defining the Purse

Next up, we have the `Coins` class. `Coins` is, as I put it

> a coin aggregator [which] encapsulates a Map structure that contains the
denomination tally

So... a _wallet_ then. A+ for naming. But of course it's not really a _wallet_, but a 
_collection of coins_. When you buy something you insert a collection of coins in the 
machine, and when you get your change, you get a collection of coins back.

Internally I used an `EnumMap` count of how many coins I have in the wallet. And what 
exactly, if I may ask, is an [`EnumMap`][enummap]? I actually had to look it up because
I've never used it since:

```java
public class EnumMap<K extends Enum<K>,V>
```

A sound decision at the time. I was telling the interviewer I know what the hell
I'm doing when using Collections and I can be trusted to choose the most appropriate for
the task at hand. Now I could've used a `Map` here because `Coins` really has no business
knowing that a `PseudoSterling` is an enum. Or does it? The documentation for `EnumMap` tells
us that 

> All of the keys in an enum map must come from a single enum type that is specified, 
explicitly or implicitly, when the map is created. Enum maps are represented internally as 
arrays. This representation is extremely compact and efficient.

My first reaction here was to conclude it was wrong, but it actually kinda makes sense. Of
course I could've used a `Map`, perhaps even make `PseudoSterling` implement some interface,
let's say `Currency`, and this way I could've supported multiple currencies. But that was
out of scope, and it would've been unnecessary bloat.

One thing I would do differently is to initialise the purse with all denominations
set to `0`. This removes a lot of condition checking from the `addCoins` method.


## Defining the Logic

Finally we come to the business end: the `Transaction`. This is the class where I fulfill
the requirements of the exercise, namely, dispensing change. The algorithm here tries to
dispense as many high-valued coins as it can, before moving on to a coin of lower value.

But I've spotted a bug in the `getChange` method:

```java
final List<PseudoSterling> available = Arrays.asList(PseudoSterling.values());

//...

//Faces are declared incrementally lowest to highest. Turn them around.
Collections.reverse(available);
```

Here I put all available coins in a `List`. I do this to get a pre-sorted list, so I can
easily get the next available coin to try and dispense. The problem here is that, once more, 
`Transaction` knows more about `PseudoSterling` than it should. The declaration order of the 
enum is irrelevant. I should've used a collection sorter instead.


# Final Thoughts

All in all, I'm quite pleased with what I submitted. There are a few bugs I overlooked,
some design decisions that would've gone differently today, and some silly mistakes
that kinda paint me as a junior dev (like a duplicate Javadoc block that I spotted).

There are some things I overlooked in this post because they aren't critical. For
example a few years ago I picked up the habit of using an early return out of a method,
rather than writing a large `if` block. That's a stylistic decision that has no bearing
in the function of the program.

Overall, I remember having fun with this exercise, and I'm happy to dust it off and put 
it in the sun for a bit. I enjoyed the simplicity of this implementation. These days I
probably would've gone for interfaces and extendability and whatnot, but what I have
here does the job and does it pretty well.

Retrospectives like this is quite useful in understanding where I was as a 
programmer a few years ago, what lessons I've since learned, but also what lessons
I've forgotten. I hope I'll have the time to do more of them.


[code]: https://github.com/aklin/ChangeDispenser
[enummap]: https://docs.oracle.com/javase/7/docs/api/java/util/EnumMap.html
