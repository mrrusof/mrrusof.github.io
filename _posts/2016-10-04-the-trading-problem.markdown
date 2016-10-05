---
layout: post
title: The Trading Problem
date: 2016-10-04
author: Ruslan Ledesma-Garza
summary: Something something
---

You are developing a new stock market analysis tool.

Your product owner asks you to compute the biggest gain that is possible for a given period of time.

TODO: graph of stock price

n: 1 2 1 5 2 6 0
i: 0 1 2 3 4 5 6
-----------------
x: 1
y: 6
a: 5

$@max { a[j] - a[i] | i < j }@$

1. A solution to the problem consists of a pair of elements $@x, y@$.
2. The pair of elements is such that element $@x@$ appears before
   elements $@y@$.
3. The pair of elements is such that there is no other pair $@u, v@$
   that satisfies condition 2 and is such that $@y - x < v - u@$

You come up with the following solution program.

{% highlight asciidoc %}
S(A, n)
 1: max := 0
 2: FOR i := 1 TO n
 3:   FOR j := i + 1 TO n
 4:     IF A[j] - A[i] > max
 5:       max := A[j] - A[i]
 6: RETURN max
{% endhighlight %}

You realize that the solution program is too slow because the actual data consists of hundreds of thousands of data points.

What do you do now?

TODO: solution.

Had you read my blog, you would have saved yourself quite some time because this approach is very similar to the approach in Jill Rides Again.



{% include subscribe.html %}
{% include share.html %}


# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    var disqus_config = function () {
        this.page.url = 'http://ruslanledesma.com/2016/10/04/the-trading-problem.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-10-04-the-trading-problem'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    (function() {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');

        s.src = '//definecode.disqus.com/embed.js';

        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a
        href="https://disqus.com/?ref_noscript"
        rel="nofollow">comments powered by Disqus.</a></noscript>
