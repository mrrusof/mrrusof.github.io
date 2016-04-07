---
layout: post
title: Stacking Boxes
date: 2016-03-25
author: Ruslán Ledesma-Garza 
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

# Problem

Consider an n-dimensional 
"box" given by its dimensions. In two dimensions the box `(2,3)` might
represent a box with length 2 units and width 3 units. In three
dimensions the box `(4,8,9)` can represent a box `4 x 8 x 9`
(length, width, and height). In 6 dimensions it is, perhaps, unclear
what the box `(4,5,6,7,8,9)` represents; but we can analyze properties
of the box such as the sum of its dimensions.

In this problem you will analyze a property of a group of
n-dimensional boxes. You are to determine the longest nesting string
of boxes, that is a sequence of boxes  `b_1, b_2, ..., b_k` such
that each box `b_i` nests in box `b_i+1 (1 <= i < k)`. 

A box `D = (d_1, d_2, ..., d_n)` nests in a box `E = (e_1, e_2, ...,
e_n)` if there is some rearrangement of the 
`d_i` such that when rearranged each dimension is
less than the corresponding dimension in box `E`. This loosely
corresponds to turning box `D` to see if it will fit in box `E`. However,
since any rearrangement suffices, box `D` can be contorted, not just
turned (see examples below).

For example, the box `D = (2,6)` nests in the box `E = (7,3)` since
`D` can be rearranged as `(6,2)` so that each dimension is less than the
corresponding dimension in `E`. The box `D = (9,5,7,3)` does NOT nest in
the box `E = (2,10,6,8)` since no rearrangement of `D` results in a box
that satisfies the nesting property, but `F = (9,5,7,1)` does nest in
box `E` since `F` can be rearranged as `(1,9,5,7)` which nests in `E`.

Formally, we define nesting as follows: box `D = (d_1, d_2, ..., d_n)`
nests in box `E = (e_1, e_2, ..., e_n)`
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
the `k` boxes determine the longest nesting string and the length of
that nesting string (the number of boxes in the string).

In this problem the maximum dimensionality is 10 and the minimum
dimensionality is 1. The maximum number of boxes in a sequence is 30.

# Output

For each box sequence in the input file, output the length of the
longest nesting string on one line followed on the next line by a list
of the boxes that comprise this string in order. The "smallest" or
"innermost" box of the nesting string should be listed first, the
next box (if there is one) should be listed second, etc.

The boxes should be numbered according to the order in which they
appeared in the input file (first box is box 1, etc.).

If there is more than one longest nesting string then any one of them
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

Nine boxes of two dimensions.
{% highlight asciidoc %}
9 2
5 3
4 4
1 5
6 6
2 4
3 6
5 5
7 7
3 8
{% endhighlight %}

Sequence of boxes.
{% highlight asciidoc %}
(5, 3) (4, 4) (1, 5) (6, 6) (2, 4) (3, 6) (5, 5) (7, 7) (3, 8)
{% endhighlight %}

Boxes rotated.
{% highlight asciidoc %}
(3, 5) (4, 4) (1, 5) (6, 6) (2, 4) (3, 6) (5, 5) (7, 7) (3, 8)
{% endhighlight %}

Sequence sorted lexicographically.
{% highlight asciidoc %}
(1, 5) (2, 4) (3, 5) (3, 6) (3, 8) (4, 4) (5, 5) (6, 6) (7, 7)
{% endhighlight %}

Longest increasing sequences for each position.
{% highlight asciidoc %}
 (1, 5) | (2, 4) | (3, 5) | (3, 6) | (3, 8) | (4, 4) | (5, 5) | (6, 6) | (7, 7)
