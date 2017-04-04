---
layout: post
title: Why does Heap's algorithm work?
date: 2016-06-17
edited: 2016-06-20
author: Ruslan Ledesma-Garza
summary: Heap's algorithm for constructing all permutations is efficient and simple but not easy to understand. This article explains Heap's algorithm by example.
---

If you are looking for an explanation of why Heap's algorithm constructs all permutations, keep reading.

[Heap](#heap)'s algorithm constructs all permutations of a given sequence.
Heap's algorithm is efficient because it constructs each permutation from the previous by swapping two elements.
Heap's algorithm is more simple than the also efficient [Steinhaus-Johnson-Trotter algorithm](https://en.wikipedia.org/wiki/Steinhaus%E2%80%93Johnson%E2%80%93Trotter_algorithm)  because it does not compute an offset for the pairs that swaps.

Despite its virtues, it is not evident why Heap's algorithm constructs all permutations.
[Heap](#heap) did give a reason why his algorithm works, but did not explain the reason.
Popular references on the Internet like [Wikipedia](https://en.wikipedia.org/wiki/Heap%27s_algorithm) and [Sedgewick](#sedgewick) do not provide an explanation either.
A thorough search reveals [Eric Martin's lecture notes on Heap's algorithm](#martin).
The notes explain Heap's algorithm using a formal approach.
This article explains Heap's algorithm by example.
This article also provides a sample C implementation.

If we understand why Heap's algorithm works, we might understand other problems like how to modify Heap's algorithm to distribute work amongst several parallel jobs or compute only relevant permutations efficiently.

If you are interested in an iterative version of Heap's algorithm or permutation algorithms in general, have a look at [Sedgewick's survey on permutation algorithms](#sedgewick).


# How does Heap's algorithm work?

For an input sequence of $@n@$ elements, Heap's algorithm fixes the element in the last position and constructs all permutations for the rest of the elements in place. After that, the algorithm swaps the element in the last position with one of the rest and repeats the process.
For example, for sequence `123`, Heap's algorithm proceeds in the following way.

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

[Heap](#heap)'s algorithm as explained in his original work consists of the following steps.
For given input sequence `12...n` do the following.

<ol class="numbered-ol">
  <li class="numbered-li">
    Permute the elements in positions $@1@$ to $@n - 1@$ by applying this algorithm to those elements.
  </li>
  <li class="numbered-li">
    Apply the following steps $@n - 1@$ times. Increment counter $@i = 1@$ after each iteration.
    <ol class="numbered-ol">
      <li class="numbered-li">
        Swap element in position $@n@$ with one of the other elements in the following way.
        <ol class="numbered-ol">
          <li class="numbered-li">
            If $@n@$ is odd, swap elements in positions $@1@$ and $@n@$.
          </li>
          <li class="numbered-li">
            If $@n@$ is even, swap elements in position $@i@$ and $@n@$.
          </li>
        </ol>
      </li>
      <li class="numbered-li">
        Permute the elements in positions $@1@$ to $@n - 1@$ by applying this algorithm to those elements.
      </li>
    </ol>
  </li>
</ol>

The algorithm applies $@n! - 1@$ swaps. The reason is that step 1 applies $@(n - 1)! - 1@$ swaps and the loop applies $@(n - 1)(n - 1)!@$ swaps. For example, for input sequence `123`, step 1 corresponds to swap 1, the first iteration of the loop corresponds to swaps 2 and 3, and the second iteration of the loop corresponds to swaps 4 and 5.
Given a permutation where the element in position $@n@$ is $@e@$, recursive steps 1 and 2.2 construct the remaining $@(n - 1)! - 1@$ permutations where $@e@$ is in the last position by adjoining $@e@$ to the remaining $@(n - 1)! - 1@$ permutations of the rest of the elements.
To construct all permutations of $@n@$ elements, it is crucial that step 2.1 places a different element in position $@n@$ every time, which it does.
Understanding this property is fundamental to understanding why Heap's algorithm works.
We explain this property in the next section.

The corresponding pseudocode is the following.
Given input sequence `S` and its corresponding length `n`, procedure `Heap` constructs the remaining $@n! - 1@$ permutations of `S`.

{% highlight asciidoc %}
Heap(S, n)
 10: IF n > 2 THEN
 20:   Heap(S, n - 1)
 30: FOR i := 1 TO n - 1
 40:   IF n is even THEN
 50:     swap S[i] and S[n]
 60:   ELSE
 70:     swap S[1] and S[n]
 80:   process S
 90:   IF n > 2 THEN
100:     Heap(S, n - 1)
{% endhighlight %}

Lines 10 and 20 correspond to step 1 of the algorithm.
Lines 30 to 100 correspond to the loop of the algorithm.
Line 80 is a call to a procedure that processes the current permutation to solve a particular problem, for example [brute search all solution candidates of another problem](http://marknelson.us/2002/03/01/next-permutation/).
Line 80 applies `process` to $@n! - 1@$ permutations of input sequence `S` but not to `S`.
To apply procedure `process` to all permutations of `S`, apply `process` to `S` before or after calling `Heap`.

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
n, (n-3), (n-4), (n-5), ..., 2, (n-2), (n-1), 1
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
n, (n-1), 2, 3, 4, ..., (n-2), 1
{% endhighlight %}

This sequence also consists of all elements.
For example, consider the execution of Heap's algorithm for input `123456`.

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

The sequence of elements for the last position is `6, 5, 2, 3, 4, 1`.

In each case the sequence of elements for the last position is determined by two aspects of the algorithm.
One is the rule applied to each swap over the last position and the other is the last permutation given by each corresponding recursive application of the algorithm.
For example, consider again the execution of Heap's algorithm for input `12345`.

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

In this case the rule indicates that we always swap the first and last position, so the progres in the sequence of elements in the last position `5, 2, 3, 4, 1` is determined by the corresponding recursive application of the algorithm.
Swaps 24, 48, 72, and 96 are the last swaps of the recursive applications.
They place elements `2, 3, 4, 1` in the first position thus determining the sequence for the last position.
The sequence for the first position of 24, 48, 72, and 96 is given by the following rule.
For sequence `12...n` of even length, the last permutation is the following.

{% highlight asciidoc %}
(n-2) (n-1) 2 3 ... (n-3) n 1
{% endhighlight %}

For the case where the input sequence is even consider again the execution for input `123456`.

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

In this case, the sequence of elements in the last position is determined by the swap rule applied in permutations 121, 241, 361, 481, and 601.
The reason is that the last permutation given by each recusive call 120, 240, 360, 480, and 600 preserves the original position of all elements except the first and the last.
The rule is, for sequence `12...n` of odd length, the last permutation is the following.

{% highlight asciidoc %}
n 2 3 ... (n-1) 1
{% endhighlight %}
<br>

The last permutation given by each recursive call and the swap rule over the last element satisfy together the following invariants for input sequence `12...n`.

- When `n` is even, the last permutation is `(n-2) (n-1) 2 3 ... (n-3) n 1`.
- When `n` is odd, the last permutation is `n 2 3 ... (n-1) 1`.
- After every swap involving position `n`, a different element is placed in position `n`.

For the first invariant consider the sequence of even length `123...n` and observe that the last permutation is `(n-2) (n-1) 2 3 ... (n-3) n 1`.
Also observe that after every swap involving position `n`, a different element is placed in that position because the sequence of elements for the position is `n, (n-1), 2, 3, 4, ..., (n-2), 1`.

{% highlight asciidoc %}
Swap:              Permutation

  0 :  1     2     3     4    ...  (n-2) (n-1)   n
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
     (n-1)   2     3     4    ...  (n-2)   1     n
  1 :  n     2     3     4    ...  (n-2)   1   (n-1)
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
       1     2     3     4   ...   (n-2)   n   (n-1)
  2 :  1   (n-1)   3     4   ...   (n-2)   n     2
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
       n   (n-1)   3     4   ...   (n-2)   1     2
  3 :  n   (n-1)   2     4   ...   (n-2)   1     3
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
       1   (n-1)   2     4   ...   (n-2)   n     3
  4 :  1   (n-1)   2     3   ...   (n-2)   n     4

   ...ELIDED SWAPS...

       1   (n-1)   2     4   ...   (n-2)   n   (n-3)
 n-2:  1   (n-1)   2     4   ...   (n-3)   n   (n-2)
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
       n   (n-1)   2     4   ...   (n-3)   1   (n-2)
 n-1:  n   (n-1)   2     4   ...   (n-3) (n-2)   1
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
     (n-2) (n-1)   2     4   ...   (n-3)   n     1
{% endhighlight %}

The diagram shows the first four swaps over the last position and also the last two.
We know that the swap marked `n-2` has `n` in the first position and `1` in the second to last position because `n-2` is an even swap and all even swaps have that property.
We know that the swap marked `n-2` swaps `n-2` and `n-3` because each even swap corresponding to swap count `i` takes a current permutation `A` and produces permutation `B` by swapping the `i`-th element with the last as follows.

{% highlight asciidoc %}
A:  n   (n-1)   3     4    ...  (i-1) (i+1)  ...  (n-2)   1     i
B:  n   (n-1)   3     4    ...  (i-1)   i    ...  (n-2)   1   (i+1)
{% endhighlight %}

For the second invariant consider the sequence of odd length `12...n` and observe that the last permutation is `n 2 3 ... (n-1) 1`.
Also observe that after every swap involving psoition `n`, a different element is placed in that position because the sequence of elements for the position is `n, (n-3), (n-4), (n-5), ..., 2, (n-2), (n-1), 1`.

{% highlight asciidoc %}
Swap:                  Permutation

  0 :   1     2     3    ...  (n-4) (n-3) (n-2) (n-1)   n
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-3) (n-2)   2     3    ...  (n-4) (n-1)   1     n
  1 :    n   (n-2)   2     3    ...  (n-4) (n-1)   1   (n-3)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-4) (n-1) (n-2)   2    ...  (n-5)   1     n   (n-3)
  2 :  (n-3) (n-1) (n-2)   2    ...  (n-5)   1     n   (n-4)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-5)   1   (n-1) (n-2)  ...  (n-6)   n   (n-3) (n-4)
  3 :  (n-4)   1   (n-1) (n-2)  ...  (n-6)   n   (n-3) (n-5)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-6)   n     1   (n-1)  ...  (n-7) (n-3) (n-4) (n-5)
  4 :  (n-5)   n     1   (n-1)  ...  (n-7) (n-3) (n-4) (n-6)

       ...ELIDED SWAPS...

 n-4:    3     6     7    ...  (n-1) (n-2)   5    4      2
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-2)   5     6     7    ...  (n-1)   4     3     2
 n-3:    2     5     6     7    ...  (n-1)   4     3   (n-2)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-1)  4      5     6    ...    1     3     2   (n-2)
 n-2:  (n-2)  4      5     6    ...    1     3     2   (n-1)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
         1    3      4      5   ...    n     2   (n-2) (n-1)
 n-1:  (n-1)  3      4      5   ...    n     2   (n-2)   1
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
         n    2      3      4   ...  (n-3) (n-2) (n-1)   1
{% endhighlight %}

