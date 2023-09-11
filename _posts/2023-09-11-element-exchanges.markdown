---
layout: post
title: Element Exchanges
date: 2023-09-11
author: Ruslan Ledesma
summary: '
Given two multisets of integers, give the minimum cost of exchanging
elements so that both multisets become equal.  The cost of exchanging
$@N@$ and $@M@$ is $@max(N, M)@$.
'
---

{{page.summary}}

<img src="/assets/business-card-exchange.jpg" />

**Input.**
The input consists of two sequences of integers separated by spaces,
one sequence per line.  Each sequence corresponds to separate
multiset.  Consider the following example input.

```asciidoc
1 4 4 1 6 6
2 2 0 0 3 3
```

**Output.**
The output consists of a single integer, the solution.  The integer `-1`
indicates that the input multisets cannot be made equal.  The
following output corresponds to the example input.

```asciidoc
11
```

# Approach

We check that there is a solution for the given input and then we
construct a minimal sequence of exchanges by pairing the minimum
elements to exchange for each step in the sequence.

The check consists of two conditions.  The first condition is that
both multisets are of the same cardinality.  The reason is that
exchanging elements preserves the cardinality of each multiset, so
when two multisets are of different cardinalities, no number of
exchanges will equate their cardinalities.  The second condition is
that for each element of either multiset, the sum of its
multiplicities must be even.  The reason is that it is impossible to
evenly distribute between the two multisets an odd number of
occurrences of an given element.  Consider the following two multisets
X and Y.  The element 2 has multiplicity 0 in X and 1 in Y.  It is
impossible to give both multisets an instance of element 2.

```asciidoc
X  Y
----
1  2
1  1
1
```

We illustrate the construction of a minimal sequence of exchanges by
example.  Consider the following multisets A and B.

```asciidoc
A  B
----
1  3
4  3
4  0
1  0
6  3
6  3
```

The elements to exchange from each multiset are the following.

```asciidoc
A->  <-B
--------
1      0
4      3
6      3
```

One minimal sequence of exchanges is `exchange(1, 0)`, `exchange(4,
3)`, `exchange(6, 3)` because the total cost of the sequence is 11,
the solution for this example.  The cost of the sequence is calculated
as `max(1, 0) + max(4, 3) + max(6, 3)`.  There are other ways to pair
the elements to exchange so that the total cost is 11 but pairing the
elements in ascending order like we did always gives a minimal
sequence of exchanges.  The reason is the following.

Imagine that pairing elements in ascending order did not give a
minimal sequence of exchanges, meaning that at least one of the
exchanges in the sequence $@e_1, e_2, ..., e_i, ..., e_n@$ could be
made cheaper, let's call this exchange $@e_i@$.  The pair for exchange
$@e_i@$ consists of one of the smallest elements in `A->` that we have
not exchanged so far, let's call it $@a_i@$, and one of the smallest
elements in `<-B` that we have not exchanged so far, let's call it
$@b_i@$.  The cost of the exchange $@e_i@$ is $@max(a_i, b_i)@$.  We
could make this exchange cheaper if the bigger of $@a_i@$ and $@b_i@$
could be substituted by a smaller member of its corresponding multiset
`A->` or `<-B`.  However we have already used all the elements of
`A->` that are smaller than $@a_i@$ and the same goes for $@b_i@$, so
it is impossible to make the cost of $@e_i@$ cheaper.

We determine how many elements to exchange from each side by analyzing
the multiplicities of `A` and `B` in the following way.  Consider the
multiplicities of `A` and `B` as follows.

```asciidoc
Element    Count_A    Count_B
-----------------------------
0             0          2
1             2          0
3             0          4
4             2          0
6             2          0
```

For each element, if the difference in multiplicities is negative, we
we must move instances to the left, if possitive we move to the right,
otherwise we do nothing.  For our example, the directions to move
instances are the following.

```asciidoc
Element    Count_A    Count_B   Delta    Move_Direction
-------------------------------------------------------
0             0          2       -2          <-
1             2          0        2          ->
3             0          4       -4          <-
4             2          0        2          ->
6             2          0        2          ->
```

The number of instances to move in each direction to balance each
element is half of the multiplicity difference (i.e. `Abs(Delta /
2)`).  For our example the number of instances to move is as follows.

```asciidoc
Element    Count_A    Count_B   Delta    Move_Direction   Move_Count
--------------------------------------------------------------------
0             0          2       -2          <-               1
1             2          0        2          ->               1
3             0          4       -4          <-               2
4             2          0        2          ->               1
6             2          0        2          ->               1
```


The time complexity of the approach $@O(n\,\text{log}\,n)@$ where $@n@$
is the length of the longest of the two input multisets `A` and `B`.
The time complexity of the initial check and the construction of
multisets `A->` and `<-B` is $@O(n)@$ because these steps amount to
repeated iteration over `A` and `B` and insertion of values in
corresponding dictionaries.  The pairing of the elements of `A->` and
`<-B` in ascending order amounts to sorting the elements of `A->` and
`<-B`, so the time complexity of the pairing is
$@O(n\,\text{log}\,n)@$, which dominates the other parts of the
approach.


# Ruby Implementation

We implement our approach in Ruby as follows.

{% highlight ruby %}
#!/usr/bin/env ruby

def multiplicities a, b
  count_a = (a + b).map { |n| [n, 0] }.to_h
  count_b = (a + b).map { |n| [n, 0] }.to_h

  a.each { |n| count_a[n] += 1 }
  b.each { |n| count_b[n] += 1 }

  return [count_a, count_b]
end

def can_be_made_equal count_a, count_b
  return count_a.values.sum == count_b.values.sum &&
    count_a.all? { |n, c| (c + count_b[n]) % 2 == 0 }
end

def elements_to_exchange count_a, count_b
  delta = count_a.map { |n, c| [n, c - count_b[n]] }.to_h

  to_exchange_a = []
  to_exchange_b = []

  delta.each do |n, d|
    move_count = d.abs / 2
    if d < 0
      move_count.times { to_exchange_a << n }
    elsif 0 < d
      move_count.times { to_exchange_b << n }
    end
  end

  return [to_exchange_a, to_exchange_b]
end

def min_exchange_sequence to_exchange_a, to_exchange_b
  return to_exchange_a.sort.zip to_exchange_b.sort
end

def sequence_cost seq
  return seq.map(&:max).sum
end

def min_exchange_cost a, b
  count_a, count_b = multiplicities a, b
  return -1 unless can_be_made_equal count_a, count_b

  to_exchange_a, to_exchange_b = elements_to_exchange count_a, count_b
  seq = min_exchange_sequence to_exchange_a, to_exchange_b
  return sequence_cost seq
end

a = readline.strip.split(' ').map(&:to_i)
b = readline.strip.split(' ').map(&:to_i)
puts min_exchange_cost a, b
{% endhighlight %}

{% include share.html %}

{% include comments.html %}
