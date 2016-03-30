---
layout: post
title: Ecological Bin Packing
date: 2016-03-21
author: Ruslán Ledesma-Garza 
summary: 'Group bottles with the least movements possible.'
---

Ecological Bin Packing is [problem 102 in the UVa Online
Judge](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=38).
Even though I include the problem description in this post, I
encourage you to visit the [UVa Online
Judge](https://uva.onlinejudge.org/index.php) because there you will
be able to submit your solution to get it judged.

# Problem

Recycling glass requires that the glass be separated by color into one
of three categories: brown glass, green glass, and clear glass. In
this problem you will be given three recycling bins, each containing a
specified number of brown, green and clear bottles. In order to be
recycled, the bottles will need to be moved so that each bin contains
bottles of only one color.

The problem is to minimize the number of bottles that are moved. You
may assume that the only problem is to minimize the number of
movements between boxes.

For the purposes of this problem, each bin has infinite capacity and
the only constraint is moving the bottles so that each bin contains
bottles of a single color. The total number of bottles will never
exceed 2^31.

# Input

The input consists of a series of lines with each line containing 9
integers. The first three integers on a line represent the number of
brown, green, and clear bottles (respectively) in bin number 1, the
second three represent the number of brown, green and clear bottles
(respectively) in bin number 2, and the last three integers represent
the number of brown, green, and clear bottles (respectively) in bin
number 3. For example, the line `10 15 20 30 12 8 15 8 31`
indicates that there are 20 clear bottles in bin 1, 12 green bottles
in bin 2, and 15 brown bottles in bin 3.

Integers on a line will be separated by one or more spaces. Your
program should process all lines in the input file.

# Output

For each line of input there will be one line of output indicating
what color bottles go in what bin to minimize the number of bottle
movements. You should also print the minimum number of bottle
movements.

The output should consist of a string of the three upper case
characters 'G', 'B', 'C' (representing the colors green, brown, and
clear) representing the color associated with each bin.

The first character of the string represents the color associated with
the first bin, the second character of the string represents the color
associated with the second bin, and the third character represents the
color associated with the third bin.

The integer indicating the minimum number of bottle movements should
follow the string.

If more than one order of brown, green, and clear bins yields the
minimum number of movements then the alphabetically first string
representing a minimal configuration should be printed.

# Sample Input

{% highlight asciidoc %}
1 2 3 4 5 6 7 8 9
5 10 5 20 10 5 10 20 10
{% endhighlight %}

# Sample Output

{% highlight asciidoc %}
BCG 30
CBG 50
{% endhighlight %}

# Analysis

For a given input, the answer is the first configuration in
alphabetical order that corresponds to the minimum cost.
Thus, for the given input we compute the cost of candidate
configurations in alphabetical order and answer with the first that
corresponds to the minimum cost.

Consider the following input.

{% highlight asciidoc %}
1  2  3  4  5  6  7  8  9
{% endhighlight %}

The input corresponds to the following initial configuration of
bottles.

{% highlight asciidoc %}
bin   |    0     |     1     |     2
------|---------------------------------
color | B  G  C  |  B  G  C  |  B  G  C
------|---------------------------------
count | 1  2  3  |  4  5  6  |  7  8  9
{% endhighlight %}

The following six configurations are all the candidate configurations
for any given input.  We list the six configurations in alphabetical
order.

{% highlight asciidoc %}
BCG
BGC
CBG
CGB
GBC
GCB
{% endhighlight %}

For the first configuration, consider the following computation of the
corresponding cost.  The associated cost is the cost of moving the brown
bottles to bin 0, the clear bottles to bin 1, and the green bottles to
bin 2.

{% highlight c %}
/* BCG */
cost  = count[1][B] + count[2][B];
cost += count[0][C] + count[2][C];
cost += count[0][G] + count[1][G];
{% endhighlight %}

The cost of all configurations for the given input is the following.

{% highlight asciidoc %}
BCG 30
BGC 30
CBG 30
CGB 30
GBC 30
GCB 30
{% endhighlight %}

The minimum cost is 30 and the first configuration with
that cost is `BCG`, thus we answer `BCG 30`.

# Solution

We implement the solution in the following C program.
For each line of input, we store the initial configuration in array
`b`.  Macro `Moves(x, y, z)` computes the cost for given configuration `x,
y, z`.  Variables `min` and `min_i` are respectively the minimum number of bottle
movements and the index of the corresponding configuration.  Macro
`SelectMin(i)` selects the first solution that has the minimum cost by
updating `min` and `min_i`.  The sequence of appications
of `Moves` and `Select` computes the answer.  The `printf`
statement prints the configuration label and the cost of the answer.

{% highlight c %}
#include <stdio.h>

#define MAX_COLOR 3

#define B 0
#define G 1
#define C 2
#define Moves(x,y,z) curr = b[1][x] + b[2][x] + b[0][y] + b[2][y] + b[0][z] + b[1][z]
#define SelectMin(i) if(curr < min) { min_i = i; min = curr; }

#define Si(i) scanf("%d", &i)

int main() {
  int bin, color, n, curr, min, min_i;
  int b[MAX_COLOR][MAX_COLOR];
  char config_labels[][4] = {
    "BCG",
    "BGC",
    "CBG",
    "CGB",
    "GBC",
    "GCB"
  };

  while(Si(n) != EOF) {
    b[0][0] = n;
    for(color = 1; color < MAX_COLOR; color++) {
      Si(n);
      b[0][color] = n;
    }
    for(bin = 1; bin < MAX_COLOR; bin++)
      for(color = 0; color < MAX_COLOR; color++) {
	Si(n);
	b[bin][color] = n;
      }

    Moves(B, C, G);
    min = curr;
    min_i = 0;
    Moves(B, G, C);
    SelectMin(1);
    Moves(C, B, G);
    SelectMin(2);
    Moves(C, G, B);
    SelectMin(3);
    Moves(G, B, C);
    SelectMin(4);
    Moves(G, C, B);
    SelectMin(5);

    printf("%s %d\n", config_labels[min_i], min);
  }

  return 0;
}
{% endhighlight %}

# Summary

Ecological Bin Packing is a problem that asks to select the best
solution out of six candidate solutions.  Given that the candidate
solutions are few, we consider all candidate solutions and select the
best.

# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    /*
    var disqus_config = function () {
        this.page.url = 'http://mrrusof.github.io/2016/03/21/ecological-bin-packing.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = 2016-03-21-ecological-bin-packing; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
