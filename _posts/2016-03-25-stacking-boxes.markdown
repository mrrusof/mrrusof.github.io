---
layout: post
title: Stacking Boxes
date: 2016-03-25
author: Ruslan Ledesma-Garza 
summary: 'Finding a way to fit a given set of boxes one inside the
other is easy even for more than 3 dimensions.  Finding a way to fit
those boxes by contorting them, not so.'
---

Stacking Boxes is [problem 103 in the UVa Online
Judge](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=39).
Even though I include the problem description in this post, I
encourage you to visit the [UVa Online
Judge](https://uva.onlinejudge.org/index.php) because there you will
be able to submit your solution to get it judged.

I substituted the verb nest with verb fits in the problem statement.

# Problem

Consider an n-dimensional 
"box" given by its dimensions. In two dimensions the box `(2,3)` might
represent a box with length 2 units and width 3 units. In three
dimensions the box `(4,8,9)` can represent a box `4 x 8 x 9`
(length, width, and height). In 6 dimensions it is, perhaps, unclear
what the box `(4,5,6,7,8,9)` represents; but we can analyze properties
of the box such as the sum of its dimensions.

In this problem you will analyze a property of a group of
n-dimensional boxes. You are to determine the longest stack
of boxes, that is a sequence of boxes  `b_1, b_2, ..., b_k` such
that each box `b_i` fits in box `b_i+1 (1 <= i < k)`. 

A box `D = (d_1, d_2, ..., d_n)` fits in a box `E = (e_1, e_2, ...,
e_n)` if there is some rearrangement of the 
`d_i` such that when rearranged each dimension is
less than the corresponding dimension in box `E`. This loosely
corresponds to turning box `D` to see if it will fit in box `E`. However,
since any rearrangement suffices, box `D` can be contorted, not just
turned (see examples below).

For example, the box `D = (2,6)` fits in the box `E = (7,3)` since
`D` can be rearranged as `(6,2)` so that each dimension is less than the
corresponding dimension in `E`. The box `D = (9,5,7,3)` does NOT fit in
the box `E = (2,10,6,8)` since no rearrangement of `D` results in a box
that satisfies the fitting property, but `F = (9,5,7,1)` does fit in
box `E` since `F` can be rearranged as `(1,9,5,7)` which fits in `E`.

Formally, we define that a box fits in another as follows: box `D = (d_1, d_2, ..., d_n)`
fits in box `E = (e_1, e_2, ..., e_n)`
if there is a permutation `p` of `1 ... n` such that `(d_p[1], d_p[2],
..., d_p[n])` "fits"
in `(e_1, e_2, ..., e_n)` i.e., if  `d_p[i] < e_i` for all
`1 <= i < n`.

# Input

The input consists of a series of box sequences. Each box sequence
begins with a line consisting of the the number of boxes `k` in the
sequence followed by the dimensionality of the boxes, `n` (on the same
line.)

This line is followed by `k` lines, one line per box with the `n`
measurements of each box on one line separated by one or more
spaces. The i-th line in the sequence `1 <= i <= k` gives the
measurements for the i-th box.

There may be several box sequences in the input file. Your program
should process all of them and determine, for each sequence, which of
the `k` boxes determine the longest stack and the length of
that stack (the number of boxes in the stack).

In this problem the maximum dimensionality is 10 and the minimum
dimensionality is 1. The maximum number of boxes in a sequence is 30.

# Output

For each box sequence in the input file, output the length of the
longest stack on one line followed on the next line by a list
of the boxes that comprise this stack in order. The "smallest" or
"innermost" box of the stack should be listed first, the
next box (if there is one) should be listed second, etc.

The boxes should be numbered according to the order in which they
appeared in the input file (first box is box 1, etc.).

If there is more than one longest stack then any one of them
can be output.

# Sample Input

{% highlight asciidoc %}
5 2
3 7
8 10
5 2
9 11
21 18
8 6
5 2 20 1 30 10
23 15 7 9 11 3
40 50 34 24 14 4
9 10 11 12 13 14
31 4 18 8 27 17
44 32 13 19 41 19
1 2 3 4 5 6
80 37 47 18 21 9
{% endhighlight %}

# Sample Output

{% highlight asciidoc %}
5
3 1 2 4 5
4
7 2 5 6
{% endhighlight %}

# Analysis

We approach the problem as a search problem.
We search for an answer in a set of candidates.

An answer is any longest stack.
Consider the following sequence of boxes.
{% highlight asciidoc %}
(1, 5) (2, 4) (3, 5) (3, 6)
{% endhighlight %}
The stacks for the sequence are the following.
{% highlight asciidoc %}
  *             *             *             *             *             *
  |             |             |             |             |             |
(1, 5)        (2, 4)        (1, 5)        (2, 4)        (3, 5)        (3, 6)
                              |             / \
                              |           /     \
                            (3, 6)    (3, 5)  (3, 6)
{% endhighlight %}
Stacks `(1, 5) (3, 6)`, `(2, 4) (3, 5)`, and `(2, 4) (3, 6)` are the longest stacks.
Each of them is an answer for the given sequence of boxes.

The set of candidates consists of one stack for each given box.
Each candidate is a longest stack that ends with the corresponding box.
For the given sequence, the candidates are the following.
{% highlight asciidoc %}
  *             *             *             *
  |             |             |             |
(1, 5)        (2, 4)        (1, 5)        (2, 4)
                              |             /
                              |           /
                            (3, 6)    (3, 5)
{% endhighlight %}

We choose candidates in the following way.
For each given box, we consider the set of stacks that end with the box and choose the first longest stack we find.
For example, for box `(3, 6)` we consider the following stacks.
{% highlight asciidoc %}
                              *                 *                       *
                              |                 |                       |
                            (1, 5)            (2, 4)                  (3, 6)
                              |                   \
                              |                     \
                            (3, 6)                (3, 6)
{% endhighlight %}
Stacks `(1, 5) (3, 6)` and `(2, 4) (3, 6)` are longest.
We choose the `(1, 5) (3, 6)` as the candidate for `(3, 6)`.

The set of candidates contains at least one answer.
The reason is that each answer is also a longest stack that ends with some box.
For example, answers `(1, 5) (3, 6)` and `(2, 4) (3, 6)` are the longest stacks that end with `(3, 6)`.
By picking any of them as the candidate for box `(3, 6)`, we include an answer in the set of candidates.

We answer with the first longest candidate we find.

# Solution

We solve the problem in three steps.
We preprocess the input sequence, construct the set of candidates, and search for a longest candidate.

Preprocessing consists in arranging the sequence of boxes so that for each box, the boxes that fit are located to the left of the box.
We preprocess by sorting the dimensions of each box and then sorting the boxes lexicographically.
Consider the following sequence of boxes.
{% highlight asciidoc %}
(5, 3) (1, 5) (3, 6) (4, 2)
{% endhighlight %}
After sorting the dimensions, the sequence is the following.
{% highlight asciidoc %}
(3, 5) (1, 5) (3, 6) (2, 4)
{% endhighlight %}
The preprocessed sequence is the following.
{% highlight asciidoc %}
(1, 5) (2, 4) (3, 5) (3, 6)
{% endhighlight %}
The preprocessed sequence is arranged.
For example, only box `(2, 4)` fits `(3, 5)` and box `(2, 4)` is to the left of `(3, 5)`.
Even though box `(1, 5)` does not fit `(3, 5)`, we do not care that box `(1, 5)` is to the left of `(3, 5)`.

We sort the dimensions of boxes because one sorted box fits into another sorted box when corresponding dimensions fit.
For example, box `(2, 4)` fits `(3, 6)` because `2 < 3` and `4 < 6`.
Another example, box `(3, 6)` does not fit `(2, 4)` because 3 does not fit 2.
Given that the dimensions are sorted, we already know that the other dimension of the first box does not fit 2.
In general, when we consider boxes `a := (a1, a2, ..., an)` and `b := (b1, b2, ..., bn)`, `a` fits `b` if and only if for each `1 <= i <= n`, `ai < bi`.
There are two cases, either the condition is satisfied or not.
In the case where the condition is satisfied, we satisfy the definition of fits.
The following diagram illustrates the other case, the case where for some `i`, `ai` is not less than `bi`.
{% highlight asciidoc %}
a1 <= a2 <= ... <= ai-1 <= ai <= ai+1 <= ... <= an

                           v
                           ‖

b1 <= b2 <= ... <= bi-1 <= bi <= bi+1 <= ... <= bn
{% endhighlight %}
In this case there is no way another dimension of `a` fits `bi` while the rest of the dimensions of `a` fit in other dimensions of `b`.
Consider that the only dimensions `a1, ..., ai-1` may fit `bi`.
Suppose that `aj` fits `bi` as follows.
{% highlight asciidoc %}
a1 <= a2 <= ... <= aj-1 <= aj <= aj+1 <= ... <= ai-1 <= ai <= ai+1 <= ... <= an

                            ʌ                           v
                            |__________________________ ‖
                                                       \
b1 <= b2 <= ... <= bj-1 <= bj <= bj+1 <= ... <= bi-1 <= bi <= bi+1 <= ... <= bn
{% endhighlight %}
Consider that `ai, ..., an` may only fit `bi+1, ..., bn`.
It is impossible to fit each dimension `ai, ..., an` into `bi+1, ..., bn` because number of dimensions is not the same.

Sorting the boxes lexicographically after sorting the dimensions arranges the sequence of boxes.
The reason is that for each box, all boxes to the right do not fit the box.
Consider box `(2, 4)` in the following sorted sequence.
{% highlight asciidoc %}
(1, 5) (2, 4) (3, 5) (3, 6)
{% endhighlight %}
Box `(2, 4)` comes before `(3, 5)` because `2 < 3`, which is the same reason why `(3, 5)` does not fit `(2, 4)`.
In general, when we consider boxes `a := (a1, a2, ..., an)` and `b := (b1, b2, ..., bn)`, `b` does not fit `a` if `a` comes before `b`.
If `a` comes before `b`, either all dimensions of `a` and `b` are the same or there is a dimension `ai` that is less or equal to dimension `bi`, as illustrated in the following diagram.
{% highlight asciidoc %}
a1 <= a2 <= ... <= ai-1 <= ai <= ai+1 <= ... <= an

                           ʌ
‖     ‖            ‖       ‖

b1 <= b2 <= ... <= bi-1 <= bi <= bi+1 <= ... <= bn
{% endhighlight %}
In either case, the condition that each dimension of `b` fits the corresponding dimension of `a` is not satisfied and thus `b` does not fit `a`.

We construct the set of candidates by iterating the preprocessed sequence applying the following two rules to each box.
If no candidate to the left of the box fits the box, then the candidate for the box is the box itself.
Consider the following table that indicates the candidates for `(1, 5)` and `(2, 4)`.
{% highlight asciidoc %}
      Box | (1, 5) | (2, 4) | (3, 5) | (3, 6)
----------|--------|--------|--------|--------
          | (1, 5) | (2, 4) |        |
Candidate |        |        |        |
          |        |        |        |
{% endhighlight %}
For box `(2, 4)`, candidate `(1, 5)` does not fit, thus stack `(2, 4)` is the candidate for box `(2, 4)`.
If at least one candidate to the left fits the box, we stack the box on all candidates that fit and we choose the first longest stack we get.
Consider the following table that indicates the candidates for all boxes.
{% highlight asciidoc %}
      Box | (1, 5) | (2, 4) | (3, 5) | (3, 6)
----------|--------|--------|--------|--------
          | (1, 5) | (2, 4) | (2, 4) | (1, 5)
Candidate |        |        | (3, 5) | (3, 6)
          |        |        |        |
{% endhighlight %}
For box `(3, 6)`, candidates `(1, 5)` and `(2, 4)` fit the box.
Each candidate corresponds to a longest stack that ends with `(3, 6)`, stacks `(1, 5), (3, 6)` and `(2, 4) (3, 6)` respectively.
We choose `(1, 5) (3, 6)` as the longest candidate for `(3, 6)` because we consider candidate `(1, 5)` before candidate `(2, 4)`.	

The construction of the set of candidates belongs to complexity class `O(n^2)`.

We return the first longest candidate we find from left to right.
Consider the selected candidate from the set of candidates.
{% highlight asciidoc %}
      Box | (1, 5) | (2, 4) | (3, 5) | (3, 6)
----------|--------|--------|--------|--------
          | (1, 5) | (2, 4) | (2, 4) | (1, 5)
Candidate |        |        | (3, 5) | (3, 6)
          |        |        |        |
                                 ^
                                 |
                        selected candidate
{% endhighlight %}

# Implementation

We apply function `merge_sort_ints(int *a, int n)` to sorting the dimensions of a given box.
Function `merge_sort_ints` sorts array `a` of `n` integers by merge sort.
{% highlight c %}
void merge_sort_ints(int *a, int n) {
  int i, j, k;
  int m = n / 2;
  int tmp[n];
  if(n <= 1)
    return;
  merge_sort_ints(a, m);
  merge_sort_ints(a + m, n - m);
  for(k = i = 0, j = m; k < n; k++)
    if(i < m && j < n)
      if(a[i] <= a[j])
	tmp[k] = a[i++];
      else
	tmp[k] = a[j++];
    else if(i < m)
      tmp[k] = a[i++];
    else
      tmp[k] = a[j++];
  for(i = 0; i < n; i++)
    a[i] = tmp[i];
}
{% endhighlight %}
Consider the following application of `merge_sort_ints` in line `SORT_DIMS`.
That application sorts dimensions for each box after reading all the boxes for a sequence.
{% highlight c %}
typedef struct box {
  int d[MAX_DIM];
  int original_position;
} box;

int main() {
  int n, d, i, j;
  box box[MAX_BOX];
...
  while(Si(n) != EOF) {
    /* Read input. */
    Si(d);
    for(i = 0; i < n; i++) {
      box[i].original_position = i + 1;
      for(j = 0; j < d; j++)
	Si(box[i].d[j]);
    }
...

    /* For each box, sort dimensions. */
    for(i = 0; i < n; i++)
      merge_sort_ints(box[i].d, d); /* ..................... SORT_DIMS */

...
{% endhighlight %}

We apply function `merge_sort_boxes(box *a, int n, int d)` to sorting the sequence of boxes.
Function `merge_sort_boxes` sorts array `a` of `n` boxes that have `d` dimensions by merge sort.
{% highlight c %}
void merge_sort_boxes(box *a, int n, int d) {
  int i, j, k;
  int m = n / 2;
  box tmp[n];
  if(n <= 1)
    return;
  merge_sort_boxes(a, m, d);
  merge_sort_boxes(a + m, n - m, d);
  for(k = i = 0, j = m; k < n; k++)
    if(i < m && j < n)
      if(le_box(a[i], a[j], d))
	copy_box(tmp + k , a + i++, d);
      else
	copy_box(tmp + k, a + j++, d);
    else if(i < m)
      copy_box(tmp + k , a + i++, d);
    else
      copy_box(tmp + k, a + j++, d);
  for(i = 0; i < n; i++)
    copy_box(a + i, tmp + i, d);
}
{% endhighlight %}
Consider the following application of `merge_sort_boxes` in line `SORT_BOXES`.
That application sorts the sequence of boxes after sorting dimensions.
{% highlight c %}
typedef struct box {
  int d[MAX_DIM];
  int original_position;
} box;

int main() {
  int n, d, i, j;
  box box[MAX_BOX];
...
  while(Si(n) != EOF) {
    /* Read input. */
    Si(d);
    for(i = 0; i < n; i++) {
      box[i].original_position = i + 1;
      for(j = 0; j < d; j++)
	Si(box[i].d[j]);
    }
...

    /* For each box, sort dimensions. */
    for(i = 0; i < n; i++)
      merge_sort_ints(box[i].d, d); /* ..................... SORT_DIMS */

    /* Sort boxes lexicographically. */
    merge_sort_boxes(box, n, d); /* ........................ SORT_BOXES */

...
{% endhighlight %}

We compute the candidate set with the following block of code in `main` after sorting the sequence.
{% highlight c %}
#define NO_PARENT -1

typedef struct box {
  int d[MAX_DIM];
  int original_position;
} box;

...

int box_fits(box *this, box *into_this, int d) {
  int i;
  for(i = 0; i < d; i++)
    if(this->d[i] >= into_this->d[i])
      return 0;
  return 1;
}

...

int main() {
  int n, d, i, j;
  box box[MAX_BOX];
  int longest_here_parent[MAX_BOX];
  int longest_here_length[MAX_BOX];
...
  while(Si(n) != EOF) {

...

    /* For each position, compute one longest increasing subsequence
       that ends in the position. */
    for(i = 0; i < n; i++) {
      longest_here_parent[i] = NO_PARENT;
      longest_here_length[i] = 1;
    }
    for(i = 0; i < n; i++)
      for(j = 0; j < i; j++)
	if(box_fits(box + j, box + i, d) &&
	   longest_here_length[i] < longest_here_length[j] + 1) {
	  longest_here_parent[i] = j;
	  longest_here_length[i] = longest_here_length[j] + 1;
	}

...
{% endhighlight %}

We search for a longest candidate and we keep the first we find in the following code.
{% highlight c %}
int main() {
  int n, d, i, j;
  box box[MAX_BOX];
  int longest_here_parent[MAX_BOX];
  int longest_here_length[MAX_BOX];
  int longest_last;
  int longest_length;
  int longest[MAX_BOX];
  while(Si(n) != EOF) {

...

    /* Compute the longest increasing subsequence. */
    longest_last = 0;
    longest_length = 1;
    for(i = 1; i < n; i++) 
      if(longest_length < longest_here_length[i]) {
	longest_last = i;
	longest_length = longest_here_length[i];
      }
    i = longest_last;
    j = longest_length - 1;
    for( ; longest_here_length[i] != 1; j--) {
      longest[j] = i;
      i = longest_here_parent[i];
    }
    longest[j] = i;
{% endhighlight %}

The full code of our implementation is the following.

{% highlight c %}
#include <stdio.h>

#define MAX_DIM 10
#define MAX_BOX 30
#define NO_PARENT -1

#define Si(i) scanf("%d", &i)

typedef struct box {
  int d[MAX_DIM];
  int original_position;
} box;

void merge_sort_ints(int *a, int n) {
  int i, j, k;
  int m = n / 2;
  int tmp[n];
  if(n <= 1)
    return;
  merge_sort_ints(a, m);
  merge_sort_ints(a + m, n - m);
  for(k = i = 0, j = m; k < n; k++)
    if(i < m && j < n)
      if(a[i] <= a[j])
	tmp[k] = a[i++];
      else
	tmp[k] = a[j++];
    else if(i < m)
      tmp[k] = a[i++];
    else
      tmp[k] = a[j++];
  for(i = 0; i < n; i++)
    a[i] = tmp[i];
}

int le_box(box a, box b, int d) {
  int i;
  for(i = 0; i < d; i++) {
    if(a.d[i] < b.d[i])
      return 1;
    else if(a.d[i] > b.d[i])
      return 0;
  }
  return 1;
}

void copy_box(box *to, box *from, int d) {
  int i = 0;
  to->original_position = from->original_position;
  for(i = 0; i < d; i++)
    to->d[i] = from->d[i];
}

void merge_sort_boxes(box *a, int n, int d) {
  int i, j, k;
  int m = n / 2;
  box tmp[n];
  if(n <= 1)
    return;
  merge_sort_boxes(a, m, d);
  merge_sort_boxes(a + m, n - m, d);
  for(k = i = 0, j = m; k < n; k++)
    if(i < m && j < n)
      if(le_box(a[i], a[j], d))
	copy_box(tmp + k , a + i++, d);
      else
	copy_box(tmp + k, a + j++, d);
    else if(i < m)
      copy_box(tmp + k , a + i++, d);
    else
      copy_box(tmp + k, a + j++, d);
  for(i = 0; i < n; i++)
    copy_box(a + i, tmp + i, d);
}

int box_fits(box *this, box *into_this, int d) {
  int i;
  for(i = 0; i < d; i++)
    if(this->d[i] >= into_this->d[i])
      return 0;
  return 1;
}

int main() {
  int n, d, i, j;
  box box[MAX_BOX];
  int longest_here_parent[MAX_BOX];
  int longest_here_length[MAX_BOX];
  int longest_last;
  int longest_length;
  int longest[MAX_BOX];
  while(Si(n) != EOF) {

    /* Read input. */
    Si(d);
    for(i = 0; i < n; i++) {
      box[i].original_position = i + 1;
      for(j = 0; j < d; j++)
	Si(box[i].d[j]);
    }

    /* For each box, sort dimensions. */
    for(i = 0; i < n; i++)
      merge_sort_ints(box[i].d, d); /* ..................... SORT_DIMS */

    /* Sort boxes lexicographically. */
    merge_sort_boxes(box, n, d); /* ........................ SORT_BOXES */

    /* For each position, compute one longest increasing subsequence
       that ends in the position. */
    for(i = 0; i < n; i++) {
      longest_here_parent[i] = NO_PARENT;
      longest_here_length[i] = 1;
    }
    for(i = 0; i < n; i++)
      for(j = 0; j < i; j++)
	if(box_fits(box + j, box + i, d) &&
	   longest_here_length[i] < longest_here_length[j] + 1) {
	  longest_here_parent[i] = j;
	  longest_here_length[i] = longest_here_length[j] + 1;
	}

    /* Compute the longest increasing subsequence. */
    longest_last = 0;
    longest_length = 1;
    for(i = 1; i < n; i++) 
      if(longest_length < longest_here_length[i]) {
	longest_last = i;
	longest_length = longest_here_length[i];
      }
    i = longest_last;
    j = longest_length - 1;
    for( ; longest_here_length[i] != 1; j--) {
      longest[j] = i;
      i = longest_here_parent[i];
    }
    longest[j] = i;

    /* Output longest increasing subsequence. */
    printf("%d\n", longest_length);
    printf("%d", box[longest[0]].original_position);
    for(i = 1; i < longest_length; i++)
      printf(" %d", box[longest[i]].original_position);
    printf("\n");
  }
  return 0;
}
{% endhighlight %}

Our implementation was accepted by the [UVa Online
Judge](https://uva.onlinejudge.org/index.php).

# Summary

We approach Stacking Boxes as a search problem.
We restrict the search space to a set of candidates.
The construction of the set of candidates belongs to complexity class `O(n^2)` and is the most expensive part.

# A faster solution

I have explored the possibility of solving the problem in at most `O(n log n)` steps.

A quick look at [Algorithmist](http://www.algorithmist.com/index.php/UVa_103) shows that Stacking Boxes is a particular case of the [Longest Increasing Subsequence Problem](http://www.algorithmist.com/index.php/Longest_Increasing_Subsequence).
There I found [an `O(n log n)` algorithm that computes the longest increasing subsequence of an array of integers](http://www.algorithmist.com/index.php/Longest_Increasing_Subsequence.cpp), which I tried to apply to the problem.
The algorithm looked promising because it refines a single solution as it processes the sequence of integers one by one.
The problem is that, unlike integers, boxes are not always comparable by means of the relation 'fits'.
For example, neither `(1, 5)` fits `(3, 5)` nor vice versa.
In mathematicalese that means that the relation 'fits' is not a [total relation](https://en.wikipedia.org/wiki/Total_relation).

Given that not all boxes are comparable, instead of refining only one solution, I could only think of an adaptation of the algorithm that refines a set of candidates.
The refinement consists in searching for each box a stack where you can place it.
The procedure for searching a stack for a box is similar to [patiente sorting](https://en.wikipedia.org/wiki/Patience_sorting).
The difference is that finding the stack for each box cannot be done by binary search applying relation 'fits'.
Therefore, in the worst case scenario that all boxes in the input sequence are incomparable, my adaptation will takes `O(n^2)` steps.

Do you have any idea how to solve Stacking Boxes faster? Let me know in the comments.

# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    /*
    var disqus_config = function () {
        this.page.url = 'http://mrrusof.github.io/2016/03/25/stacking-boxes.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = 2016-03-25-stacking-boxes; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');

        s.src = '//mrrusof.disqus.com/embed.js';

        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a
        href="https://disqus.com/?ref_noscript"
        rel="nofollow">comments powered by Disqus.</a></noscript>