--------|--------|--------|--------|--------|--------|--------|--------|--------
 (1, 5) | (2, 4) | (2, 4) | (1, 5) | (1, 5) | (4, 4) | (2, 4) | (2, 4) | (2, 4)
        |        | (3, 5) | (3, 6) | (3, 8) |        | (5, 5) | (3, 5) | (3, 5)
        |        |        |        |        |        |        | (6, 6) | (6, 6)
        |        |        |        |        |        |        |        | (7, 7)
    ^                 ^                                            ^        ^
    |                 |                                            |        |
    1                 2                                            3        4
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /
                       /
                     /
                   /
                 /
               /
             /
           /
         /
      (1, 5)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|
                       /  |
                     /    |
                   /      |
                 /        |
               /          |
             /            |
           /              |
         /                |
      (1, 5)            (2, 4)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|
                       /  |
                     /    |
                   /      |
                 /        |
               /          |
             /            |
           /              |
         /                |
      (1, 5)            (2, 4)
                         /
                       /  
                    (3, 5)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|
                       /  |
                     /    |
                   /      |
                 /        |
               /          |
             /            |
           /              |
         /                |
      (1, 5)            (2, 4)
       /                 /
     /                 /  
 (3, 6)             (3, 5)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|
                       /  |
                     /    |
                   /      |
                 /        |
               /          |
             /            |
           /              |
         /                |
      (1, 5)            (2, 4)
       / \               /
     /     \           /  
 (3, 6)   (3, 8)    (3, 5)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|\
                       /  |  \
                     /    |    \
                   /      |      \
                 /        |        \
               /          |          \
             /            |            \
           /              |              \
         /                |                \
      (1, 5)            (2, 4)            (4, 4)
       / \               /
     /     \           /  
 (3, 6)   (3, 8)    (3, 5)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|\
                       /  |  \
                     /    |    \
                   /      |      \
                 /        |        \
               /          |          \
             /            |            \
           /              |              \
         /                |                \
      (1, 5)            (2, 4)            (4, 4)
       / \               / \
     /     \           /     \
 (3, 6)   (3, 8)    (3, 5)  (5, 5)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|\
                       /  |  \
                     /    |    \
                   /      |      \
                 /        |        \
               /          |          \
             /            |            \
           /              |              \
         /                |                \
      (1, 5)            (2, 4)            (4, 4)
       / \               / \
     /     \           /     \
 (3, 6)   (3, 8)    (3, 5)  (5, 5)
                      |
                      |
                    (6, 6)
{% endhighlight %}

{% highlight asciidoc %}
                          *
                         /|\
                       /  |  \
                     /    |    \
                   /      |      \
                 /        |        \
               /          |          \
             /            |            \
           /              |              \
         /                |                \
      (1, 5)            (2, 4)            (4, 4)
       / \               / \
     /     \           /     \
 (3, 6)   (3, 8)    (3, 5)  (5, 5)
                      |
                      |
                    (6, 6)
                      |
                      |
                    (7, 7)
{% endhighlight %}

<<<<<<< HEAD
{% highlight asciidoc %}
0  0

0  1        1  1

0  2        1  2        2  2

0  3        1  3        2  3        3  3

0  4        1  4        2  4        3  4        4  4

0  5        1  5        2  5        3  5        4  5        5  5

0  6        1  6        2  6        3  6        4  6        5  6        6  6

0  7        1  7        2  7        3  7        4  7        5  7        6  7

0  8        1  8        2  8        3  8        4  8        5  8        6  8

0  9        1  9        2  9        3  9        4  9        5  9        6  9

0 10        1 10        2 10        3 10        4 10        5 10        6 10

0 11        1 11        2 11        3 11        4 11        5 11        6 11

0 12        1 12        2 12        3 12        4 12        5 12        6 12

0 13        1 13        2 13        3 13        4 13        5 13        6 13

0 14        1 14        2 14        3 14        4 14        5 14        6 14

0 15        1 15        2 15        3 15        4 15        5 15        6 15

...         ...         ...         ...         ...         ...         ...
{% endhighlight %}

=======
>>>>>>> a81d921ccdfb72d6826961be0a7f0f72a7824643
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
      merge_sort_ints(box[i].d, d);

    /* Sort boxes lexicographically. */
    merge_sort_boxes(box, n, d);

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


# Solution


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
