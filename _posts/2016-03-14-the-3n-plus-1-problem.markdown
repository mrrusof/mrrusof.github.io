---
layout: post
title: 'The 3n + 1 Problem'
date: 2016-03-14
author: Ruslán Ledesma-Garza 
summary: 'A classical programming problem. The problem statement will
tell you an answer, just not a fast one.'
---

This is an old one.  One of the first programming
problems I ever solved.  Not very difficult to get it right, not very
difficult to make it fast.

The 3n + 1 Problem is [problem 100 in the UVa Online
Judge](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=36).
Even though I include the problem description in this post, I
encourage you to visit the [UVa Online
Judge](https://uva.onlinejudge.org/index.php) because there you will
be able to submit your solution to get it judged.

# Problem

Consider the following algorithm.


{% highlight asciidoc %}
1. input n
2. print n
3. if n = 1 then STOP
4. if n is odd then n <-- 3n + 1
5. else             n <-- n/2
6. GOTO 2
{% endhighlight %}


Given the input 22, the following sequence of numbers will be printed
`22 11 34 17 52 26 13 40 20 10 5 16 8 4 2 1`.

It is conjectured that the algorithm above will terminate for any
integral input value.  This conjecture is the [Collatz
Conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture).
Despite the simplicity of the algorithm, it is unknown whether this
conjecture is true. It has been verified, however, for all integers
`n` such that `0 < n < 1,000,000` (and, in fact, for many more numbers
than this.)

Given an input `n`, it is possible to determine the number of numbers
printed (including the 1). For a given `n` this is called the
cycle-length of `n`. In the example above, the cycle length of 22 is
16.

For any two numbers `i` and `j` you are to determine the maximum cycle
length over all numbers between `i` and `j`.

# Input

The input will consist of a series of pairs of integers `i` and `j`,
one pair of integers per line. All integers will be less than
`1,000,000` and greater than `0`.

You should process all pairs of integers and for each pair determine
the maximum cycle length over all integers between and including `i`
and `j`.

You can assume that no operation overflows a 32-bit integer.

# Output

For each pair of input integers `i` and `j` you should output `i`,
`j`, and the maximum cycle length for integers between and including
`i` and `j`. These three numbers should be separated by at least one
space with all three numbers on one line and with one line of output
for each line of input. The integers `i` and `j` must appear in the output
in the same order in which they appeared in the input and should be
followed by the maximum cycle length (on the same line).

# Sample Input

{% highlight asciidoc %}
1 10
100 200
201 210
900 1000
{% endhighlight %}

# Sample Output

{% highlight asciidoc %}
1 10 20
100 200 125
201 210 89
900 1000 174
{% endhighlight %}

# Solution

Consider the following solution.  Function `collatz_len` computes the
cycle length of a given integer.  Each recursive call corresponds to an
assignment in the algorithm of the problem statement.  The numbers in
the left margin indicate execution count for [a random
input](https://github.com/mrrusof/algorithms/blob/master/uva/100-the-3n-plus-1-problem/random.in).

{% highlight c %}
/*        -:    1: */ #include <stdio.h>
/*        -:    2: */ 
/*        -:    3: */ #define MAX(x,y) x > y ? x : y
/*        -:    4: */ #define MIN(x,y) x < y ? x : y
/*        -:    5: */ #define Si(n) scanf("%d", &n)
/*        -:    6: */ 
/* 11448555:    7: */ int collatz_len(const int n) {
/*        -:    8: */   int len;
/*        -:    9: */   if(n == 1)
/*    82704:   10: */     return 1;
/*        -:   11: */   if(n & 1)
/*  3779464:   12: */     len = collatz_len(3*n + 1) + 1;
/*        -:   13: */   else
/*  7586387:   14: */     len = collatz_len(n >> 1) + 1;
/* 11365851:   15: */   return len;
/*        -:   16: */ }
/*        -:   17: */ 
/*        1:   18: */ int main() {
/*        -:   19: */   int i, j, ii, jj, max;
/*        -:   20: */   while(Si(i) != EOF) {
/*      100:   21: */     Si(j);
/*      300:   22: */     ii = MIN(i, j);
/*      300:   23: */     jj = MAX(i, j);
/*   242570:   24: */     for(max = 0; ii<=jj; ii++)
/*   242370:   25: */       max = MAX(max, collatz_len(ii));
/*      100:   26: */     printf("%d %d %d\n", i, j, max);
/*        -:   27: */   }
/*        1:   28: */   return 0;
/*        -:   29: */ }
{% endhighlight %}

We reduce the number of steps taken to solve the random input by
memoizing the cycle lengths.  The following solution memoizes calls
for `n` less than 1,000,000.  Memoization reduces the number of calls
one order of magnitude because the calls to `collatz_len` decreased
from ~11,400,000 steps to ~900,000 and we added ~3,000,000 steps for
initialization in line 26.

{% highlight c %}
/*        -:    1: */ #include <stdio.h>
/*        -:    2: */ 
/*        -:    3: */ #define MAX_INPUT 1000000
/*        -:    4: */ #define MAX(x,y) x > y ? x : y
/*        -:    5: */ #define MIN(x,y) x < y ? x : y
/*        -:    6: */ #define Si(n) scanf("%d", &n)
/*        -:    7: */ 
/*        -:    8: */ int lengths[MAX_INPUT];
/*        -:    9: */ 
/*   889627:   10: */ int collatz_len(const int n) {
/*        -:   11: */   int len;
/*        -:   12: */   if(n < MAX_INPUT && lengths[n] > 0)
/*    82704:   13: */     return lengths[n];
/*        -:   14: */   if(n & 1)
/*   285459:   15: */     len = collatz_len(3*n + 1) + 1;
/*        -:   16: */   else
/*   521464:   17: */     len = collatz_len(n >> 1) + 1;
/*        -:   18: */   if(n < MAX_INPUT)
/*   445674:   19: */     lengths[n] = len;
/*   806923:   20: */   return len;
/*        -:   21: */ }
/*        -:   22: */ 
/*        1:   23: */ int main() {
/*        -:   24: */   int i, j, ii, jj, max;
/*        1:   25: */   lengths[1] = 1;
/*  2999996:   26: */   for(i = 2; i<MAX_INPUT; i++) lengths[i] = 0;
/*        -:   27: */   while(Si(i) != EOF) {
/*      100:   28: */     Si(j);
/*      300:   29: */     ii = MIN(i, j);
/*      300:   30: */     jj = MAX(i, j);
/*   242570:   31: */     for(max = 0; ii<=jj; ii++)
/*   242370:   32: */       max = MAX(max, collatz_len(ii));
/*      100:   33: */     printf("%d %d %d\n", i, j, max);
/*        -:   34: */   }
/*        1:   35: */   return 0;
/*        -:   36: */ }
{% endhighlight %}

# Discussion

Our solution ranks 183 in the [UVa Online
Judge](https://uva.onlinejudge.org/index.php).
There are submissions with reported runtimes of 0.000 seconds.
I have yet to figure out a faster solution.

# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    /*
    var disqus_config = function () {
        this.page.url = 'http://mrrusof.github.io/2016/03/14/the-3n-plus-1-problem.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = 2016-03-14-the-3n-plus-1-problem; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
