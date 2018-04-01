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
