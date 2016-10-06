---
layout: post
title: The Stock Problem
date: 2016-10-04
author: Ruslan Ledesma-Garza
summary: Something something
---

The Stock Problem is based on a technical interview question I once solved.
Solve this problem and feel satisfied that you are one step closer to mastery of the Wonderful World of Algorithms.
The drama in the problem statement and the solution is fictional, created by me, totally uncalled for, and any resemblance to a real life situation is purely coincidental.

# Problem

You are a developer for a company that sells a program that analyses stock.
One day, your manager asks you to develop a feature that tells the user the biggest gain for a given stock over a period of time in the past.
For example, the following is the price of stock FAKE over the last 7 days.

TODO: graph of stock price

The biggest gain is 5 USD, so that's what the feature will tell the user.
There are two possible ways to obtain the biggest gain.
One way is buy on day 1 and sell on day 6.
The other is buy on day 3 and sell on day 6.
You only have to report the gain.

For a moment you have the slight impression the biggest gain is 6 USD, because the difference in price between day 6 and day 7 is 6 USD.
However, you realize that that makes absolutely no sense because buying on day 6 and selling on day 7 does not give you a gain, it gives you a loss, the biggest loss.

Given the example, you have the feeling that you understand what your manager is asking for.
You figure that the most difficult part of the feature is the algorithm that computes the biggest gain and
that the rest of the changes are just run-of-the-mill scaffolding and unit tests.

So you have a go at the algorithm.

You determine that the input to the algorithm is a sequence of prices over a period of time.
For example, the input that corresponds to the price of stock FAKE over the last 7 days is the following.

{% highlight asciidoc %}
  A: 1 2 1 5 2 6 0
day: 1 2 3 4 5 6 7
len: 7
{% endhighlight %}

You also determine that given the array of prices, the biggest gain is $@max \\{ A[j] - A[i] \\,\|\\, 1 \\leq i < j \\leq 7 \\}@$.
So you translate that mathematical statement for the biggest gain into the following algorithm.

{% highlight asciidoc %}
S(A, len)
 1: max := 0
 2: FOR i := 1 TO len
 3:   FOR j := i + 1 TO len
 4:     IF A[j] - A[i] > max
 5:       max := A[j] - A[i]
 6: RETURN max
{% endhighlight %}

You convince yourself that algorithm $@S@$ works because line 4 considers each element of $@\\{ A[j] - A[i] \\,\|\\, 1 \\leq i < j \\leq 7 \\}@$ and selects the maximum so far.

Boom! Solved, right?

Wrong.

But you only find that your solution is wrong one hour before the deadline for the feature, when your QA analyst tells you that your feature is timing out some acceptance tests.

The problem is that algorithm $@S@$ is too slow.
The reason is that the running time of algorithm $@S@$ is cuadratic, i.e. [it behaves like function $@len ^ 2@$](#complexity-S).
Already with 100,000 data points, the algorithm takes more than 1 second to give you the corresponding answer.
Given that input sequences usually consists of hundreds of thousands of prices, this is totally unacceptable because end users won't buy software that takes more than half a second to react.
I mean, would you?

After a well deserved moment of sheer panic and seriously considering how much work would it be to update your CV, you get a grip of yourself.

(Psst, this is where you try to come up with an algorithm that takes half a second for an input of length 1,000,000.
Use [this benchmark](#).
The answer is XXX.
No peeking at the solution.
Scroll down only when you have a solution or you are sure you want to give up.)

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

Are you done?

Very good.

# Solution

Here is an algorithm that solves the problem in linear time.

{% highlight asciidoc %}
F(A, len)
 1: min := 0
 2: max := 0
 3: FOR i := 1 TO n
 4:   do et
{% endhighlight %}

Boom!

Don't spend too much time looking at it.
Instead, read the following explanation of the approach of algorithm $@F@$.

By the way, if you had read [Jill Rides Again](/2016/03/05/jill-rides-again.html), you would have saved yourself quite some time because the approach of algorithm $@F@$ is the same approach of Jill Rides Again.

Consider the input sequence $@1@$.
The biggest gain for this input is 0.
The biggest gain is always 0 for any given sequence of length 0 or 1.

Consider the input sequence $@1, 2@$.
The biggest gain for this input is 1.


# Implementation

Are you still reading?
Nice.
Let me make it worth your while.

Here algorithm $@F@$ implemented in 6 different languages.
Enjoy and don't forget to subscribe!

## C

## Golang

## Java

## Ruby

## Python

## OCaml

## Prolog



{% include subscribe.html %}
{% include share.html %}

# Appendix: Time complexity for algorithm $@S@$

Line 4 considers 21 elements ($@21 = 6 + 5 + 4 + 3 + 2 + 1@$).
For given length $@len@$, line 4 considers $@1 + 2 + ... + (len - 2) + (len - 1) = \\Sigma_{i = 1}^{len - 1} i@$.
Thus, the running time $@T(n)@$ is $@\\Sigma_{i = 1}^{n - 1} i@$.
$$
T(n) \\& = 1 + 2 + ... + (n - 2) + (n - 1) \\\\
     \\& = 1 + 2
$$

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
