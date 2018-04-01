---
layout: post
title:  "The Mighty Sibling Selector"
subtitle: "Styling naturally ordered data"
date:   2018-02-08 00:00:00 +0100
published: false
categories: guides
tags:
- css
- web-development
---


This is one in a series of posts I write to remind myself of useful features I have found
and forgotten about, only to rediscover them again. The CSS sibling selector - specifically
the _[general sibling combinator][mdn]_ - is something I rediscovered recently.


It's all about selecting elements that are _next_ to each other, without being nested in
one another. And what's more, it's actually supported by <i class="fa fa-internet-explorer"></i>
versions as old as IE 7!



In the following example I'm using some paragraph elements separated by header tags. I'm going
to indent the paragraphs slightly to the right, so that the headers stick out a bit.

* I can apply the style on all `p` elements - but there are paragraphs before and after the text I don't want indented.
* I could perhaps wrap the region in question around a `div`, but I don't want ugly HTML markup cluttering up my Markdown

The solution is the sibling selector `~`, which the [spec][spec] is very clear about:

> the element represented by the first sequence precedes (not necessarily immediately) the element represented by the second one


<script async src="//jsfiddle.net/bmnsorzk/2/embed/html,css,result/"></script>


**The problem** I 


[mdn]: https://developer.mozilla.org/en-US/docs/Web/CSS/General_sibling_selectors
[spec]: https://www.w3.org/TR/selectors-3/#general-sibling-combinators