---
layout: post
title:  "Setting up Aglio on Windows 10 Enterprise"
date:   2018-04-01
categories: guides
tags:
- api
- restful
- documentation
- aglio
---


Having documented our new APIs in [API Blueprint][apb] I settled on [aglio][aglio] to generate
the documentation file I could send to everyone else. I had some trouble on my Windows 10 Enterprise
machine in that aglio didn't quite work out of the box - at least not without some gentle encouragement.


![](/assets/images/posts/code.jpg)
<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px;" href="https://unsplash.com/@ilyapavlov?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Download free do whatever you want high-resolution photos from Ilya Pavlov"><span style="display:inline-block;padding:2px 3px;"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-1px;fill:white;" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M20.8 18.1c0 2.7-2.2 4.8-4.8 4.8s-4.8-2.1-4.8-4.8c0-2.7 2.2-4.8 4.8-4.8 2.7.1 4.8 2.2 4.8 4.8zm11.2-7.4v14.9c0 2.3-1.9 4.3-4.3 4.3h-23.4c-2.4 0-4.3-1.9-4.3-4.3v-15c0-2.3 1.9-4.3 4.3-4.3h3.7l.8-2.3c.4-1.1 1.7-2 2.9-2h8.6c1.2 0 2.5.9 2.9 2l.8 2.4h3.7c2.4 0 4.3 1.9 4.3 4.3zm-8.6 7.5c0-4.1-3.3-7.5-7.5-7.5-4.1 0-7.5 3.4-7.5 7.5s3.3 7.5 7.5 7.5c4.2-.1 7.5-3.4 7.5-7.5z"></path></svg></span><span style="display:inline-block;padding:2px 3px;">Ilya Pavlov</span></a>

The following are more or less the notes I kept for myself if I ever needed to do it again. Which I did,
because we got new laptops. Reproduced here for posterity.

---

# Setup

After Node JS is installed we need to make it play well with Windows:

Open a new PowerShell console as admin. Run the following commands

```
npm install --global --production windows-build-tools
npm config set msvs_version 2015 --global
```

The first command will take a few minutes. It will download Python 2.7 among
other things. Second one will set your toolchain to the appropriate version.


This is roughly what it'll look like at the end - and it _will_ take a few moments,
so go make yourself a nice cup of tea.


```
Starting installation...
Launched installers, now waiting for them to finish.
This will likely take some time - please be patient!

Status from the installers:
---------- Visual Studio Build Tools ----------
Successfully installed Visual Studio Build Tools.
------------------- Python --------------------
Successfully installed Python 2.7
+ windows-build-tools@2.2.1
added 132 packages in 358.755s
```



Now we can install `aglio` globally. In the same admin console, type

```
npm install -g aglio --python=2.7
```

# Compiling Documents

Compiling documentation is as easy as:

```
aglio -i module.apib -o output.html
```

# Conventions

> A reminder that these are my personal / domain-specific notes. If you adopt anything similar for your project
it makes sense to document your own conventions somewhere, so I'll leave those in as an example because, well, why not

Host URLs do not have a trailing slash. For example:

```
FORMAT: 1a
HOST: /api/v1/module
```

Therefore Resource Identifiers must start with a slash:

```
#  [GET /resource]
```


[aglio]: https://github.com/danielgtaylor/aglio
[apb]: https://apiblueprint.org/