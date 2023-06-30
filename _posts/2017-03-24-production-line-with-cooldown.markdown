---
layout: post
title: Production Line With Cooldown
date: 2017-03-24
author: Ruslan Ledesma-Garza
summary: '
You are given a work list that must be processed in the
given order in such a way that no two identical items are processed
within a given cooldown period.  Compute the amount of time that
takes processing the work list.
'
---

![Delicious Production Line](/assets/2017-03-24.doughnut-production-line.jpg)
<small>_Doughnut Production Line by [Kozuch](https://commons.wikimedia.org/wiki/User:Kozuch), licensed under the [Creative Commons Attribution-Share Alike 2.0 Generic](https://creativecommons.org/licenses/by-sa/2.0/deed.en) license._</small>

{{page.summary}}

Processing each item takes one time unit and the cooldown period is
given in time units.  When a given item is within the cooldown of
another identical item, do nothing until the cooldown period elapses.

Consider work list `AABACBC` and cooldown period of 2 time units.  The
processing of the work list is the following.

{% highlight asciidoc %}
 work list:  A        A  B     A  C  B     C
processing:  A  _  _  A  B  _  A  C  B  _  C
      time:  1  2  3  4  5  6  7  8  9 10 11
{% endhighlight %}

**Input.**
The input consists of one or more cases.
Each case consists of two lines.
The first line of a case consists of a single integer, the time units in the cooldown period.
The second line of a case consists of a string, the work list.
The input is terminated by EOF.  The following is a sample input
file.

{% highlight asciidoc %}
2
AABACBC
1
AACA
3
ABAABCCB
{% endhighlight %}

**Output.**
For each input case, print on a single line the time units it takes to process the work list.
The following is the output file that corresponds to the sample input
file.

{% highlight asciidoc %}
11
5
16
{% endhighlight %}

# Solution

Given a work list of length `n`, the proposed solution takes `O(n)`
time and `O(1)` space.

Consider the processing of work list `AABAB` with cooldown period
of 2 time units.

{% highlight asciidoc %}
 work list:  A        A  B     A  B 
processing:  A  _  _  A  B  _  A  B 
      time:  1  2  3  4  5  6  7  8 
{% endhighlight %}

The cooldown period indicates the minimum separation between any two
identical items.  When two identical items are not far enough, we
introduce the minimum amount of idle time slots possible to satisfy
the cooldown period.  For example, between the first and second `A` we
introduce two idle time slots because there is nothing in between
them.  Another example, for the second and third `A`, we introduce one
idle time slot because there is already the time slot of `B` between
the two `A`.  Another example, by the time we start processing the second `B`,
the distance to the first `B` is already more than the cooldown
period, so we do not introduce idle time.

When two identical work items are within the cooldown time, the amount
of idle time slots we introduce is the difference between the cooldown
period and the current distance between the items. We introduce the
idle time before the second work item.  For example, we introduce one
idle slot before the third `A` because the distance between the time
when we finish processing the first `B` and the time we finish
processing the first `A` is 1 as illustrated in the following
diagram.

{% highlight asciidoc %}
 work list:  A        A  B     A  B 
processing:  A  _  _  A  B  _  A  B 
      time:  1  2  3  4  5  6  7  8 
                         |--|
                           \
                         distance
{% endhighlight %}

To compute the distance between one item and the previous identical
item, we store in a hash the current time for the current element as
illustrated in the following diagram.

{% highlight asciidoc %}
 work list:   A              A    B         A    B 
processing:   A    _    _    A    B    _    A    B 
      time:   1    2    3    4    5    6    7    8 

      hash:  A=1  A=1  A=1  A=4  A=4  A=4  A=7  A=7
                                 B=5  B=5  B=5  B=8
{% endhighlight %}

Given hash `last`, current work item `w`, and current time `time`, the
computation of the distance is given by expression `time - last[w]`.

# Ruby implementation

{% highlight ruby %}
#!/usr/bin/env ruby

def process wl, cd
  time = 0
  last = Hash.new
  wl.chars.each do |w|
    if last[w] != nil and time - last[w] <= cd
      time += cd - (time - last[w])
    end
    time += 1
    last[w] = time
  end
  return time
end

while true
  cd = readline.strip.to_i rescue break
  wl = readline.strip rescue break
  puts process wl, cd
end
{% endhighlight %}


{% include share.html %}

{% include comments.html %}
