---
layout: post
title: Longest Common Anagram
date: 2017-03-08
edited: 2017-03-10
author: Ruslan Ledesma-Garza
summary: '
Given a set of strings, find the length of a longest common
anagram.
'
---

{{page.summary}}

For example, the length of a longest common anagram for the
following strings is 4 because a longest common anagram is `kikj`.

{% highlight asciidoc %}
ijkik
kkjki
kljkikiijk
{% endhighlight %}

**Input.**
The input file consists of one or more cases.  Each case consists of a
number `n` on a single line followed by `n` lines, each consisting of
a word.  Each word consists of lowercase ascii letters from `a` to
`z`.  The input is terminated by EOF.  The following is a sample input
file.

{% highlight asciidoc %}
3
ijkik
kkjki
kljkikiijk
{% endhighlight %}

**Output.**
The output file consists of one line per case, each consisting of a
single integer that indicates the length of the longest common
anagram for the case.
The following is the output file that corresponds to the sample input
file.

{% highlight asciidoc %}
4
{% endhighlight %}

# Solution

Consider the count of letters for each word.

{% highlight asciidoc %}
Word       | i | j | k | l
---------------------------
ijkik      | 2 | 1 | 2 | 0
kkjki      | 1 | 1 | 3 | 0
kljkikiijk | 3 | 2 | 4 | 1
{% endhighlight %}

A longest common anagram consists only of letters appearing in the
words.  For each letter, a longest common anagram has as many
instances of the letter as the minimum count amongst the words.
Consider the minimum count of letters amongst the words.

{% highlight asciidoc %}
Word       | i | j | k | l
---------------------------
ijkik      | 2 | 1 | 2 | 0
kkjki      | 1 | 1 | 3 | 0
kljkikiijk | 3 | 2 | 4 | 1
           ----------------
             1   1   2   0  <-- minimum count of each letter
{% endhighlight %}

Given the minimum count of letters, we know that a longest common
anagram consists of one `i`, one `j`, and two `k`, thus giving a
grand total of 4 letters.

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def length_of_longest_common_anagram mm
  mins = Array.new(26)
  count = Array.new(26)
  mm.each { |m|
    count.map! { |_| 0 }
    m.chars.each { |l|
      count[l.ord - 'a'.ord] += 1
    }
    count.each_with_index { |c, l|
      if mins[l] == nil or c < mins[l]
        mins[l] = c
      end
    }
  }
  return mins.reduce(0, &:+)
end

while true
  n = readline.strip.to_i rescue break
  mm = []
  while n > 0
    m = readline.strip
    mm << m
    n -= 1
  end
  puts length_of_longest_common_anagram mm
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
        this.page.url = 'http://ruslanledesma.com/2017/03/08/longest-common-anagram.html';  // Replace PAGE_URL with your page's cbanonical URL variable
        this.page.identifier = '2017-03-08-longest-common-anagram'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
