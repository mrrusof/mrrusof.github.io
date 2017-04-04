---
layout: post
title: First Non-Repeated Character For Each Prefix
date: 2016-12-11 00:00:01 +00:00
author: Ruslan Ledesma-Garza
summary: "
For each non-empty prefix of a given string consisting of lowercase
English letters, find the first non-repeated character if any.
"
---

{{page.summary}}

**Input.**
The input file consists of one or more input strings, one per line and
is terminated by an EOF character. Consider the following example.

{% highlight asciidoc %}
a
aa
ab
aab
{% endhighlight %}

**Output.**
The output file consists the count of unique non-empty substrings for
each input string. The following example corresponds to the previous
example input.

{% highlight asciidoc %}
a
a
ab
ab
{% endhighlight %}

# Solution

For each prefix of a given input string, we determine the first
non-repeated letter by determining the repeated letters and picking
the first that is not repeated.

For example, consider the prefixes of input string  "ababc".

{% highlight asciidoc %}
a
ab
aba
abab
ababc
{% endhighlight %}

For prefix "a", there are no repeated letters and thus we pick letter
"a".
We also remember that "a" has appeared for the first time.
We remember the first non-repeated letter by placing a token on top of
the first position of "ababc" like so.

{% highlight asciidoc %}
*
ababc
{% endhighlight %}

For prefix "ab", we figure that "b" appears for the first time and
remember that. Also, the first non-repeated letter is still "a"
because "a" is not repeated and the token we put is still on the first
position of "ababc".

For prefix "aba", we know that "a" is repeated because we had already
found it before, so we remember that "a" is repeated. Also, we figure
that the first non-repeated letter is "b" because given that "a" is
repeated we move the token to the right and arrive to "b" which is not
repeated, like so.

{% highlight asciidoc %}
 *
ababc
{% endhighlight %}

For prefix "abab", we figure that "b" is repeated and remember
that. This time all letters are repeated because we move the token to
the right beyond the fourth position without finding a letter that is
not repeated, like so.

{% highlight asciidoc %}
    *
ababc
{% endhighlight %}

You can remember how many times you have found a given letter
by using a hash or an array with enough capacity.

# Ruby implementation

Here is a Ruby implementation.

{% highlight ruby %}
def first_non_repeated_prefix s
  return nil if s == ''
  o = ''
  l = 0
  h = {}
  s.chars.each_with_index do |c, i|
    if not h.has_key? c
      h[c] = :not_repeated
    else
      h[c] = :repeated
    end
    while l <= i and h[s[l]] == :repeated
      l += 1
    end
    o += s[l] if l < i or h[s[l]] == :not_repeated
  end
  return o
end

while true
  s = readline.strip rescue break
  puts first_non_repeated_prefix(s)
end
{% endhighlight %}


{% include subscribe.html %}

{% include share.html %}

{% include comments.html %}
