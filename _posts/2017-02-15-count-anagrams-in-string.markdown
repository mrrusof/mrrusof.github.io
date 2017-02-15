---
layout: post
title: Count Anagrams in String
date: 2017-02-15
author: Ruslan Ledesma-Garza
summary: "
Given a strings `s` and `p`, count the anagrams of `s` that occur in
`p`.
"
---

{{page.summary}}

For example, given `s` = "hello" and `p` = "ellohello", the count of
anagrams is 5.

**Input.**
The input file consists of one or more pair of lines where the first
line is string `s` and the second string `p`.
The input file is terminated by an EOF character. Consider the
following example.

{% highlight asciidoc %}
hello
ellohello
{% endhighlight %}

**Output.**
The output file consists the count anagrams of `s` that occur in
`p`. The following output corresponds to the previous
example input.

{% highlight asciidoc %}
5
{% endhighlight %}

# Solution

Consider the 5 anagrams of `s` = "hello" that occur in `p` =
"ellohello".

{% highlight asciidoc %}
ellohello
-----
 -----
  -----
   -----
    -----
{% endhighlight %}

Each anagram is a substring of `p` and has the frequency of
letters that `s` has (one "h", one "e", two "l", and one
"o"). Therefore, to count the anagrams in `p`, consider each substring
of `p` of length 5 and check that the frequency of letters is the
same as that of `s`.

To consider all substrings of length 5, it suffices to _place a
window of length 5 on the left of `p`_ (in other words, underline the
first 5 characters) and then move the window to the right one position
at a time.

To check the frequency of letters for every position of the window, do
the following. For the first position, determine how many letters of
`s` occur in the window. If that frequency is the same as the
frequency of letters in `s`, count one anagram. In the example,
the substring given by the first window (`elloh`) passes the check.
For the rest of the positions, decrement the counter for the letter
that exits the window through the left and increase the counter for
the letter that enters the window from the right. In the example, for
the second position of the window (`llohe`), decrement the counter for
`e` (because `e` exits the window through the left) and then increment
the counter for `e` (because another `e` enters the window from the
right).

Finally, to avoid iterating over the counters every time you do the
frequency check, give the counters the following meaning. Each counter
is not how many times a letter occurs in a given window, but how many
more occurrences of the letter you need to make an anagram. In the
example, for the first position of the window, the counter for each
letter of `s` is 0 because the first position is an anagram. Then,
have an additional counter for how many counters have reached
zero. Decrement that counter every time a letter that enters makes a
counter reach zero and increment the counter every time a letter that
exits makes a counter go above zero.

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def count_anagrams s, p
  ac = 0        # anagram count
  f = {}        # counters for the characters in `s`
  s.chars.each { |c| f[c] = (f[c] || 0) + 1 }
  cl = f.length # counters left to reach zero
  p.chars.each_with_index do |c, i|
    # contract window
    if i >= s.length
      ec = p[i - s.length] # the letter that exits
      if !!f[ec]           # is the letter that exits in `s`?
        f[ec] += 1         # it is and we note that it left the window
        if f[ec] == 1      # do we lack instances of the letter?
          cl += 1          # we do and we note that
        end
      end
    end
    # expand window
    if !!f[c]      # is the letter that enters in `s`?
      f[c] -= 1    # it is and we make a note that it is in the window
      if f[c] == 0 # have we found enough instances of the letter?
        cl -= 1    # we have and we note that
        if cl == 0 # have we found all the letters of `s`?
          ac += 1  # we have, this window position is an anagram
        end
      end
    end
  end
  return ac
end

while true
  s = readline.strip rescue break
  p = readline.strip
  puts count_anagrams s, p
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
        this.page.url = 'http://ruslanledesma.com/2017/02/15/count-anagrams-in-string.html';  // Replace PAGE_URL with your page's cbanonical URL variable
        this.page.identifier = '2017-02-15-count-anagrams-in-string'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
