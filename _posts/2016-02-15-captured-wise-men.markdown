---
layout: post
title: The Captured Wise Men
date: 2016-02-15
author: Ruslán Ledesma-Garza
summary: Two wise men are prisoners in a tower in solitary confinement. Each holds half of an answer that would save their lives. Eventually, they put together the answer. But how?
---

Here is a puzzle, the kind you find during interviews for software
developer jobs.  Two wise men are prisoners in a tower in solitary
confinement.  Each holds half of an answer that would save their
lives.  Eventually, they put together the answer.  But how?

# Problem

Two wise men were captured by an sadistic king.  They were locked up in
two separate cells in a tower in solitary confinement. 

One of the cells pointed eastwards.  From there you could see half of
the kingdom.  The other cell pointed westwards.  From there you could
see the other half.  Thus, each of the wise men could see a
different half of the kingdom.

Upon imprisonment, the sadistic king told the wise men that he would
set them free if any of them could tell if the cities in his kingdom
were 5 or 8.  A wrong answer or not answering within 7 days of
imprisonment would result in the execution of both wise men.  The wise
men could only give their answer to a jailer who would give them food
each morning.

On the third morning, the wise men were set free.  What answer did they
give and how did they manage to put it together?

# Answer

There are 8 cities in the kingdom.

# Solution

On the first day, we know that the amount of cities that the wise men can see is either 5 or 8.
```asciidoc
\  B | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8
A \  |   |   |   |   |   |   |   |   |
------------------------------------------
  0  |   |   |   |   |   | 5 |   |   | 8
  1  |   |   |   |   | 5 |   |   | 8 |
  2  |   |   |   | 5 |   |   | 8 |   |
  3  |   |   | 5 |   |   | 8 |   |   |
  4  |   | 5 |   |   | 8 |   |   |   |
  5  | 5 |   |   | 8 |   |   |   |   |
  6  |   |   | 8 |   |   |   |   |   |
  7  |   | 8 |   |   |   |   |   |   |
  8  | 8 |   |   |   |   |   |   |   |
```

When a wise man can see 6 cities or more, there are 8 cities in the kingdom.
```asciidoc
\  B | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8
A \  |   |   |   |   |   |   |   |   |
------------------------------------------
  0  |   |   |   |   |   | 5 |   |   | 8
  1  |   |   |   |   | 5 |   |   | 8 |
  2  |   |   |   | 5 |   |   | 8 |   |
  3  |   |   | 5 |   |   | 8 |   |   |
  4  |   | 5 |   |   | 8 |   |   |   |
  5  | 5 |   |   | 8 |   |   |   |   |
  6  |   |   | 8 |   |   |   |   |   |    <--- Wise man A can see 6 cities.
  7  |   | 8 |   |   |   |   |   |   |    <--- Wise man A can see 7 cities.
  8  | 8 |   |   |   |   |   |   |   |    <--- Wise man A can see 8 cities.
                               ^   ^   ^
                               |   |   |
                               |   |   |
                               |   |   Wise man B can see 8 cities.
                               |   Wise man B can see 7 cities.
                               Wise man B can see 6 cities.
```

None of the wise men could see 6 cities or more, because they did not get out on the first day.
```asciidoc
\  B | 0 | 1 | 2 | 3 | 4 | 5 |
A \  |   |   |   |   |   |   |
------------------------------
  0  |   |   |   |   |   | 5 |
  1  |   |   |   |   | 5 |   |
  2  |   |   |   | 5 |   |   |
  3  |   |   | 5 |   |   | 8 |
  4  |   | 5 |   |   | 8 |   |
  5  | 5 |   |   | 8 |   |   |
```

On the second day, when a man can see 3 cities or less, there are 5 cities in the kingdom.
```asciidoc
\  B | 0 | 1 | 2 | 3 | 4 | 5 |
A \  |   |   |   |   |   |   |
------------------------------
  0  |   |   |   |   |   | 5 |    <---
  1  |   |   |   |   | 5 |   |    <---
  2  |   |   |   | 5 |   |   |    <---
  3  |   |   | 5 |   |   | 8 |
  4  |   | 5 |   |   | 8 |   |
  5  | 5 |   |   | 8 |   |   |
       ^   ^   ^
       |   |   |
       |   |   |
```

None of the wise men could see 3 cities or more, because they did not get out on the second day.
```asciidoc
\  B | 3 | 4 | 5 |
A \  |   |   |   |
------------------
  3  |   |   | 8 |
  4  |   | 8 |   |
  5  | 8 |   |   |
```

Thus, on the third day, there is only one possibility: there are 8 cities in the kingdom.


# Summary

I really liked this one because the idea of communication is
counterintuitive given that the wise men are in solitary confinement.
