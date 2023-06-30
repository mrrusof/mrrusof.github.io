---
layout: post
title: Longest Word
date: 2017-03-08
edited: 2017-03-18
author: Ruslan Ledesma-Garza
summary: '
Given a set of strings, find the length of any longest word you can
make with the letters that are common.
'
---

{{page.summary}}

For example, consider the following set of strings.

{% highlight asciidoc %}
ijkik
kkjki
kljkikiijk
{% endhighlight %}

The length of a longest word is 4 because a longest word is `kikj`.

*Edit.* This post was previously titled 'Longest Common Anagram' and
had a different statement.  Thanks to [Marcelo
Melendez](https://www.linkedin.com/in/eugenio-marcelo-melendez-248b7942/)
for pointing out that the title and statement were confusing.

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
single integer that indicates the length of any longest word for the
case.
The following is the output file that corresponds to the sample input
file.

{% highlight asciidoc %}
4
{% endhighlight %}

# Solution

Consider the count of letters for each string.

{% highlight asciidoc %}
String     | i | j | k | l
---------------------------
ijkik      | 2 | 1 | 2 | 0
kkjki      | 1 | 1 | 3 | 0
kljkikiijk | 3 | 2 | 4 | 1
{% endhighlight %}

A longest word consists only of letters appearing in the
words.  For each letter, a longest word has as many
copies of the letter as the minimum count amongst the strings.
Consider the minimum count of letters amongst the strings.

{% highlight asciidoc %}
String     | i | j | k | l
---------------------------
ijkik      | 2 | 1 | 2 | 0
kkjki      | 1 | 1 | 3 | 0
kljkikiijk | 3 | 2 | 4 | 1
           ----------------
             1   1   2   0  <-- minimum count of each letter
{% endhighlight %}

Given the minimum count of letters, we know that a longest word
consists of one `i`, one `j`, and two `k`, thus giving a
grand total of 4 letters.

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def length_of_a_longest_word mm
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
  puts length_of_a_longest_word mm
end
{% endhighlight %}


{% include share.html %}

{% include comments.html %}
