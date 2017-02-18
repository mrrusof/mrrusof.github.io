---
layout: post
title: Equilibrium Index
date: 2017-02-17
author: Ruslan Ledesma-Garza
summary: "
Given an array of integers, find an equilibrium index.
An equilibrium index of an array is an index such that the sum of
elements at lower indexes is equal to the sum of elements at higher
indexes.
"
---

{{page.summary}}

For example, consider the following array `A`.

{% highlight asciidoc %}
       A: -7  1  5  2 -4  3  0
position:  0  1  2  3  4  5  6
{% endhighlight %}

Index 3 is an equilibrium index.

{% highlight asciidoc %}
A[0] + A[1] + A[2] = A[4] + A[5] + A[6]
{% endhighlight %}

Index 6 is also an equilibrium index, because sum of zero elements is zero.

{% highlight asciidoc %}
A[0] + A[1] + A[2] + A[3] + A[4] + A[5] = 0
{% endhighlight %}

Index 7 is not an equilibrium index, because it is not a valid index of array `A`.

**Input.**
The input file consists of one or more arrays, each on a single line.
Each array consists of a sequence of integers, each pair separated by
a single space.  The input file is terminated by an EOF
character. Consider the following example.

{% highlight asciidoc %}
-7  1  5  2 -4  3  0
1

1 1
1 0
{% endhighlight %}

**Output.**
The output file consists of an output line for each array in the
input. Each output line consists of an equilibrium index (if any) or
-1 when no equilibrium index exists. For example, the following output
corresponds to the previous example input.

{% highlight asciidoc %}
6
-1
-1
-1
0
{% endhighlight %}

# Solution

The proposed solution is `O(n)`.

Just consider each position of a given array, from left to
right. Start with a variable `l` for the left sum set to zero and a
variable `r` for the right sum set to the sum of all elements except the
first. Then, as you go from left to right, add the element that
becomes part of the left side to `l` and subtract from `r` the element that
leaves the right side. Return the current index when `l` and `r` are equal.

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def equilibrium_index nn
  return -1 if nn.length == 0
  return 0 if nn.length == 1
  ls = 0
  rs = nn[1..-1].reduce(0, &:+)
  nn[0..-2].each_with_index do |n, i|
    return i if ls == rs
    ls += n
    rs -= nn[i + 1]
  end
  return -1
end

while true
  nn = readline.strip.split(' ').map(&:to_i) rescue break
  puts equilibrium_index nn
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
        this.page.url = 'http://ruslanledesma.com/2017/02/17/equilibrium-index.html';  // Replace PAGE_URL with your page's cbanonical URL variable
        this.page.identifier = '2017-02-17-equilibrium-index'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
