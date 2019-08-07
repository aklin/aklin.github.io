---
layout: post
title:  "Syntax Highlighting with Kubectl"
date:   2019-08-07 21:00:00 +0000
categories: projects
header:
 external: https://images.unsplash.com/photo-1484417894907-623942c8ee29?q=80
tags:
- bash
- kubernetes
- kubectl
---

Today at work I had to examine a large kubernetes Ingress definition.
The output filled my terminal and as I was not able to quickly
scan the text for what I was looking for, I felt slightly annoyed.
I searched online for solutions and I found [this issue][issue]
which describes what I want as a feature request.

Some people had posted various solutions in the comments, but for
some reason I wanted to try my own. And here it is:

```sh
# In your shell resource file
function hl () {
	highlight - -S yaml -O truecolor | less
}
```

See that cheeky call to `less` there at the end? That's because I wanted
to navigate through the output at my leisure. Though it's true that I use
tmux, which supports all kinds of scrollback, I really like the search
feature of `less` and the amount of control I get.

To invoke it I would simply do

```
$ kubectl get cm test -oyaml | hl
```

That's good enough... for about an day. Which is how long I've had it for.
Don't get me wrong, it works wonders, and I love it. I just don't want to
pipe it manually every time.


When I went home I thought about the pull request and how easy it would be
to patch the `kubectl` executable to _somehow_ include - or fork - the
`highlight` process to style the output. Before even realizing how stupid
this implementation would be, the nebulous beauty of `alias` danced before
my eyes. Of course, I didn't need to touch `kubectl` at all - I just wanted
to format the _output_!

So the project [`kubectl-prettifier-script `][project] was born, which aims
to do just that. At most as a temporary solution, until the Kubernetes team
implements the same feature natively.



[project]: https://github.com/aklin/kubectl-prettifier-script
[highlight]: http://manpages.ubuntu.com/manpages/cosmic/man1/highlight.1.html
[issue]: https://github.com/kubernetes/kubectl/issues/524