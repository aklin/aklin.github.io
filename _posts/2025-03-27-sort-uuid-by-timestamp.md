---
layout: post
title:  "Can I sort UUIDs by their creation time?"
date:   2025-03-27 00:11 +0100
categories: shorts
tags:
- uuid 
- uuidv7
- sorting
- timestamp
---

Yes, if you use UUIDv7, which is designed to do just that. This will not work
with UUIDv4, which has been the default option for many codebases.

To decode the timestamp from an existing UUIDv7 value, you need the first 48 bits 
(which works out as the first 12 characters), and parse it with your favourite
date-time library.

```js
const uuidV7 = '0195d72a-7401-7b71-a6b5-49d435658b40'.replaceAll('-', '');
// 16 is the radix, 12 is the number of characters (12*4 = 48)
const timestamp = new Date ( parseInt (uuidV7.substr(0, 12), 16));
```

The result of `parseInt` is `1743071638529`, and `timestamp` should be
`Thu Mar 27 2025 10:33:58 GMT+0000 (Greenwich Mean Time)`.
```

