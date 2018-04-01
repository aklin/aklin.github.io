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

> A reminder that these are from my personal / domain-specific notes. If you adopt anything similar for your project
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