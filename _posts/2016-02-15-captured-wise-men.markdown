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

Two wise men were captured by an evil king.  They were locked up in
two separate cells in a tower in solitary confinement.

The wise men came from far away and did not know the kingdom of the
sadistic king.  They were brought blindfolded and isolated all the way
from their origin to the cells.

One of the cells pointed eastwards.  From there you could
see half of the kingdom.  The other cell pointed westwards.  From
there you could see the other half.  Thus, each of the wise men could
see a different half of the kingdom.

The wise men were imprisoned one morning.  Upon imprisonment, the
evil king told the wise men the following.  They would be free if
any of them could tell if the cities in the kingdom were 5 or 8. From
each cell you could see half of the kingdom.  A wrong
answer or not answering within 7 days of imprisonment would result in
the execution of both wise men.  The wise men could only give their
answer to a jailer who would give them food each evening.

On the third evening, the wise men were set free.  What answer did they
give and how did they manage to put it together?


# Answer

The wise men said that there were 8 cities in the kingdom.


# Solution

When the wise men are imprisoned, we learn two restrictions on the
answer.
One rule is that there are either 5 or 8 cities in the kingdom.
The other rule is that the answer is the sum of what each wise man sees.
The following table illustrates feasible solutions to the problem.
```asciidoc
\  W | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8
E \  |   |   |   |   |   |   |   |   |
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
The first row and first column indicates what each man sees.
Each cell that is not empty indicates a feasible solution.
An example of feasible solution is when the man in the East sees
5 cities and the man in the West sees 3.
For this solution the answer is 8 cities.
An example of an infeasible solution is when the man the East sees 5
cities and the man in the West sees 2.
For this solution the answer is 7 cities, which is impossible given that
there are either 5 or 8 cities.

After the first dinner, we learn that neither of the wise men is
looking at more than 5 cities.
The reason is that the wise men were not set free after the first
dinner.
The rule is that if a wise man sees 6 cities or more, that man
answers that there are 8 cities in the kingdom.
We illustrate the rule by labeling our table of feasible solutions.
```asciidoc
\  W | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8
E \  |   |   |   |   |   |   |   |   |
------------------------------------------
  0  |   |   |   |   |   | 5 |   |   | 8
  1  |   |   |   |   | 5 |   |   | 8 |
  2  |   |   |   | 5 |   |   | 8 |   |
  3  |   |   | 5 |   |   | 8 |   |   |
  4  |   | 5 |   |   | 8 |   |   |   |
  5  | 5 |   |   | 8 |   |   |   |   |
  6  |   |   | 8 |   |   |   |   |   |    <--- Wise man East can see 6 cities.
  7  |   | 8 |   |   |   |   |   |   |    <--- Wise man East can see 7 cities.
  8  | 8 |   |   |   |   |   |   |   |    <--- Wise man East can see 8 cities.
                               ^   ^   ^
                               |   |   |
                               |   |   |
                               |   |   Wise man West can see 8 cities.
                               |   Wise man West can see 7 cities.
                               Wise man West can see 6 cities.
```
We label rows and columns where a man sees 6 cities or more.
Any labeled row corresponds to one feasible solution, because there is
only one cell that is not empty.
All feasible solutions in those rows correspond to answer 8.
The same is true for the labeled columns.

Given that neither wise man is looking at 6 or more cities, we remove
those solutions from our table of feasible solutions.
```asciidoc
\  W | 0 | 1 | 2 | 3 | 4 | 5
E \  |   |   |   |   |   |
------------------------------
  0  |   |   |   |   |   | 5
  1  |   |   |   |   | 5 |
  2  |   |   |   | 5 |   |
  3  |   |   | 5 |   |   | 8
  4  |   | 5 |   |   | 8 |
  5  | 5 |   |   | 8 |   |
```

After the second dinner, the wise men learn that none of them is
looking at less than 3 cities.
The reason is that the the wise men were not set free after the second
dinner.
The rule is that if a wise man sees 2 cities or less, that man answers
that there are 5 cities in the kingdom.
We illustrate the rule by labeling our table of feasible solutions.
```asciidoc
\  W | 0 | 1 | 2 | 3 | 4 | 5
E \  |   |   |   |   |   |
------------------------------
  0  |   |   |   |   |   | 5  <--- Wise man East can see 0 cities.
  1  |   |   |   |   | 5 |    <--- Wise man East can see 1 cities.
  2  |   |   |   | 5 |   |    <--- Wise man East can see 2 cities.
  3  |   |   | 5 |   |   | 8
  4  |   | 5 |   |   | 8 |
  5  | 5 |   |   | 8 |   |
       ^   ^   ^
       |   |   |
       |   |   |
       |   |   Wise man West can see 2 cities.
       |   Wise man West can see 1 cities.
       Wise man West can see 0 cities.
```
We label rows and columns where a man sees 2 cities or less.
Any labeled row corresponds to one feasible solution.
All feasible solutions in those rows correspond to answer 5.
The same is true for the labeled columns.

Given that neither wise man is looking at 2 or less cities, we remove
those solutions from our table of feasible solutions.
```asciidoc
\  W | 3 | 4 | 5
E \  |   |   |
------------------
  3  |   |   | 8
  4  |   | 8 |
  5  | 8 |   |
```

In the third dinner, the wise men know that there are 8 cities.
The reason is that all remaining feasible solutions correspond to
answer 8.


# Summary

The wise men solved the problem by identifying feasible solutions and
eliminating solutions as they became unfeasible.  Solutions became
unfeasible with each day that they remained imprisoned.

I really liked this problem because the first time I heard it, it
sounded impossible for them to save their lives.
The reason is that I pictured solitary imprisonment as a condition that
barred all form of communication.

What was your experience trying to solve the problem? Let me know in
the comments.

# Acknowledgements

Thanks to Eduardo Romano for challenging me to solve this problem.
