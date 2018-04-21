---
layout: post
title:  "Paginating Queries in Oracle SQL"
subtitle: "Limiting results in JDBC the quick way"
date:   2018-02-26 15:00:00 +0100
categories: guides
tags:
- oracle
- jdbc
- java
- sql
---


Here's what I've used a couple of times to paginate my queries. It adds a limit and offset to your query results so you don't have to do it Java-side:

```sql
SELECT * FROM (SELECT ROWNUM rnum, paginateInner.* FROM (%s) paginateInner WHERE ROWNUM <= ? ) WHERE rnum >= ?
```

This is a Java string. I inject the actual query in it like this:

```java
final String sql = String.format(PAGINATION_WRAPPER, actual_query_sql);
```

Then add the final two parameters to your prepared statement:

```java
try (final PreparedStatement statement = connection.getPreparedStatement(sql)) {
	int counter = 1;

	// ...set all the other parameters...

	statement.setInt(count++, limit);  //limits go first
	statement.setInt(count++, offset);
}
```

If your `limit` isn't known in advance, you can use just omit the inner query:

```sql
SELECT * FROM (%s) WHERE rnum >= ?
```

And that's it.

![](/assets/images/posts/anastasia-zhenina-65700-unsplash.jpg)
<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px;" href="https://unsplash.com/@disguise_truth?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Download free do whatever you want high-resolution photos from Anastasia Zhenina"><span style="display:inline-block;padding:2px 3px;"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-1px;fill:white;" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M20.8 18.1c0 2.7-2.2 4.8-4.8 4.8s-4.8-2.1-4.8-4.8c0-2.7 2.2-4.8 4.8-4.8 2.7.1 4.8 2.2 4.8 4.8zm11.2-7.4v14.9c0 2.3-1.9 4.3-4.3 4.3h-23.4c-2.4 0-4.3-1.9-4.3-4.3v-15c0-2.3 1.9-4.3 4.3-4.3h3.7l.8-2.3c.4-1.1 1.7-2 2.9-2h8.6c1.2 0 2.5.9 2.9 2l.8 2.4h3.7c2.4 0 4.3 1.9 4.3 4.3zm-8.6 7.5c0-4.1-3.3-7.5-7.5-7.5-4.1 0-7.5 3.4-7.5 7.5s3.3 7.5 7.5 7.5c4.2-.1 7.5-3.4 7.5-7.5z"></path></svg></span><span style="display:inline-block;padding:2px 3px;">Anastasia Zhenina</span></a>