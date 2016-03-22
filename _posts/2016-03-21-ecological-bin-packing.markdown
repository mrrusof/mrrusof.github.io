---
layout: post
title: Ecological Bin Packing
date: 2016-03-21
author: Ruslán Ledesma-Garza 
summary: Something something.
---

The Blocks Problem is [problem 102 in the UVa Online
Judge](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=37).
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
