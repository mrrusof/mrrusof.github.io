---
layout: post
title: The Trading Problem
date: 2016-10-04
author: Ruslan Ledesma-Garza
summary: Something something
---

Imagine that you are a developer for a company that sells a stock analysis tool.
One day, you are asked to develop a feature that tells the user the biggest gain for a given stock over a period of time in the past.
For example, consider the price of stock FAKE over the last 7 days.

TODO: graph of stock price

The biggest gain is 5 USD, so that's what the feature will tell the user.
There are two possible ways to obtain the biggest gain.
One way is buy on day 1 and sell on day 6.
The other is buy on day 3 and sell on day 6.

In case you think that the biggest gain is 6 USD, think again.
Buying on day 6 and selling on day 7 does not give you a gain, it gives you a loss, the biggest loss.

The most difficult part of the feature is the algorithm that figures out the biggest gain.
The rest of the changes consist of run-of-the-mill scaffolding and unit tests.

You determine that the input to the algorithm is a sequence of prices over a period of time.
For example, the input that corresponds to the price of stock FAKE over the last 7 days is the following.

{% highlight asciidoc %}
  A: 1 2 1 5 2 6 0
day: 1 2 3 4 5 6 7
len: 7
{% endhighlight %}

You also determine that given the array of prices, the biggest gain is $@max \\{ A[j] - A[i] \\,\|\\, 1 \\leq i < j \\leq 7 \\}@$.
So you translate the mathematical statement for the biggest gain into the following algorithm.

{% highlight asciidoc %}
S(A, len)
 1: max := 0
 2: FOR i := 1 TO len
 3:   FOR j := i + 1 TO len
 4:     IF A[j] - A[i] > max
 5:       max := A[j] - A[i]
 6: RETURN max
{% endhighlight %}

Algorithm $@S@$ works because line 4 considers each element of $@\\{ A[j] - A[i] \\,\|\\, 1 \\leq i < j \\leq 7 \\}@$ and selects the maximum so far.

Boom! Solved, right?

Wrong.

Algorithm $@S@$ is not a solution because it is too slow.
The running time of algorithm $@S@$ is cuadratic, i.e. [it behaves like function $@len ^ 2@$](#complexity-S).
Already with 100,000 data points, the algorithm takes more than 1 second to give you the corresponding answer.
Input usually consists of hundreds of thousands of prices and end users won't wait more than half a second for an answer.

# Time complexity for algorithm $@S@$

Line 4 considers 21 elements ($@21 = 6 + 5 + 4 + 3 + 2 + 1@$).
For given length $@len@$, line 4 considers $@1 + 2 + ... + (len - 2) + (len - 1) = \\Sigma_{i = 1}^{len - 1} i@$.
Thus, the running time $@T(n)@$ is $@\\Sigma_{i = 1}^{n - 1} i@$.
$$
T(n) \\& = 1 + 2 + ... + (n - 2) + (n - 1) \\\\
     \\& = 1 + 2
$$


How do you approach the problem now?

# Solution

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
