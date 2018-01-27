---
layout: post
title: Shortest Substring Made of Allowed Letters
date: 2017-04-01
author: Ruslan Ledesma-Garza
edited: 2017-04-02
summary: '
You are given a string and a set of letters.  Your task is to return
any shortest substring that consists of all and only the letters in
the set.
'
---

{{page.summary}}

**Edit 2017-04-02**: The example string did not correspond to the
illustrations. Thanks to [Guillermo
Cruz](https://www.linkedin.com/in/guillermo-cruz-20732948/) for the correction.

Consider string `aabbxbbcaab` and the set of letters `{a,b,c}`.
The only possible answer is `bca` as illustrated in the following diagram.

<img src="/assets/2017.04.01.solution.jpg" alt="Solution" style="width:50%; display: block; margin-left: auto; margin-right: auto;" />

When there is no shortest substring, return the empty string.

**Input.**
The input consists of one or more cases.
Each case consists of a two strings, one on a separate line.
The first string is the string where you will search for a shortest substring.
The second string indicates the characters in the set.
The input is terminated by EOF.  The following is a sample input
file.

{% highlight asciidoc %}
xaabbca
abc
aabxbcbaaccb
abc
aabbccbbabbcaabcbc
abc
{% endhighlight %}

**Output.**
For each input case, print on a single line a shortest substring or the empty string.
The following is the output file that corresponds to the sample input
file.

{% highlight asciidoc %}
bca
cba
bca
{% endhighlight %}

# Approach

We approach the problem by computing a candidate solution for each
prefix and then selecting the shortest.  A candidate solution for a
prefix is the shortest suffix that is made of all and only the allowed
letters.  There may not be a candidate solution for every prefix.  For
example, consider the candidates for string `aabbxbbcaab` in the
following diagram.

<img src="/assets/2017.04.01.candidates.jpg" alt="Candidates" style="width:50%; display: block; margin-left: auto; margin-right: auto;" />

For the prefix that ends in position 8, the candidate solution is the
substring `bca`.  That substring runs from position 6 to position 8.
That prefix is also the solution.
For the prefix that ends in position 7, there is no candidate because
every prefix that includes all allowed letters also includes the
forbidden letter `x`.

We approach the problem of computing candidates by computing
pre-candidates for each prefix and then filtering out those that
either contain a forbidden character or do not contain all allowed
characters.

A pre-candidate for a prefix is the shortest suffix that contains as
many allowed characters as possible. For example, consider the
pre-candidates for string `aabbxbbcaab` in the following diagram.

<img src="/assets/2017.04.01.pre-candidates.jpg" alt="Pre-Candidates" style="width:50%; display: block; margin-left: auto; margin-right: auto;" />

For the prefix that ends in position 8, the pre-candidate is the
substring `bca`.  For the prefix that ends in position 7, the
pre-candidate is the substring that begins at position 1 and ends in
position 7, even though that substring contains the forbidden
character.

# Procedure

To compute the pre-candidates and select the shortest candidate, we
apply the following procedure to each prefix.

For each allowed character, compute the last position within the
prefix.  For example, consider the positions for string `aabbxbbcaab`
in the following diagram.

<img src="/assets/2017.04.01.last.jpg" alt="Last occurrence of allowed letters" style="width:50%; display: block; margin-left: auto; margin-right: auto;" />

The solid vertical line corresponds to forbidden character `x`.  There
is a row for each prefix, each is labeled with the last position of
the prefix.  On each row, the last position of each allowed letter is
indicated by the letter.  For example, for the prefix that ends in
position 8, the last position for `a` is 8, for `b` is 6, and for `c`
is 7.  The positions appear on the row labeled 8.

We determine the pre-candidate for each prefix from the previous
positions.  The leftmost position is the beginning of the
pre-candidate and the end is the last position of the prefix.  In the
previous diagram, the pre-candidate for each row is indicated by a
line that goes from the beginning to the end of the pre-candidate.
For example, the beginning of the precandidate for prefix 4 is
position 1 and the end is position 4.

We filter pre-candidates by applying the following two rules.

1. A pre-candidate has a forbidden character when the last position of
any forbidden character is after the beginning of the pre-candidate.
For example, the pre-candidates that intersect the solid vertical line
in the previous diagram have forbidden character `x` after the beginning
and are thus rejected.
2. A pre-candidate has all allowed characters when the count of last
positions is the same as the count of allowed characters.  For
example, consider the row for the prefix that ends in position 3.
The count of last positions is 2 and the pre-candidate is thus
rejected.

Instead of applying the previous two rules separately, we count the
number of allowed characters that appear after the last forbidden
character and compare that count.  When the count equals the count of
allowed characters, we know that the pre-candidate consists of all and
only the allowed chars and is thus a candidate.

When the pre-candidate turns out to be a candidate, we choose it as
the solution so far when it is shorter that any current solution.

# Implementation

We implement the procedure in Ruby as follows.

{% highlight ruby %}
 1: #!/usr/bin/env ruby
 2: 
 3: def shortest_substring_of_allowed_letters s, allowed
 4:   last = allowed.chars.map { |c| [c, -1] }.to_h
 5:   allowed_count = 0
 6:   min_left = min_right = left = other_last = -1
 7:   min_length = s.length + 1
 8:   s.chars.each_with_index do |c, right|
 9:     if last.has_key? c
10:       allowed_count += 1 if last[c] <= other_last
11:       last[c] = right
12:       while last[s[left]] != left
13:         left += 1
14:       end
15:       length = right - left + 1
16:       if allowed_count == last.size and length < min_length
17:         min_length = length
18:         min_left = left
19:         min_right = right
20:       end
21:     else
22:       other_last = right
23:       allowed_count = 0
24:     end
25:   end
26:   if min_length < s.length + 1
27:     return s[min_left..min_right]
28:   else
29:     return ''
30:   end
31: end
32: 
33: while true
34:   a = readline.strip rescue break
35:   b = readline.strip rescue break
36:   puts shortest_substring_of_allowed_letters a, b
37: end
{% endhighlight %}

The implementation considers prefixes from left to right.

The meaning of the variables is the following.
Hash `last` stores the last position of each allowed character so far.
Index `other_last` stores the last position of any forbidden character
so far.
Counter `allowed_count` stores the count of allowed characters since
any forbidden character.
Index `left` stores the leftmost position in `last`.
Indices `min_left` and `min_right` store the beginning and end of the
solution so far.
Integer `min_length` stores the length of that solution.

By storing the last positions in `last`, the computation of last
positions for a given prefix consists of the assignment on line 11.

The crucial aspect of the implementation is computing the value of
`left` every time the leftmost position changes.  When that happens
the new leftmost position `p` is to the right of `left` and satisfies
the condition that the last position of the character at `p` is `p`.
For that reason, we increment `left` until we satisfy `last[s[left]]
== left`.  For example, consider the computation of `left` for the
prefix that ends in position 8 illustrated in the following diagram.

<img src="/assets/2017.04.01.computation.of.left.jpg" alt="Computation of `left`" style="width:50%; display: block; margin-left: auto; margin-right: auto;" />

Because the last position of `a` changes, we move `left` to the right
until we reach the last position of `b`.

{% include subscribe.html %}

{% include share.html %}

{% include comments.html %}
