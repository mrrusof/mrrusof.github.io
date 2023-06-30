---
layout: post
title: The Parentheses Problem
date: 2017-02-18
author: Ruslan Ledesma-Garza
summary: '
You are given a string that may contain any number of parentheses "("
and ")".
Remove from the string the minimum number of parentheses so that the
remaining parentheses are balanced.
'
---

{{page.summary}}

Consider the following examples.

{% highlight asciidoc %}
"()())()" -> "()()()" or "(())()"
"(bla)(ble))(bli)" -> "(bla)(ble)(bli)" or "(bla(ble))(bli)"
")(" -> ""
")))" -> ""
{% endhighlight %}

**Input.**
The input file consists of one or more input strings, each on a single line.
The input file is terminated by an EOF character.
Consider the following example.

{% highlight asciidoc %}
()())()
(bla)(ble))(bli)
)(
)))
(helo)())
(((((
(((()((
)()
{% endhighlight %}

**Output.**
The output file consists of an output string for each input line, each
on a separate line.
For example, the following output
corresponds to the previous example input.

{% highlight asciidoc %}
()()()
(bla)(ble)(bli)



(helo)()

()
()
{% endhighlight %}

# Solution

The time and space complexity of the proposed solution are both `O(n)`.

Consider the string `())(` from left to right. Given that the first
position is an opening parenthesis, remember that position in stack
of opening parentheses that are unmatched `op`.

{% highlight asciidoc %}
    ())(
    0123
    |
    V
op: 0
{% endhighlight %}

The second position is a closing parenthesis. We match it with the
opening parenthesis that we found before and therefore remove the
opening parenthesis from `op`.


{% highlight asciidoc %}
    ())(
    0123
     *
op:
{% endhighlight %}

The third position is again a closing parenthesis. This time we don't
have an opening parenthesis that matched and so we remember this
position for later deletion in list `dl`.

{% highlight asciidoc %}
    ())(
    0123
      |
      V
dl:   2
op:
{% endhighlight %}

The fourth and last position is an opening parenthesis. We remember
that parenthesis in `op` like before.

{% highlight asciidoc %}
    ())(
    0123
       |
       V
dl:   2
op:    3
{% endhighlight %}

We don't have any more parenthesis to match that last opening
parenthesis because we are at the end of the string. So, it makes
sense to remove that last opening parenthesis together with anything
in list `dl`. By doing so we get string `()`, a balanced string with
the minimum number of parentheses removed.

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def balance s

  op = [] # opening parentheses
  dl = [] # delete these indexes later

  s.chars.each_with_index do |c, i|
    if c == '('         # `c` is an opening parenthesis
      op << i           #   that we have to remember.
    elsif c == ')'      # `c` is a closing parenthesis
      if op.length == 0 #   that does not match an opening parenthesis
        dl << i         #     and therefore we will delete later
      else              #   that corresponds to an opening parenthesis
        op.pop          #     and therefore we forget about both
      end
    end
  end
  dl += op # delete later all unmatched opening/closing parentheses

  r = ''   # result string
  di = 0   # index in dl
  s.chars.each_with_index do |c, i|
    if di < dl.length and dl[di] == i # either skip this character
      di += 1
    else                              # or not
      r += c
    end
  end

  return r
end

while true
  s = readline.strip rescue break
  puts balance s
end
{% endhighlight %}


{% include share.html %}

{% include comments.html %}