We know that swap marked as `n-4` is as indicated because the sequence of elements for each position and each swap is the following.

{% highlight asciidoc %}
        1  |  2  |  3  |  4  | ... | n-3 | n-2 | n-1 |  n
     |------------------------------------------------------
  0  |  1  |  2  |  3  |  4  |     | n-3 | n-2 | n-1 |  n
  1  |  n  | n-2 |  2  |  3  |     | n-4 | n-1 |  1  | n-3
  2  | n-3 | n-1 | n-2 |  2  |     | n-5 |  1  |  n  | n-4
  3  | n-4 |  1  | n-1 | n-2 |     | n-6 |  n  | n-3 | n-5
 ... | ... | ... | ... | ... |     | ... | ... | ... | ...
 n-5 |  4  |  7  |  8  |  9  |     |  2  |  6  |  5  |  3
 n-4 |  3  |  6  |  7  |  8  |     | n-2 |  5  |  4  |  2
 n-3 |  2  |  5  |  6  |  7  |     | n-1 |  4  |  3  | n-2
 n-2 | n-2 |  4  |  5  |  6  |     |  1  |  3  |  2  | n-1
 n-1 | n-1 |  3  |  4  |  5  |     |  n  |  2  | n-2 |  1
{% endhighlight %}


# Summary

