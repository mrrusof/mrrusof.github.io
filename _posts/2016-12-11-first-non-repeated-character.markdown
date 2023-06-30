---
layout: post
title: First Non-Repeated Character
date: 2016-12-11
author: Ruslan Ledesma-Garza
summary: "
For a given string consisting of lowercase English letters, find the
first non-repeated character if any.
"
---

{{page.summary}}

After you solve this problem, solve [First Non Repeated Character For
Each Prefix](/2016/12/11/first-non-repeated-character-prefixes.html).

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
b
{% endhighlight %}

# Solution

We solve the problem by detecting the letters that are repeated and
then finding the first that is not.

For example, consider input string 'aab'.
To determine the repeated letters, iterate the string in the following
way. At each letter, record the occurrence of the letter the first
time you find it and the repetition of the letter every subsequent
time you find the letter.
You may use a hash or an array with sufficient capacity if you have
aversion to hashes.
When you are done, iterate the string again and return the first
letter that is not repeated.

# Ruby implementation

{% highlight ruby %}
def first_non_repeated s
  h = {}
  s.chars.each do |c|
    if not h.has_key? c
      h[c] = :not_repeated
    else
      h[c] = :repeated
    end
  end
  return s.chars.find { |c| h[c] == :not_repeated }
end

while true
  s = readline.strip rescue break
  puts first_non_repeated(s)
end
{% endhighlight %}



{% include share.html %}

{% include comments.html %}
