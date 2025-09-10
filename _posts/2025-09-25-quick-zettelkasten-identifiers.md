---
layout: post
title:  "Quick Zettlekasten identifiers"
date:   2025-09-10 00:00 +0100
categories: shorts
tags:
- zettelkasten
- identifier
---

How do you quickly calculate a unique identifier for your Zettelkasten, when you
don't know where you left off? Here's my method:

## The Day-Of-Year Method

Use the Day Of Year (DOY):
1. Multiply the (current month - 1) by 30
2. add the day of the month
3. and a _suffix_ starting at 1
4. Finally Add year prefix

The suffix is important if you take more notes in the same day. Sometimes I go
weeks or months without taking any zettle notes, and sometimes I take many in a
single day. I can generally keep track of how many notes I've taken in that day,
but even if I forget, I know that I made _roughly_ four or five notes, so I can
safely start again at seven.

### A Worked Example

For example, let's say that today is the 30th of September 2025.

![casio watchface showing 30th of the month](/assets/images/posts/casio-face.JPG)

1. Since we're in September and we don't calculate January in DOY, we multiply 
8 x 30, which gives us **240** (in my head: 3 times 8, add a zero).
2. Add 30 (the current day of the month) to 240, which gives us **270**. We have our DOY.
3. We suffix with the daily counter: **2701**
4. And finally, we prefix with the last digit of the year: **52701**.

This will create a collision in ten years time if I decide to take another note
on September 30th 2035. In this case I would use two digits for the year prefix,
which would make the note in 2035 be indexed at **352701**

Each month is calculated at 30 days. We don't account for leap years or any of
that. The identifier is not meant to encode a date. It is easy, however, to
calculate the date given a DOY identifier (and a rough idea of the decade).

This method produces a minimum of 5 digits.

### Benefits

* One digit shorter than the YMMDDC method below
* Makes your brain work

### Problems

The method adds friction to taking notes, which leads to fewer notes.

## The YMMDDC Method

The _Year-Month-Day-Counter_ method doesn't require calculations, but it does
take up two digits for the month and day fields, otherwise it can lead to
duplicate identifiers. For example, the 1st of November and the 11th of January
would both be **111** if we decided to skimp on digits.

This method produces a minimum of 6 digits.

### A Worked Example

The first note created on the 30th of September 2025 would be **509301**. The
final `1` is the note counter. As before, if you've been taking notes for a
decade, you might want to use two digits for the year field.

### Benefits

Nothing to calculate

### Problems

One extra digit compared to the DOY method

## Why on Earth would you use the DOY method?

Sometimes I use a numbering stamp to stamp the number on my notes - the exact
model pictured here:

![picture of a self-inking numbering stamp](/assets/images/posts/numbering-stamp.jpg)

It's limited to 6 digits.
