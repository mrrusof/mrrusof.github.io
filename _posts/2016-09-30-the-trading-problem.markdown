---
layout: post
title: .
date: 2016-09-29
author: Ruslan Ledesma-Garza
summary: something something
---

# Problem

The scenario is the following.

n: 1 2 1 5 2 6 3
i: 0 1 2 3 4 5 6
-----------------
x: 1
y: 6
a: 5

max { n[i] < n[j] | i < j }

1. A solution to the problem consists of a pair of elements $@x, y@$.
2. The pair of elements is such that element $@x@$ appears before
   elements $@y@$.
3. The pair of elements is such that there is no other pair $@u, v@$
   that satisfies condition 2 and is such that $@y - x < v - u@$

# Solution

# Implementation

{%highlight c%}
int main() {
  return 0;
}
{%endhighlight%}

