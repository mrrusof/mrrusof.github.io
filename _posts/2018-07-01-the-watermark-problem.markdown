---
layout: post
title: The Watermark Problem
date: 2018-07-01
author: Ruslan Ledesma-Garza
summary: Calculate how much water a histogram can hold.
---

You are given a bar graph as a list of integers.  Write a function
that returns the amount of water the bar graph would hold if you
poured water over the top.

Consider the following bar graph

```asciidoc
          _ . . . _
      _ .| |  _  | |
  _ .| | | |_| |_| |
_| |_| |_|         |
0 1 0 2 0 3 1 2 1 3
```

This graph can hold 8 units of water.  The dots in the graph mark
the level of water for a given column.

**Input.**
The input consists of one or more bar graphs.  Each bar graph is given
on a separate line as a list of space separated integers.  Each line
is terminated by a newline character (`\n`) and the input is
terminated by EOF.

```asciidoc
0 1 0 2 0 3 1 2 1 3
0 1 0 1 0
```

**Output.**
The output consists of the amount of units of water that each bar
graph can hold.  Each amount appears in the line that corresponds to
its graph.  Each line is terminated by a newline character (`\n`) and
the output is terminated by EOF.

```asciidoc
8
1
```

# Problem Structure

Given a histogram, the structure of the histogram consists of a
sequence of valleys and peaks that begins with an edge and ends with
another.  Consider the following example histogram.

```asciidoc
        peaks
          |
  -------------------
  |   |   |   |     |
  |   |   v   |     |
  |   v   _   v     v
  v   _ .| |. _ . . _
  _ .| | | |_| |  _| |
_| |_| |_|     |_|   |
0 1 0 2 0 3 1 2 0 1 2  <-- value
0 1 2 3 4 5 6 7 8 9 10 <-- index
^   ^   ^   ^   ^^^ ^
|   |   |   |    |  |
|   --------------  |
|         |         |
|      valleys      |
|                   |
---------------------
          |
        edges
```

When we pour water over the histogram, a unit of water either remains
trapped or escapes the histogram.  A unit of water remains in the
histogram when it is trapped in a valley.  In the example, every space
below the dots contains water.  A unit of water escapes the histogram
by overflowing the left or the right edge.  In the example, water
above the dots or the peaks overflows.

The direction of overflow is determined by the highest peak.  In the
example, the highest peak is at index 5.  Water poured to the left
of the highest peak that overflows does so by the left edge.  For
water poured to the right, water overflows to the right.

# Approach

We approach the problem by splitting the histogram by the first
highest peak and solving each part.

To illustrate the approach, consider the highest peak in the example
histogram. 

```asciidoc
     highest peak
          |
          v
          _
      _  | |  _     _
  _  | | | |_| |  _| |
_| |_| |_|     |_|   |
0 1 0 2 0 3 1 2 0 1 2  <-- value
0 1 2 3 4 5 6 7 8 9 10 <-- index
```

The splitting of the histogram gives the following left-hand side.

```asciidoc
          _
      _  | |
  _  | | | |
_| |_| |_| |
0 1 0 2 0 3  <-- value
0 1 2 3 4 5  <-- index
^
|
left edge of the histogram
```

We solve the left-hand side by detecting each valley and computing its
corresponding amount of water.

We detect valleys in the following way.  We move from left to right.
We record the position of the left edge of the histogram (index 0 in
the example) and find the first value that is higher or equal (index 1
in the example).  Those positions are the left and right edges of a
valley.

```asciidoc
          _
      _  | |
  _  | | | |
_| |_| |_| |
0 1 0 2 0 3  <-- value
0 1 2 3 4 5  <-- index
^ ^
| |
| right edge of a valley
left edge of a valley
```

We accumulate the amount of water that the valley holds.  In our
example the length of the valley is 0 and thus the amount of water
is 0.  Next, we record the position of the right edge (index 1) and
look for a value that is higher or equal (index 3).

```asciidoc
          _
      _  | |
  _  | | | |
_| |_| |_| |
0 1 0 2 0 3  <-- value
0 1 2 3 4 5  <-- index
  ^   ^
  |   |
  |   right edge of a valley
  left edge of a valley
```

We accumulate the corresponding amount of water (1 unit of water).  We
repeat the process until we get to the end.

Detecting valleys by finding the first value higher or equal to the
current left edge works because water can only overflow to the left.

We solve the right-hand side of the histogram by reversing it and
applying the same procedure.  Consider the reverse of the right-hand
side of the example.

```asciidoc
      highest peak
           |
           v
           _
 _     _  | |
| |_  | |_| |
|   |_|     |
 2 1 0 2 1 3 <-- value
 0 1 2 3 4 5 <-- index
```

# Implementation

The following is our Ruby implementation of the approach.

{% highlight ruby %}
#!/usr/bin/env ruby

def volume_of_segment h, s, e
  watermark = [h[s], h[e]].min
  volume = 0
  h[(s+1)..(e-1)].each do |n|
    volume += watermark - n
  end
  return volume
end

def volume_increasing h
  return 0 if h.empty?
  volume = 0
  max = h[0]
  maxi = 0
  h[1..-1].each_with_index do |n, i|
    if n >= max
      volume += volume_of_segment h, maxi, (i + 1)
      max = n
      maxi = (i + 1)
    end
  end
  return volume
end

def volume h
  return 0 if h.empty?
  max = h.max
  maxi = h.find_index max
  first_half = h[0..maxi]
  second_half = h[maxi..-1].reverse
  vfh = volume_increasing(first_half)
  vsh = volume_increasing(second_half)
  return vfh + vsh
end

while true
  h = readline.strip.split(' ').map(&:to_i) rescue break
  puts volume h
end
{% endhighlight %}

The run-time of our implementation if $@O(n)@$ where $@n@$ is the
length of the histogram.


{% include share.html %}

{% include comments.html %}
