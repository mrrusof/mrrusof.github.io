---
layout: post
title: 'Substring with Concatenation of All Words'
date: 2016-11-28
author: Ruslan Ledesma-Garza
summary: "
Given a string and a list of words of the same length, find all start
indices in the string of concatenations of each word.
"
---

{{page.summary}}

For example, for string `"barfoomanthefoobar"` and words `["foo",
"bar"]` the corresponding indices are `[0,12]`.

**Input.**
The input consists of two lines.
The first line is the string where you will search for concatenations
of words.
The second line is the list of words.
Words are separated by a single space.

{% highlight asciidoc %}
barfoomanthefoobar
foo bar
{% endhighlight %}

**Output.**

Output consists of a single line.
The line consists of all the indices separated by a single space.
Order of the indices does not matter.

{% highlight asciidoc %}
0 12
{% endhighlight %}

# Solution

Here is a Ruby implementation.

{% highlight ruby %}
def do_et offset, l, s, ww
  i = 0
  tt = []
  while i + l - 1 < s.length
    tt << s[i..i+l-1]
    i += l
  end
  o = []
  ttq = []
  wc = {}
  ww.each { |w| if wc.has_key? w then wc[w] += 1 else wc[w] = 1 end }
  zc = wc.length
  tt.each_with_index do |t, i|
    # expand
    ttq << [t, i * l + offset]
    if wc.has_key? t
      if wc[t] == 1
        zc -= 1
      elsif wc[t] == 0
        zc += 1
      end
      wc[t] -= 1
    end
    # contract
    if ttq.length > ww.length
      h, _ = ttq.delete_at 0
      if wc.has_key? h
        if wc[h] == -1
          zc -= 1
        elsif wc[h] == 0
          zc += 1
        end
        wc[h] += 1
      end
    end
    # check
    if zc == 0
      o << ttq.first[1]
    end
  end
  return o
end

def swcoaw s, ww
  return [] if ww.length == 0
  l = ww.first.length
  return (0..l-1).reduce([]) { |o, i| o + do_et(i, l, s[i..-1], ww) }
end

def main
  s = readline rescue abort('Could not read string.')
  ww = readline.split(' ') rescue abort('Could not read words.')
  puts swcoaw(s, ww).join(' ')
end

main
{% endhighlight %}


{% include share.html %}

{% include comments.html %}
