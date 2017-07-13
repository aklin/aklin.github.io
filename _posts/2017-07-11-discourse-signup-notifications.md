---
layout: post
title:  "Discourse sign-up Notifications"
date:   2017-07-11 23:44:11 +0100
categories: projects
tags:
- discourse
- google-functions
- jekyll
---



### The Problem

Our forum registration is open to anyone, but we need to be notified of new members so that the Recruitment Officers (ROs) can get in contact with them.

Setting registrations to require manual approval is not an option, because not every RO is a moderator.


### The Solution - Design

I'm using a [Google Cloud Function][gcf] to respond to a user sign-up event, and make a new post in the appropriate section. ROs then reply to that post with updates to the new member's progress.

This has been working well for us so far, both technically and operationally, so I'm quite pleased.


### Implementation


Allow me to introduce [Project Trumpet][git.trumpet]. Trumpet receives a user event notification, looks for the `user.last_seen_at` field to see if it's `null` (which means it's the first event for this user, since we haven't seen them before), and [creates a new topic][api.discourse] in the _Recruitment_ category.

```
if (req.body.user.last_seen_at !== null) {
	return;
}
```


The only preparation I had to do was to create a new user account, generate an API key for it, add it to an `APIBots` group, and make sure it had write permissions only where it needed to.

That's it.


[gcf]: https://cloud.google.com/functions/
[dwebhooks]: https://meta.discourse.org/t/setting-up-webhooks/
[git.trumpet]: https://github.com/HellenicMilsim/trumpet
[api.discourse]: http://docs.discourse.org/