---
layout: post
title: Dominant Palindromes
date: 2017-03-12
author: Ruslan Ledesma-Garza
summary: '
Given a string, find all dominant palindromes.  For a given string, a
dominant palindrome is a palindrome ocurring in the string that is
longer than 1 letter and is not a substring of another palindrome in
the string.
'
---

{{page.summary}}

For example, for string `abcbabb`, there are three such
palindromes as follows.

{% highlight asciidoc %}
abcbabb
-----
   ---
     --
{% endhighlight %}

**Input.**
The input consists of one or more strings, each on a line of its own.
The input is terminated by EOF.  The following is a sample input
file.

{% highlight asciidoc %}
abcababcdfgghi
abcbab
abcbabb
zyxxyayx
baaa
aaabaa
{% endhighlight %}

**Output.**
For each input string, the output consists of an output case.
An output case consists of the input string, followed by the dominant
palindromes in any order, followed by a blank line.
The following is the output file that corresponds to the sample input
file.

{% highlight asciidoc %}
abcababcdfgghi
aba
bab
gg

abcbab
abcba
bab

abcbabb
abcba
bab
bb

zyxxyayx
yxxy
xyayx

baaa
aaa

aaabaa
aaa
aabaa

{% endhighlight %}

# Solution

Given a string of length `n`, the proposed solution takes `O(n)` time
and space.

We illustrate our solution on the following string `s`.

{% highlight asciidoc %}
  s: abcbabb
pos: 0123456
{% endhighlight %}

We determine the dominant palindromes from left to right in two steps.
We determine the longest palindrome greater than one character for
each position and then discard palindromes that are subsumed.

For string `s`, the longest palindromes are the following.  For
position 3 the longest palindrome is `bcb`, for position 4 is `abcba`,
for position 5 is `bab`, and for position 6 is `bb`.

For string `s`, there is only one subsumed palindrome.  That
palindrome is the one for position 3 and the dominant palindrome is
the one for position 4.

To determine longest palindromes we apply the following rules.

When there is a palindrome for a position, the longest palindrome is
either a continuation of a previous longest palindrome or a new
palindrome.  For example, for position 3 the longest palindrome `bcb`
is new because the characters in positions 1 and 3 are the
same. Another example, for position 4 the longest palindrome `abcba`
is a continuation of the palindrome for position 3 because the
characters in positions 0 and 4 are the same.

When there is no palindrome, neither condition is true.
For example, for position 2 there is no palindrome because there is no
palindrome for position 1 and the characters in positions 0 and 2 are
different.

When a position corresponds to a new palindrome, the reason is that
either the character two positions before is the same or the character
in the previous position is the same.  For example, position 3 is a
new palindrome because the character two positions before is the
same.  Another example, position 6 is a new palindrome because the
previous character is the same.

When the apply the rules for longest palindromes to each position, we
annotate the position with the start position of the longest
palindrome if any, like so.

{% highlight asciidoc %}
    s: abcbabb
  pos: 0123456
start: ___1035
{% endhighlight %}

Given the list of start positions, we determine the dominant
palindromes by moving from left to right on the list, dropping any
previous start position that is greater than the current start
position because those are subsumed, like so.


{% highlight asciidoc %}
       s: abcbabb
     pos: 0123456
   start: ___1035
dominant: ____035
{% endhighlight %}

Given that a previous palindrome can only be subsumed when we
determine a longest palindrome, we drop subsumed palindromes as we
determine palindromes in the following way.

When we continue a palindrome, we drop the start position for the
previous position because we just subsumed the longest palindrome for
the previous position.  For example, for position 4 we continue the
palindrome for position 3, and so we drop the start position for
position 3.

When we find a new longest palindrome of length 2, there is nothing to
drop, because any palindrome for the previous position starts before
the previous position.  For example, for position 6, the palindrome
for position 5 starts in position 3.

When we find a new longest palindrome of length 3 at position `i`, we
drop the previous start position when that start position is `i - 2`.
For example, consider the start and dominant positions of string
`aaa`.

{% highlight asciidoc %}
       s: aaa
     pos: 012
   start: _00
dominant: __0
{% endhighlight %}

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def dominant_palindromes s
  ss = Array.new(s.length, nil)
  left = 0
  (0...s.length).each do |right|
    if left < right - 2 and left >= 0 and s[left] == s[right]
      ss[right - 1] = nil
      ss[right] = left
      left -= 1
    elsif (left = right - 2) >= 0 and s[left] == s[right]
      ss[right - 1] = nil if ss[right - 1] == left
      ss[right] = left
      left -= 1
    elsif (left = right - 1) >= 0 and s[left] == s[right]
      ss[right] = left
      left -= 1
    else
      left = right
    end
  end
  ss.each_with_index { |left, right| if !!left then puts s[left..right] end }
end

while true
  s = readline.strip rescue break
  puts s
  dominant_palindromes s
  puts
end
{% endhighlight %}

{% include subscribe.html %}

{% include share.html %}

{% include comments.html %}
