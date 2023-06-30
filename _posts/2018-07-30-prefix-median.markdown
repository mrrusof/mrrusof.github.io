---
layout: post
title: Prefix Median
date: 2018-07-30
author: Ruslan Ledesma-Garza
summary: '
Given a sequence of integers, print the median for each non-empty
prefix of the sequence.
'
---

{{page.summary}}

**Input.**
The input consists of one or more sequences.  Each sequence consists
of integers separated by space.  Each sequence is terminated by a new
line character (`\n`).  The input is terminated by EOF.  Consider the
following example.

```asciidoc
1 7 6 8 9
-4 -3 -2 -1
```

**Output.**
The output consists of one output sequence for each input sequence.
Each output sequence consists of one real number for each non-empty
prefix of the corresponding input sequence.  Each real number is the
median of the corresponding prefix.  Each real number is formatted with
one decimal after the decimal point.  Each output sequence is
terminated by a new line character (`\n`).  The output is terminated
by EOF.  The following example corresponds to the example input.

```asciidoc
1.0 4.0 6.0 6.5 7.0
-4.0 -3.5 -3.0 -2.5
```

# Approach

We track the median by means of a pair of
[heaps](https://en.wikipedia.org/wiki/Heap_(data_structure)).  As we
read the sequence, we keep the lower half of the elements in a
max-heap and the upper half in a min-heap.  For every non-empty
prefix, the median is either the root of one heap or the average of
both roots.

We illustrate our approach by example.  Consider the following
sequence and the min-heap and max-heap after reading each element from
left to right.

```asciidoc
 element: 1 7 6 8 9 2 3 0
         -----------------
min-heap:               9
                    9 9 8
                8 9 8 8 7
            7 7 7 8 7 7 6 <-- root
         -----------------
max-heap: 1 1 6 6 7 6 6 3 <-- root
              1 1 6 2 3 2
                  1 1 2 1
                      1 0
```

When we read an element, we place it in max-heap when it is less or
equal to the root, otherwise we place it in the min-heap.  For
example, `1` goes to the max-heap and `7` goes to the min-heap.

The crucial aspect of our approach is that we satisfy the following
condition at every moment: `max-heap` is at least as big as `min-heap`
and at most one element more than `min-heap`.  To satisfy the
condition, we balance the heaps when they become unbalanced.  Consider
the case when the min-heap becomes bigger than the max-heap after
reading `9`.

```asciidoc
 element: 1 7 6 8 9 2 3 0
         -----------------
min-heap:                
                  9      
                8 8      
            7 7 7 7       <-- root
         -----------------
max-heap: 1 1 6 6 6       <-- root
              1 1 1     
```

In this case, we balance the heaps by popping the root of min-heap and
pushing it into the max-heap.  Consider the situation where the max-heap
becomes bigger than min-heap by more than one element after we
read `0`.

```asciidoc
 element: 1 7 6 8 9 2 3 0
         -----------------
min-heap:           9 9 9
                8 9 8 8 8
            7 7 7 8 7 7 7 <-- root
         -----------------
max-heap: 1 1 6 6 7 6 6 6 <-- root
              1 1 6 2 3 3
                  1 1 2 2
                      1 1
                        0
```

In this case, we balance the heaps by popping the root of the max-heap
and pushing it into the min-heap.

We compute the median in the following way.  When the max-heap is
larger than the min-heap, the median is the root of the max-heap.
When both heaps are the same size, the median is the average of both
roots.

The run time of the approach is dominated by the repeated insertion &
removal of elements from both heaps.  Each such operation takes
$@O(\text{log}\,n)@$, where $@n@$ is the length of the sequence.  The
count of those operations is linear on the length of the sequence, for
that reason the time complexity of the approach is
$@O(n\,\text{log}\,n)@$.

# Ruby Implementation

We implement our approach in Ruby as follows.

{% highlight ruby %}
#!/usr/bin/env ruby

class BaseHeap
  def initialize
    @data = []
  end

  def push v
    @data.push v
    bubble(@data.length - 1)
  end

  def pop
    last = @data.pop
    return last if @data.empty?
    root = @data[0]
    @data[0] = last
    sink 0
    return root
  end

  def peek
    @data.first
  end

  def size
    @data.length
  end

  def to_s
    inspect
  end

  def inspect
    @data.inspect
  end

  private

  def bubble i
    while 0 < i
      p = parent i
      return if ordered? p, i
      swap p, i
      i = p
    end
  end

  def sink i
    while true
      l = left i
      return if l >= @data.length
      r = right i
      if r >= @data.length || ordered?(l, r)
        pivot = l
      else
        pivot = r
      end
      return if ordered? i, pivot
      swap i, pivot
      i = pivot
    end
  end

  def parent i
    (i - 1) / 2
  end

  def left i
    (i * 2) + 1
  end

  def right i
    left(i) + 1
  end

  def swap i, j
    t = @data[i]
    @data[i] = @data[j]
    @data[j] = t
  end
end

class MinHeap < BaseHeap
  def ordered? i, j
    @data[i] <= @data[j]
  end
end

class MaxHeap < BaseHeap
  def ordered? i, j
    @data[i] >= @data[j]
  end
end

class PrefixMedian
  def initialize
    @left = MaxHeap.new
    @right = MinHeap.new
  end

  def push v
    if @left.size == 0
      @left.push v
      return
    end     
    if @left.peek < v
      @right.push v
    else
      @left.push v
    end
    balance
  end

  def median
    return nil if @left.size == 0
    return @left.peek.to_f if @left.size != @right.size
    return (@left.peek + @right.peek) / 2.0
  end

  private

  def balance
    if @left.size < @right.size
      @left.push @right.pop
    elsif @left.size > @right.size + 1
      @right.push @left.pop
    end
  end
end

while true
  input = readline.strip.split(' ').map(&:to_i) rescue break
  pm = PrefixMedian.new
  input.each_with_index do |v, i|
    pm.push v
    print pm.median
    print ' ' if i < input.length - 1
  end
  puts
end
{% endhighlight %}

{% include subscribe.html %}

{% include share.html %}

{% include comments.html %}
