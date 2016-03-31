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

# Solution

I'll give you a head start.

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
