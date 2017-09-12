---
layout: post
title:  "Two-dimensional grid with Oracle SQL"
subtitle: "Finding gaps in grids"
date:   2017-09-12 13:44:11 +0100
categories: projects
tags:
- oracle
- oracle11g
- sql
---


I found a fun way to generate a 2D grid in Oracle SQL and I thought I'd share.

Let's say we have a rectangular container of X by Y dimensions, which can fit X\*Y contents. Since that table will not be used
in any examples, I have chosen to omit it. The `Contents` table however is important, and is defined as:

```
Contents
-------
id [PK]
parent_id [FK]
pos_x
pos_y
```

Simple enough and does the job. Now let's say that we want to check if an X/Y spot is available; that's a simple `SELECT COUNT(*)` query
I won't bother with - but which works perfectly well for single spot checking.

The only problem is that our spot-check query doesn't scale. What if a client wants to get _all_ available X/Y positions? This is the problem
I had to solve a few weeks ago, and this is how I did it.


## The Problem

The code I was looking to replace would do the following:

1. Fetch all occupied X/Y spots in a container
1. Iterate through a `boolean` array and mark the ones occupied.

A workable, quick-n-dirty solution, but for various reasons it needed to be replaced.

## Generating a 2D Grid

At first I thought I should translate the Java logic into the SQL, so I started by making a grid. Let's make it 3x3 for simplicity.
Initially I started with a single side:

```sql
SELECT rownum X FROM dual  CONNECT BY LEVEL <= 3
```

then I joined this query back onto a copy of itself, and named the two sub-queries appropriately:

```sql
SELECT *
FROM 
    (SELECT rownum X FROM dual  CONNECT BY LEVEL <= 3 ) xaxis
INNER JOIN 
    (SELECT rownum Y FROM dual  CONNECT BY LEVEL <=3 ORDER BY 1) yaxis
ON xaxis.X <> yaxis.Y OR xaxis.X = yaxis.Y
```

The line: `xaxis.X <> yaxis.Y OR xaxis.X = yaxis.Y` says _if X is not equal to Y, but also if X is equal to Y_. Omit either clause
and you get incomplete results.

The `ORDER BY 1` on the `Y` axis ensures we get the `X` values to increment first, which is a business rule in my case.

This should give us something like this:

```
X | Y
-----
1   1
2   1
3   1
1   2
2   2
3   2
1   3
2   3
3   3
```


## Adding Content data

Now we need to subtract occupied positions from all available places. After trying and failing to join the query above with a
`SELECT` on `Contents`, I ended up using the [`MINUS`][minus-url] operator, which

> returns only unique rows returned by the first query but not by the second

now that sounds like it might do it. Indeed,

```sql
select X, Y
from 
    (select rownum X from dual  CONNECT BY LEVEL <= 3 ) xaxis
inner join 
    (select rownum Y from dual  CONNECT BY LEVEL <=3 order by 1) yaxis
on xaxis.X <> yaxis.Y OR xaxis.X = yaxis.Y

MINUS

select pos_x, pos_y FROM Contents WHERE parent_id = ?;

```

generates a 3x3 grid and returns only unoccupied X/Y coordinates.


That's it. I learned something new about Oracle SQL, and I enjoyed doing it.


## Alternative Solution

Now, I did try to "cheat" by using StackOverflow, but right before posting my question I found the answer. I decided to post
it anyway in case anyone else could come up with something better.

Indeed [someone has][so-answer]. I quote Boneist's answer here

> If you are only doing it for a single parent_id at a time, you would do

```sql
WITH CONTENTS AS (SELECT 1 parent_id, 2 pos_x, 2 pos_y FROM dual UNION ALL
                  SELECT 2 parent_id, 2 pos_x, 1 pos_y FROM dual)
SELECT xaxis.x,
       yaxis.y
FROM   ((SELECT LEVEL x FROM dual CONNECT BY LEVEL <= 3) xaxis
        CROSS JOIN (SELECT LEVEL y FROM dual CONNECT BY LEVEL <= 3) yaxis)
       LEFT OUTER JOIN CONTENTS c ON c.pos_x = xaxis.x AND c.pos_y = yaxis.y AND c.parent_id = 1
WHERE  c.parent_id IS NULL
ORDER BY x, y;
```

But he didn't stop there. What if someone wanted one query for many parents?

> Alternatively, if you want to run it for all parent_ids, you could use a partitioned outer join like so

```sql
WITH CONTENTS AS (SELECT 1 parent_id, 2 pos_x, 2 pos_y FROM dual UNION ALL
                  SELECT 2 parent_id, 2 pos_x, 1 pos_y FROM dual)
SELECT c.parent_id,
       xaxis.x,
       yaxis.y
FROM   ((SELECT LEVEL x FROM dual CONNECT BY LEVEL <= 3) xaxis
        CROSS JOIN (SELECT LEVEL y FROM dual CONNECT BY LEVEL <= 3) yaxis)
       LEFT OUTER JOIN CONTENTS c PARTITION BY (c.parent_id) ON c.pos_x = xaxis.x AND c.pos_y = yaxis.y
WHERE  c.pos_x IS NULL
AND    c.pos_y IS NULL
ORDER BY c.parent_id,
         xaxis.x,
         yaxis.y;
```

Due to workload I haven't been able to test either of his solutions, but I'm planning to once I get some downtime.
Thank you Boneist in any case.

[so-answer]: https://stackoverflow.com/a/46001956/2194007
[minus-url]: https://docs.oracle.com/cd/B19306_01/server.102/b14200/queries004.htm