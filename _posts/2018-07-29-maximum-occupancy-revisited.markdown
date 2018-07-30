---
layout: post
title: Maximum Occupancy Revisited
date: 2018-07-29
author: Ruslan Ledesma-Garza
summary: '
We revisit problem Maximum Occupancy and give a simpler solution.
'
---

{{page.summary}}  You can find our previous solution [here](/2017/03/07/maximum-occupancy.html).

# Problem Statement

You work for a chain of hotels. The central management asks you to
make a report that includes some metrics for each month and hotel.
The report must include the maximum occupancy.  Given that the only
data you have that is related to occupancy is a list of room
reservations for each month and hotel, how do you obtain the maximum
occupancy?

For a given hotel and day, occupancy is the number of rooms that are
occupied on that day.  We consider that a room is occupied when the
reservation is paid.  After a room is paid, we do not make the room
available even if guests do not show up on the first date of their
reservation.

Each reservation that you have is fully paid.  Each reservation
consists of a check-in date and a check-out date.  Each reservation
corresponds to a single room.  For that reason, two overlapping
reservations correspond to different rooms.  Given a reservation, the
corresponding room is occupied on all dates of the reservation except
the check-out date.

Consider the following example.  Suppose that on February 2017 there
were only 5 reservations for our hotel in
[Cancun](https://en.wikipedia.org/wiki/Canc%C3%BAn).

{% highlight asciidoc %}
Reservation   Check-in    Check-out
1             2017/02/01  2017/02/03
2             2017/02/03  2017/02/05
3             2017/02/05  2017/02/07
4             2017/02/05  2017/02/08
5             2017/02/05  2017/02/09
6             2017/02/10  2017/02/11
{% endhighlight %}

The maximum occupancy is 3 and it happened on February 5 and 6.

**Input.**
The input file consists of one or more cases.  Each case begins with a
positive number `n` on a line of its own.  Following `n`, there are
`n` reservations corresponding to the case, each on a separate line.
Each reservation consists of two numbers separated by one or more
spaces.  The first number of a given reservation corresponds to the
day of the month where a check-in happened.  The second number
corresponds to the check-out day.  No reservation crosses the
boundaries of any month.  The input file is terminated by an EOF
character.  Consider the following example.

{% highlight asciidoc %}
6
1 3
3 5
5 7
5 8
5 9
10 11
{% endhighlight %}

**Output.**
The output file consists of an output line for each case.  Each output
line consists of a single integer, the maximum occupancy for the case.
For example, the following output corresponds to the previous example
input.

{% highlight asciidoc %}
3
{% endhighlight %}

# Approach

We illustrate our approach by example.  Consider the following
timeline of the reservations given by the example in the problem
statement.

{% highlight asciidoc %}
   1   2   3   4   5   6   7   8   9   10  11  12  ... <-- Day of month

1: i       o
2:         i       o
3:                 i       o
4:                 i           o
5:                 i               o
6:                                     i   o
^
|
 \
  Reservation
{% endhighlight %}

We compute the occupancy on each day in the following way.  We start
with an occupancy of zero.  Imagine that we sweep a line over the
diagram from left to right.  Each time we step over a day, we
calculate the occupancy on the day by adding the delta occupancy for
the day.  The delta occupancy for the a given day is the difference
between the count of `i`'s and the count of `o`'s for that day.
Consider the occupancy for each day in the example.

{% highlight asciidoc %}
   1   2   3   4   5   6   7   8   9   10  11  12  ... <-- Day of month

   1   0   0   0   2   0  -1  -1  -1   1  -1   0   <-- Delta Occupancy
   1   1   1   1   3   3   2   1   0   1   0   0   <-- Occupancy

1: i       o
2:         i       o
3:                 i       o
4:                 i           o
5:                 i               o
6:                                     i   o
^
|
 \
  Reservation
{% endhighlight %}

The result for the example is 3, which is the maximum among the
occupancies in the previous diagram.

# Ruby Implementation

We implement our approach in three steps.  We compute the delta
occupancy for each day, then the occupancy for each day, and finally
the occupancy for all the reservations.  Given that only days that
have a check-in or check-out may have an effect on occupancy, we
compute occupancies only for those.

We compute the deltas by means of a dictionary in the following way.
We take as input a list of reservations consisting of a pair of
integers `day_in, day_out`.  For each reservation, we increase the
delta for `day_in` and decrease the delta for `day_out`.  We output a
list of deltas sorted by day.  We implement the computation of deltas
in the following method `sorted_deltas`.  The run time of
the implementation is dominated by the sorting and belongs to class
$@O(r\,\text{log}(r))@$, where $@r@$ is the count of reservations.

{% highlight ruby %}
def sorted_deltas reservations
  deltas = Hash.new
  reservations.each { |day_in, day_out|
    deltas[day_in] ||= 0
    deltas[day_in] += 1
    deltas[day_out] ||= 0
    deltas[day_out] -= 1
  }
  return deltas.to_a.sort { |a, b| a.first <=> b.first }.map { |day, delta| delta }
end
{% endhighlight %}

We compute each occupancy by adding the corresponding delta to the
previous occupancy.  We start with occupancy zero.  We implement the
computation of occupancies in the following method `occupancies`.  The
run time belongs to class $@O(d)$@, where $@d@$ is the count of
deltas.

{% highlight ruby %}
def occupancies sorted_deltas
  occ = 0
  return sorted_deltas.map { |delta| occ += delta }
end
{% endhighlight %}

Finally, the occupancy for all the reservations is the maximum
among the occupancies for each day.

The following is our Ruby implementation of the approach.  The run
time of the implementation is bounded by the call to `sorted_deltas`
and belongs to class $@O(r\,\text{log}(r))@$, where $@r@$ is the count of
reservations.

{% highlight ruby %}
#!/usr/bin/env ruby

def sorted_deltas reservations
  deltas = Hash.new
  reservations.each { |day_in, day_out|
    deltas[day_in] ||= 0
    deltas[day_in] += 1
    deltas[day_out] ||= 0
    deltas[day_out] -= 1
  }
  return deltas.to_a.sort { |a, b| a.first <=> b.first }.map { |day, delta| delta }
end

def occupancies sorted_deltas
  occ = 0
  return sorted_deltas.map { |delta| occ += delta }
end

def maximum_occupancy reservations
  deltas = sorted_deltas reservations
  occupancies_for_each_day = occupancies deltas
  return occupancies_for_each_day.max
end

n = readline.strip.to_i
reservations = Array.new
while n > 0
  r = readline.strip.split(' ').map(&:to_i) rescue break
  reservations << r
  n -= 1
end
puts maximum_occupancy reservations
{% endhighlight %}

{% include subscribe.html %}

{% include share.html %}

{% include comments.html %}
