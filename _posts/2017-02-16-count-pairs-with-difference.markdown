---
layout: post
title: Count Pairs with Difference
date: 2017-02-16
author: Ruslan Ledesma-Garza
summary: "
Given a set of integers, count the pairs where the difference equals
a given integer `d`.
"
---

{{page.summary}}

Consider the set of integers `1 3 5 2 4` and `d` = 2. The count of
pairs is 3 and the pairs are `1, 3`, `2, 4`, and `3, 5`.

**Input.**
The input file consists of one or more cases consisting of a pair of
lines. The first line of each case consists of a pair of integers
separated by a single space: the length `l` of a subsequent list of
integers and the difference `d`.  The second line of each case
consists of `l` integers separated by a single space. None of the
integers is repeated in the list. The input file is terminated by an
EOF character. Consider the following example.

{% highlight asciidoc %}
5 2
1 3 5 2 4
{% endhighlight %}

**Output.**
The output file consists of the count of pairs for each input
case. The following output corresponds to the previous example input.

{% highlight asciidoc %}
3
{% endhighlight %}

# Solution

The proposed solution is `O(n log n)`.

To count the pairs in `1 3 5 2 4` that are at difference 2, consider
the sorted set and the three pairs in the set.

{% highlight asciidoc %}
1 2 3 4 5
A   A
  B   B
    C   C
{% endhighlight %}

To count those pairs, consider each integer `n` from left to right and
look for `n - d` to the left of `n`. For example, consider the way we
find pair `A`.  For the first integer `1`, there is nothing on the left,
so there is no pair.

{% highlight asciidoc %}
1 2 3 4 5
^
{% endhighlight %}

For the second integer `2`, only integer `1` is to the left. Because
`0` is not to the left, there is no pair.

{% highlight asciidoc %}
1 2 3 4 5
. ^
{% endhighlight %}

For the third integer `3`, integers `1` and `2` are to the
left. Because `1` is to the left, we find our first pair, which
we tag with label `A`.

{% highlight asciidoc %}
1 2 3 4 5
.   ^
A   A
{% endhighlight %}

When we look for a corresponding integer to the left, it suffices to
consider the integers between the last integer to the left that we
considered and the current integer (marked with `^`).  For example,
consider the way we find pair `B` after we found `A`.  We consider the
fourth integer `4` and we look for `2` to the left.  Because we
already considered `1`, we look for `2` between `1` and `4`. When we
find `2`, we stop there and find our second pair `B`.

{% highlight asciidoc %}
1 2 3 4 5
  .   ^
A   A
  B   B
{% endhighlight %}

Another example that it suffices to consider integers between the last
integer to the left and the current integer is when we find pair `C`.
In this case, the current integer is `5` and we look for `3` between
integers `2` and `5`. When we find `3` we find pair `C`.

{% highlight asciidoc %}
1 2 3 4 5
    .   ^
A   A
  B   B
    C   C
{% endhighlight %}

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def count_pairs nn, d # O(n log n + n) = O(n log n)
  nn.sort! # O(n log n)
  count = 0
  c = 1
  p = 0
  while c < nn.length # O(n + n) = O(n)
    # move candidate position p
    while nn[p] < nn[c] - d # O(n)
      p += 1
    end
    # do positions p and c make a pair?
    if nn[p] == nn[c] - d
      count += 1
    end
    c += 1
  end
  return count
end

while true
  _, d = readline.chomp.split(' ').map(&:to_i) rescue break
  nn = readline.chomp.split(' ').map(&:to_i)
  puts count_pairs nn, d
end
{% endhighlight %}


{% include subscribe.html %}

{% include share.html %}

{% include comments.html %}