Heap's algorithm is efficient and has a simple formulation.
It may not be evident why it constructs all permutations of a given sequence.
We explained by example the reason Heap's algorithm works, which is that it adjoins each element to the permutations of the rest of the elements.

Do you have any questions, comments, suggestions? Let me know in the comments.



{% include subscribe.html %}
{% include share.html %}



# References


<dl>
  <dt id="heap">
    Heap
  </dt>
  <dd>
    <a href="http://comjnl.oxfordjournals.org/content/6/3/293.full.pdf">Permutations by interchanges</a>.
    B. R. Heap, The Computer Journal, 6(3) (1963), pp. 293-298
  </dd>
  <dt id="martin">
    Martin
  </dt>
  <dd>
    Martin, Eric. "Notes on Cryptarithm Solver and Permutations." Principles of Programming. University of South Wales, n.d. Web. 11 June 2016. <a href="https://webcms3.cse.unsw.edu.au/COMP9021/15s2/resources/1544">https://webcms3.cse.unsw.edu.au/COMP9021/15s2/resources/1544</a>.
  </dd>
  <dt id="sedgewick">
    Sedgewick
  </dt>
  <dd>
    <a href="http://homepage.math.uiowa.edu/~goodman/22m150.dir/2007/Permutation%20Generation%20Methods.pdf">Permutation Generation Methods</a>.
    R. Sedgewick, Computing Surveys, 9(2) (1977), pp. 137-164
  </dd>
</dl>


{% include comments.html %}
