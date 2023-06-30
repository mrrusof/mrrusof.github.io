---
layout: post
title: 'Corner to Corner Matrix Traversal'
date: 2016-11-30
edited: 2016-12-12
author: Ruslan Ledesma-Garza
summary: "
Given a square matrix of integers, find the maximum sum of elements on
the way from the top-left cell to the bottom-right cell.
"
---

{{page.summary}}
From a given cell, you may only move either to the cell to the right
or to the cell below.
For example, consider the following matrix.

{% highlight asciidoc %}
1 2 3
4 5 6
7 8 9
{% endhighlight %}

The maximum sum is 29 and is given by the following path.

{% highlight asciidoc %}
 1    2    3
 |
 v
 4    5    6
 |
 v
 7 -> 8 -> 9
{% endhighlight %}

Your task is to write procedure `traverse` that takes a square matrix
of integers and returns the corresponding answer.

# Solution

Here is a Ruby implementation.

{% highlight ruby %}
def traverse m
  n = m.length - 1
  (0..n).each do |i|
    (0..n).each do |j|
      u = if i > 0 then m[i-1][j] else 0 end
      l = if j > 0 then m[i][j-1] else 0 end
      m[i][j] = [u, l].max + m[i][j]
    end
  end
  return m[-1][-1]
end
{% endhighlight %}


{% include share.html %}

{% include comments.html %}
