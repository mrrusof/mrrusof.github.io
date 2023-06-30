---
layout: post
title: 'The Pattern Matching Problem'
date: 2016-11-04
author: Ruslan Ledesma-Garza
summary: '
Find a match, any match.
You will be given very simple patterns.
But, how fast can you come up with a solution when you are not allowed to use your regexp library?
And yes, you might find this in your next interview.
'
---

{{page.summary}}

# Problem

**Input.**
A set of words and a set of patterns.  Each word and each pattern is
given in a line by itself.  Words and patterns are separated by
character `#`.  In patterns, the character period `.` matches any
character.  The input is terminated by EOF.

**Output.**
For each pattern, write in a single line the pattern, a space, and
true if the pattern matches a word or false if it does not.  Output
patterns in the order you read them.

# Example

**Input**

{% highlight asciidoc %}
coin
coins
does
dork
why
work
#
coin
coins
c
co
coi
coins
coins.
.coins
does
d
do
doe
.oes
d.es
do.s
doe.
w..
w...
w....
{% endhighlight %}

**Output**

{% highlight asciidoc %}
coin true
coins true
c false
co false
coi false
coins true
coins. false
.coins false
does true
d false
do false
doe false
.oes true
d.es true
do.s true
doe. true
w.. true
w... true
w.... false
{% endhighlight %}

# Solution

Have you heard about [tries](https://en.wikipedia.org/wiki/Trie)?
Use them maybe?

Here is a Ruby implementation.

{% highlight ruby %}
class Hash

  def save_word w
    h = self
    w.chars.each do |l|
      h[l] = {} if not h[l]
      h = h[l]
    end
    h[:eow] = true
  end

  def word_exists? w
    h = self
    w.chars.each_with_index do |l, i|
      if l == '.'
        return h.any? { |k,v| k != :eow and v.word_exists? w[i+1..-1] }
      elsif not h[l]
        return false
      end
      h = h[l]
    end
    return !!h[:eow]
  end

end

def main
  words = {}
  while true
    w = readline.strip
    break if w == '#'
    words.save_word w
  end
  while true
    begin
      p = readline.strip
    rescue EOFError => ex
      break
    end
    puts "#{p} #{words.word_exists? p}"
  end
end

main
{% endhighlight %}


{% include share.html %}

{% include comments.html %}
