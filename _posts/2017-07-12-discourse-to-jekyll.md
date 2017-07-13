---
layout: post
title:  "Discourse posts to Jekyll entries"
date:   2017-07-12 23:44:11 +0100
categories: projects
tags:
- discourse
- google-functions
- jekyll
---


I've been looking into a way of publishing Discourse posts as Jekyll entries.

### The Problem

I run a gaming community that uses a Discourse forum. In it there are pages (rules, tutorials etc.) that I'd like to feel more _permanent_, more easily accessible. I got around this by curating permalinks, but then there are nice-to-haves like mission debriefs - or even a monthly blog that could keep non-members engaged. This is where we run into the forum's limitations as a platform.

Enter Github-powered Jekyll. Easy and fun to set up, uses the same markup language as the forum, and every change is publicly visible - which helps build and maintain trust. Creating a pull request, however, is a problem for almost all of our members, who are non-technical.

Ideally I'd like to keep a familiar interface and make the process automagical. Another issue is authentication and access control: authentication is implicitly taken care of, and access control is certainly achievable via the Groups feature.


### The Solution - Design

Every time a post tagged, eg, `featured` is updated, a commit must be made to the blog's repository. This can be handled in a Discourse plugin but I'm too lazy to learn Ruby, and we're still in the prototyping phase.

Enter Google's [Cloud Functions][gcf], webhook-triggered NodeJS code that runs as soon as you upload the code. Couple that with Discourse [webhooks][dwebhooks] and we have our recipe for disaster!

> Webhook is a way to notify external services about changes on the internet. It's easier to setup, manage and develop than a Discourse plugin. Though, it does require you to program a little bit or understand its technical details.


Here's the plan:

1. Discourse fires a webhook following a user action
1. Google Functions picks it up, evaluates, and makes a commit
1. New commit fires the build cycle in Github Pages


### Implementation

And there we are. I'll be updating this after the summer - vacations coming up!

[gcf]: https://cloud.google.com/functions/
[dwebhooks]: https://meta.discourse.org/t/setting-up-webhooks/