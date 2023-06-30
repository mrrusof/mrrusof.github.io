---
layout: post
title: 'The Blocks Problem'
date: 2016-03-18
edited: 2016-10-21
author: Ruslan Ledesma-Garza
summary: 'Interpret a language of four commands to model the interaction of a robot arm with its environment. An exercise in keeping your solution simple.'
---

The Blocks Problem is [problem 101 in the UVa Online
Judge](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=37).
Even though I include the problem description in this post, I
encourage you to visit the [UVa Online
Judge](https://uva.onlinejudge.org/index.php) because there you will
be able to submit your solution to get it judged.

# Problem

The problem is to parse a series of commands that instruct a robot arm
in how to manipulate blocks that lie on a flat table. Initially there
are `n` blocks on the table (numbered from `0` to `n - 1`) with block
`b_i` adjacent to block `b_i + 1` for all `0 <= i < n - 1` as shown
in the diagram below:

{% highlight asciidoc %}
-------------------------------------------
|     |     |     |     |     |     |     |
|  0  |  1  |  2  |  3  |  4  | ... | n-1 |
|     |     |     |     |     |     |     |
-------------------------------------------
{% endhighlight %}

The valid commands for the robot arm that manipulates blocks are:

- `move a onto b`
where `a` and `b` are block numbers, puts block `a` onto block `b` after
returning any blocks that are stacked on top of blocks `a` and `b` to
their initial positions.

- `move a over b`
where `a` and `b` are block numbers, puts block `a` onto the top of the
stack containing block `b`, after returning any blocks that are stacked
on top of block `a` to their initial positions.

- `pile a onto b`
where `a` and `b` are block numbers, moves the pile of blocks consisting
of block `a`, and any blocks that are stacked above block `a`, onto block
`b`. All blocks on top of block `b` are moved to their initial positions
prior to the pile taking place. The blocks stacked above block `a`
retain their order when moved.

- `pile a over b`
where `a` and `b` are block numbers, puts the pile of blocks consisting of
block `a`, and any blocks that are stacked above block `a`, onto the top
of the stack containing block `b`. The blocks stacked above block `a`
retain their original order when moved.

- `quit`
terminates manipulations in the block world.

Any command in which `a = b` or in which `a` and `b` are in the same
stack of blocks is an illegal command. All illegal commands should be
ignored and should have no effect on the configuration of blocks.

# Input 

The input begins with an integer `n` on a line by itself representing the number of blocks in the block world. You may assume that `0 < n < 25`.
The number of blocks is followed by a sequence of block commands, one
command per line. Your program should process all commands until the
`quit` command is encountered.

You may assume that all commands will be of the form specified
above. There will be no syntactically incorrect commands.

# Output 

The output should consist of the final state of the blocks world. Each
original block position numbered `i` (`0 <= i < n)` where `n` is the
number of blocks) should appear followed immediately by a colon. If
there is at least a block on it, the colon must be followed by one
space, followed by a list of blocks that appear stacked in that
position with each block number separated from other block numbers by
a space. Don't put any trailing spaces on a line.

There should be one line of output for each block position (i.e., `n`
lines of output where `n` is the integer on the first line of input).

# Sample Input 

{% highlight asciidoc %}
10
move 9 onto 1
move 8 over 1
move 7 over 1
move 6 over 1
pile 8 over 6
pile 8 over 5
move 2 over 1
move 4 over 9
quit
{% endhighlight %}

# Sample Output 

{% highlight asciidoc %}
 0: 0
 1: 1 9 2 4
 2:
 3: 3
 4:
 5: 5 8 7 6
 6:
 7:
 8:
 9:
{% endhighlight %}

# Analysis

Commands are either actions or quit.

We analyze the syntax and semantics of actions to determine a set of
primitive operations on blocks.
Consider the following action.
{% highlight asciidoc %}
move 8 over 9
---- - ---- -
 |   |  |   |
verb |  |   |
     |  |   |
 object |   |
        |   |
preposition |
            |
       complement
{% endhighlight %}
Syntactically, each action consists of a verb, an object, a
preposition, and a complement.

Semantically, each action consists of a sequence of three primitive
operations.
Consider the following state.
{% highlight asciidoc %}
 0: 0
 1: 1 9 2 4
 2:
 3: 3
 4:
 5: 5 8 7 6
 6:
 7:
 8:
 9:
{% endhighlight %}
The verb indicates the first operation.
The first operation is either put back the blocks on top of the
object or keep them relative to the object.
For the state, `move 8` indicates that blocks 7 and 6 be put back in
the following way.
{% highlight asciidoc %}
 0: 0
 1: 1 9 2 4
 2:
 3: 3
 4:
 5: 5 8
 6: 6
 7: 7
 8:
 9:
{% endhighlight %}
The preposition indicates the second operation.
The second operation applies to the complement in an analogous way.
For the previous state, `over 9` indicates that the blocks on top of 9
be kept where they are, thus the previous state remains unmodified.

The third operation is the same for all actions and consists in
placing the object and everything above it on top of the stack that contains the complement.
For the previous state, the third operation gives the following state.
{% highlight asciidoc %}
 0: 0
 1: 1 9 2 4 8
 2:
 3: 3
 4:
 5: 5
 6: 6
 7: 7
 8:
 9:
{% endhighlight %}

# Solution

We implement the primitive operations and then apply them for each
action.

For verb `move` and preposition `onto`, we put back blocks on top of a
given block by means of function `put_back`.
{% highlight c %}
#define MAX 25

typedef struct block {
  struct block *above;
  struct block *below;
  int table_loc;
  int value;
} block;

block *table[MAX];

void put_back(block *a) {
  block *b;
  while(a->above != NULL) {
    b = a->above;
    a->above = NULL; /* .......... A */
    b->below = NULL; /* .......... B */
    b->table_loc = b->value; /* .. C */
    table[b->value] = b; /* ...... D */
    a = b;
  }
}
{% endhighlight %}
Array `table` indicates the state of the table by pointing to the
bottom of each stack located on the table.
For a given instance of `block`, pointer `above` indicates the block
above if any and pointer `below` a block below.
Integer `table_loc` indicates the location on the table of the given
block.
For a given block, `put_back` iterates the blocks on top.
For each block on top, `put_back` removes the block
from the current stack (lines A and B), sets the current location of
the block to its initial position (line C), and puts the block back in
its initial position (line D).

For verb `pile` and preposition `over`, we keep blocks on top of a
given block by doing nothing.

We implement the primitive operation that is common to all actions
in function `stack`.
{% highlight c %}
block *top(struct block *a) {
  while(a->above != NULL) a = a->above;
  return a;
}

void stack(block *a, block *b) {
  block *top_b = top(b);
  block *a_below = a->below;
  int table_loc = b->table_loc;

  /* Remove stack a from its current position. */
  if(a_below != NULL) a_below->above = NULL;
  else table[a->value] = NULL;

  /* Stack stack a onto the top of stack b. */
  top_b->above = a;
  a->below = top_b;

  /* Update stack reference for block a and blocks above a. */
  while(a != NULL) {
    a->table_loc = table_loc;
    a = a->above;
  }
}
{% endhighlight %}

We apply functions `put_back` and `stack` to actions in the following
main loop.
{% highlight c %}
while(1) {

  /* Read the verb. */
  Ss(verb);

  /* Exit when verb is quit. */
  if(verb[0] == 'q') break; /* ............................. A */

  /* Read the object. */
  Si(v);
  a = blocks[v]; /* ........................................ B */
  
  /* Read the preposition. */
  Ss(prep);

  /* Read the complement. */
  Si(v);
  b = blocks[v]; /* ........................................ C */

  /* Ignore action if it is illegal. */
  if(a->table_loc == b->table_loc) continue; /* ............ D */

  /* When verb is `move`, put back that are above the object `a`. */
  if(verb[0] == 'm') put_back(a); /* ....................... E */

  /* When preposition is `onto`, put back the blocks that are above the complement `b`. */
  if(prep[1] == 'n') put_back(b); /* ....................... F */

  /* Pile object `a` over complement `b`. */
  stack(a, b); /* .......................................... G */
}
{% endhighlight %}

We stop as soon as the verb is `quit` (line A).
Array `blocks` holds a reference to each block in the world, so that
we can get a hold of any block in constant time (lines B and C).
A command is illegal when blocks `a` and `b` have the same location on
the table (line D).
For the first operation of the action, we apply `put_back` for verb
`move` and do nothing (line E).
Similarly for the second operation (line F).
We always stack `a` on top of the stack that contains `b` (line G).

Our solution is the following.
{% highlight c %}
#include <stdio.h>
#include <stdlib.h>

#define Si(a) scanf("%d", &a)
#define Ss(a) scanf("%s", a)

#define MAX 25

typedef struct block {
  struct block *above;
  struct block *below;
  int table_loc;
  int value;
} block;

block *table[MAX];

void put_back(block *a) {
  block *b;
  while(a->above != NULL) {
    b = a->above;
    a->above = NULL;
    b->below = NULL;
    b->table_loc = b->value;
    table[b->value] = b;
    a = b;
  }
}

block *top(struct block *a) {
  while(a->above != NULL) a = a->above;
  return a;
}

void stack(block *a, block *b) {
  block *top_b = top(b);
  block *a_below = a->below;
  int table_loc = b->table_loc;

  /* Remove stack a from its current position. */
  if(a_below != NULL) a_below->above = NULL;
  else table[a->value] = NULL;

  /* Stack stack a onto the top of stack b. */
  top_b->above = a;
  a->below = top_b;

  /* Update stack reference for block a and blocks above a. */
  while(a != NULL) {
    a->table_loc = table_loc;
    a = a->above;
  }
}

void print_table(int n) {
  block *a;
  int i;
  for(i = 0; i < n; i++) {
    printf("%d:", i);
    for(a = table[i]; a != NULL; a = a->above)  {
      printf(" %d", a->value);
    }
    printf("\n");
  }
}

int main() {
  int n, i, v;
  char verb[4], prep[4];
  block *blocks[MAX];
  block *a, *b;

  for(i = 0; i < MAX; i++)
    table[i] = blocks[i] = NULL;

  /* Read number of blocks `n`. */
  Si(n);

  /* Initialize world with `n` blocks. */
  for(i = 0; i < n; i++) {
    table[i] = blocks[i] = (block *) malloc(sizeof(block));
    blocks[i]->above = NULL;
    blocks[i]->below = NULL;
    blocks[i]->table_loc = i;
    blocks[i]->value = i;
  }

  while(1) {

    /* Read the verb. */
    Ss(verb);

    /* Exit when verb is quit. */
    if(verb[0] == 'q') break;

    /* Read the object. */
    Si(v);
    a = blocks[v];
    
    /* Read the preposition. */
    Ss(prep);

    /* Read the complement. */
    Si(v);
    b = blocks[v];

    /* Ignore action if it is illegal. */
    if(a->table_loc == b->table_loc) continue;

    /* When verb is `move`, put back that are above the object `a`. */
    if(verb[0] == 'm') put_back(a);

    /* When preposition is `onto`, put back the blocks that are above the complement `b`. */
    if(prep[1] == 'n') put_back(b);

    /* Pile object `a` over complement `b`. */
    stack(a, b);
  }

  print_table(n);

  for(i = 0; i < n; i++)
    free(blocks[i]);

  return 0;
}
{% endhighlight %}

# Summary

We took a compositional approach to solving the problem.
We identified primitive operations and implemented each action as a
sequence of primitive operations.
We avoid searching for blocks by keeping a reference to each block.
We avoid computing the location of blocks on the table by tagging each
block with its location.


{% include share.html %}


{% include comments.html %}
