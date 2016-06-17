---
layout: post
title: Why does Heap's algorithm work?
date: 2016-06-15
author: Ruslan Ledesma-Garza
summary: Permutations are easy, right?
---

If you are looking for an explanation of why Heap's algorithm constructs all permutations, keep reading.

Heap's algorithm constructs all permutations of a given sequence.
Heap's algorithm is efficient because it constructs each permutation from the previous by swapping two elements.
Heap's algorithm is more simple than the also efficient [Steinhaus-Johnson-Trotter algorithm](https://en.wikipedia.org/wiki/Steinhaus%E2%80%93Johnson%E2%80%93Trotter_algorithm)  because it does not compute an offset for the pairs that swaps.

Despite its virtues, it is not evident why Heap's algorithm constructs all permutations.
[Heap](#heap) did give a reason why his algorithm works, but did not explain the reason.
Popular references on the Internet do not provide an explanation either.

If we understand why Heap's algorithm works, we might understand other problems like how to modify Heap's algorithm to distribute work amongst several parallel jobs or compute only relevant permutations.

# How does Heap's algorithm work?

For an input sequence of $$n$$ elements, Heap's algorithm applies a sequence of $$n! - 1$$ swaps to produce the remaining $$n! - 1$$ permutations.
For example, for sequence `123`, Heap's algorithm applies the following 5 swaps.

{% highlight asciidoc %}
 0 |  |  | 123
 1 |--|  | 213
 2 |-----| 312
 3 |--|  | 132
 4 |-----| 231
 5 |--|  | 321
{% endhighlight %}

Given sequence `123`, swap 1 constructs the other permutation where `3` is in the last position.
Swap 2 moves `2` to the last position to construct one permutation with `2` in the last position and swap 3 constructs the other permutation.
Swap 4 moves `1` to the last position to construct one permutation with `1` in the last position and swap 5 constructs the other.
By moving each element to the last position and constructing the remaining permutations, Heap's algorithm adjoins each element to all permutations of the other two elements.

Heap's algorithm consists of the following steps.
For given input sequence `12...n` do the following.

1. Permute the elements in positions $$1$$ to $$n - 1$$ by applying this algorithm to those elements.
2. Apply the following steps $$n - 1$$ times. Increment counter $$i = 1$$ after each iteration.
   1. Swap element in position $$n$$ with one of the other jjelements in the following way.
      1. If $$n$$ is odd, swap elements in positions $$1$$ and $$n$$.
      2. If $$n$$ is even, swap elements in position $$i$$ and $$n$$.
   2. Permute the elements in positions $$1$$ to $$n - 1$$ by applying this algorithm to those elements.

The algorithm applies $$n! - 1$$ swaps because step 1 applies $$(n - 1)! - 1$$ swaps and the loop applies $$(n - 1)(n - 1)!$$ swaps. For example, for input sequence `123`, step 1 corresponds to swap 1, the first iteration of the loop corresponds to swaps 2 and 3, and the second iteration of the loop corresponds to swaps 4 and 5.
Given a permutation where the element in position $$n$$ is $$e$$, recursive steps 1 and 2.2 construct the remaining $$(n - 1)! - 1$$ permutations where $$e$$ is in the last position by adjoining $$e$$ to the remaining $$(n - 1)! - 1$$ permutations of the rest of the elements.
To construct all permutations of $$n$$ elements, it is crucial that step 2.1 places a different element in position $$n$$ every time, which it does.
Understanding this property is fundamental to understanding why Heap's algorithm works.
We explain this property in the next section.

The corresponding pseudocode is the following.
Given input sequence `S` and its corresponding length `n`, procedure `Heap` constructs the remaining $$n! - 1$$ permutaitons of `S`.

{% highlight asciidoc %}
Heap(S, n)
 10: IF n > 2 THEN
 20:   Heap(S, n - 1)
 30: FOR i := 1 TO n - 1
 40:   IF n > 2 THEN
 50:     Heap(S, n - 1)
 60:   IF n is even THEN
 70:     swap S[i] and S[n]
 80:   ELSE
 90:     swap S[0] and S[n]
100:   process S
{% endhighlight %}

Lines 20 and 30 correspond to step 1 of the algorithm.
Lines 40 to 90 correspond to the loop of the algorithm.
Line 100 is an instruction that corresponds to the processing of the current permutation to solve a particular problem, for example [brute search all solution candidates of another problem](http://marknelson.us/2002/03/01/next-permutation/).

Consider the following C implementation for reference.

{% highlight c %}
#include <stdio.h>

#define Ss(s) scanf("%s", s)

#define MAX_LEN 1000

int count;

void print_swap(int a, int b, int n) {
  int i;
  printf("%5d ", count++);
  for(i = 0; i < n - 1; i++)
    if(a == i)
      printf("|--");
    else if(a < i && i < b)
      printf("---");
    else
      printf("|  ");
  printf("|");
}

void swap(char *s, int a, int b) {
  char t = s[a];
  s[a] = s[b];
  s[b] = t;
}

void h(char *s, int n, int len) {
  int i;
  for(i = 0; ; i++) {
    if(n > 2)
      h(s, n - 1, len);
    if(i == n - 1)
      break;
    if(n % 2 == 0) {
      swap(s, i, n - 1);
      print_swap(i, n - 1, len);
    } else {
      swap(s, 0, n - 1);
      print_swap(0, n - 1, len);
    }
    printf(" %s N = %5d\n", s, n);
  }
}

void heap(char *s, int len) {
  if(len < 2) {
    printf("The only permutation is %s\n", s);
    return;
  }
  count = 1;
  print_swap(-1, -1, len);
  printf(" %s\n", s);
  h(s, len, len);
}

int main() {
  char s[MAX_LEN];
  int len;
  Ss(s);
  for(len = 0; s[len] != '\0'; len++);
  heap(s, len);
  return 0;
}
{% endhighlight %}


# Why does Heap's algorithm construct all permutations?

Heap's algorithm constructs all permutations because it adjoins each element to each permutation of the rest of the elements.
Steps 1 and 2.2 of the algorithm take care of adjoining.
Step 2.1 takes care of placing a different element in the last position each time.

It may not be evident that Heap's algorithm places a different element in the last position each time.
There are two cases for a given input sequence `12...n`, either `n` is odd or even.
When `n` is odd, the sequence of elements for position `n` is the following.

{% highlight asciidoc %}
n, 2, 3, ..., n - 1, 1
{% endhighlight %}

The sequence consists of all elements.
For example, consider the execution of Heap's algorithm for sequence `12345`.

{% highlight asciidoc %}
   1 |  |  |  |  | 12345
...
  24 |--|  |  |  | 23415
  25 |-----------| 53412
...
  48 |--|  |  |  | 34152
  49 |-----------| 24153
...
  72 |--|  |  |  | 41523
  73 |-----------| 31524
...
  96 |--|  |  |  | 15234
  97 |-----------| 45231
...
 120 |--|  |  |  | 52341
{% endhighlight %}

The sequence of elements for the last position is `5, 2, 3, 4, 1`.
When `n` is even, the sequence of elements for position `n` is the following.

{% highlight asciidoc %}
n, n - 1, 2, 3, ..., n - 2, 1
{% endhighlight %}

This sequence also consists of all elements.
For example, consider the execution of Heap's algorithm for sequence `123456`.

{% highlight asciidoc %}
   1 |  |  |  |  |  | 123456
...
 120 |--|  |  |  |  | 523416
 121 |--------------| 623415
...
 240 |--|  |  |  |  | 123465
 241 |  |-----------| 153462
...
 360 |--|  |  |  |  | 653412
 361 |  |  |--------| 652413
...
 480 |--|  |  |  |  | 152463
 481 |  |  |  |-----| 152364
...
 600 |--|  |  |  |  | 652314
 601 |  |  |  |  |--| 652341
...
 720 |--|  |  |  |  | 452361
{% endhighlight %}



# References


<dl>
  <dt id="heap">
  Heap
  </dt>
  <dd>
  http://comjnl.oxfordjournals.org/content/6/3/293.full.pdf
  </dd>
</dl>
- Eric Martin's lecture notes on Principles of Programming.
https://webcms3.cse.unsw.edu.au/COMP9021/15s2/resources/1544