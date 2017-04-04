---
layout: post
title: 'Number of Islands'
date: 2016-11-14
author: Ruslan Ledesma-Garza
summary: "
A popular problem associated with Leetcode's online judge.
Determine the count of islands in a grid of locations that are either land or water.
"
---

{{page.summary}}

# Problem

Consider the following grid of locations.

{% highlight asciidoc %}
11110
11010
11000
00000
{% endhighlight %}

Each location is either land (1) or water (0).

Your task is to count the number of islands.
An island is surrounded by water and consists of several pieces of
land reachable from each other by moving horizontally or vertically.
Whatever is outside the four edges of the grid is water.

**Input.**
The input consists of the number of rows of the grid in one line
followed by the rows of the grid.
For example

{% highlight asciidoc %}
4
11000
11000
00100
00011
{% endhighlight %}

**Output.**

The output consists of a single number, the count of islands in the
grid.
For example, the number of islands in the sample input is 3.

# Solution

Here is a Ruby implementation.

{% highlight ruby %}
def explore_island m, v, i, j
  w = [[i, j]]
  v[i][j] = true
  while not w.empty?
    i, j = w.pop
    if j > 0 and m[i][j-1] == 1 and !v[i][j-1]
      w << [i, j-1]
      v[i][j-1] = true
    end
    if j + 1 < m[i].length and m[i][j+1] == 1 and !v[i][j+1]
      w << [i, j+1]
      v[i][j+1] = true
    end
    if i > 0 and m[i-1][j] == 1 and !v[i-1][j]
      w << [i-1, j]
      v[i-1][j] = true
    end
    if i + 1 < m.length and m[i+1][j] == 1 and !v[i+1][j]
      w << [i+1, j]
      v[i+1][j] = true
    end
  end
end

def count_islands m
  v = m.map { |r| r.map { |_| false }}
  c = 0
  m.each_with_index do |r, i|
    r.each_with_index do |l, j|
      if l == 1 and !v[i][j]
        explore_island m, v, i, j
        c += 1
      end
    end
  end
  return c
end

def main
  n = readline.strip.to_i
  m = []
  while n > 0
    m << readline.strip.chars.map { |e| e.to_i }
    n -= 1
  end
  m.each { |r| puts "#{r}" }
  puts count_islands m
end

main
{% endhighlight %}


{% include subscribe.html %}
{% include share.html %}

{% include comments.html %}
