---
layout: post
title: 'Corner to Corner Matrix Traversal'
date: 2016-11-30
author: Ruslan Ledesma-Garza
summary: "
Given a square matrix of integers, find the maximum sum of elements on
the way from the top-left cell to the bottom-right cell.
"
---

{{page.summary}}
From a given cell, you may only move either to the cell to the right
or to the cell below.
For example, consider the following matrix.

{% highlight asciidoc %}
1 2 3
4 5 6
7 8 9
{% endhighlight %}

The maximum sum is 29 and is given by the following path.

{% highlight asciidoc %}
 1    2    3
 |
 v
 4    5    6
 |
 v
 7 -> 8 -> 9
{% endhighlight %}

Your task is to write procedure `traverse` that takes a square matrix
of integers and returns the corresponding answer.

# Solution

Here is a Ruby implementation.

{% highlight ruby %}
def traverse m
  n = m.length
  (0..n-1).each do |r|
    (0..r).each do |j|
      i = r - j
      u = if i > 0 then m[i-1][j] else 0 end
      l = if j > 0 then m[i][j-1] else 0 end
      m[i][j] = [u, l].max + m[i][j]
    end
  end
  (1..n-1).each do |r|
    (n-1).downto(r).each do |j|
      i = r + n-1 - j
      u = if i > 0 then m[i-1][j] else 0 end
      l = if j > 0 then m[i][j-1] else 0 end
      m[i][j] = [u, l].max + m[i][j]
    end
  end
  return m[-1][-1]
end
{% endhighlight %}


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
        this.page.url = 'http://ruslanledesma.com/2016/11/30/corner-to-corner-matrix-traversal.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-11-30-corner-to-corner-matrix-traversal'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
