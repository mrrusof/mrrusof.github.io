---
layout: post
title: Most Recent Common Ancestor
date: 2017-10-13
edited: 2017-10-17
author: Ruslan Ledesma-Garza
summary: Given two nodes of a tree, find their most recent common ancestor.
---

{{page.summary}}

**Input.**
The input consist of one tree.  The first line of input is a pair of
integers `x` and `y` separated by a space.  `x` and `y` are the nodes
that you will consider.  The second line of input is a single
integer `n` which is the count of edges in the tree.  Each one of the
next `n` lines consist of a pair of integers `a` and `b` separated by a
space. The pair `a b` corresponds to an edge from `a` and `b`.  The
following is an example input.

{% highlight asciidoc %}
5 7
6
1 2
1 3
3 4
4 5
4 6
6 7
{% endhighlight %}

**Output.**
The output consist of a single integer, the most recent common
ancestor.  The following output corresponds to the example input.

{% highlight asciidoc %}
4
{% endhighlight %}

# Solution

We find the most recent common ancestor in the following way.

We load the graph into a data structure such that there is a reference
from each node to its parent.  The following diagram illustrates the
data structure for the example input.

{% highlight asciidoc %}
   1
  / \
 2   3
      \
       4
      / \
     6   5
    /
   7
{% endhighlight %}

We place two pebbles `A` and `B` on top of the nodes we consider, like
so.

{% highlight asciidoc %}
   1
  / \
 2   3
      \
       4
      / \
     6   5 A
    /
   7 B
{% endhighlight %}

We move the pebble that is farther from the root to the same distance
than the other.  In our example, we move `A` from `7` to `6`.

{% highlight asciidoc %}
   1
  / \
 2   3
      \
       4
      / \
   B 6   5 A
    /
   7
{% endhighlight %}

We move both pebbles closer to the root one step at a time until they
meet.  In our example the pebbles meet at `4`.

{% highlight asciidoc %}
   1
  / \
 2   3
      \
     B 4 A
      / \
     6   5
    /
   7
{% endhighlight %}

The node where the pebbles meet is the most recent common ancestor.

# Implementation

Here is a Ruby implementation.

{% highlight ruby %}
#!/usr/bin/env ruby

def depth n, p
  i = 0
  while !!p[n]
    n = p[n]
    i += 1
  end
  return i
end

def most_recent_common_ancestor a, b, p
  da = depth a, p
  db = depth b, p
  if da < db
    diff = db - da
    while diff > 0
      b = p[b]
      diff -= 1
    end
  elsif db < da
    diff = da - db
    while diff > 0
      a = p[a]
      diff -= 1
    end
  end
  while a != b
    a = p[a]
    b = p[b]
  end
  return a
end

def main
  x, y = readline.strip.split(' ').map(&:to_i)
  n = readline.strip.to_i
  p = {}
  while n > 0
    a, b = readline.strip.split(' ').map(&:to_i)
    p[b] = a
    n -= 1
  end
  puts most_recent_common_ancestor x, y, p
end

main
{% endhighlight %}

{% include share.html %}

{% include comments.html %}
