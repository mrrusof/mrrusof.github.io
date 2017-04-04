---
layout: post
title: The Special Offer Problem
date: 2016-08-11
author: Ruslan Ledesma-Garza
summary: 'A problem for the unsuspecting programmer.
Makes for a great interview question.'
---

Here is a problem for the unsuspecting programmer.
Makes for a great interview question.


# Problem

You are developing an online store with a shopping cart feature.
Right before checkout, the system must show the customer a detailed bill.
The bill includes one special offer when some special offer
applies to the items in the cart.  When more than one special offer
applies, the system selects the offer that gives the highest discount.

Your task is to determine if a given special offer applies to the
items in the cart.  You are given the identification number of each
item in the cart and the combination of items that correspond to the
special offer.  For example, consider cart items `3, 1, 4, 2`.
Special offer combination `2, 3` applies to the cart items.
Combination `1, 5` does not apply to the cart items because item `5`
is not included in the cart.


# Input

The input consists of a number $@1 \le i \le 10000@$ followed by $@i@$
cases.  Each case consists of a list of items and a special offer
combination.  The list of items consists of a sequence of integers
separated by spaces and ended by a newline.  The list of items is
preceded by the count of items $@0 \le n \le 100000@$.  The special
offer combination has the same format and is preceded by its
corresponding count $@0 \le m \le 100000@$.  Consider the following
example.

{% highlight asciidoc %}
2
4
3 1 4 2
2
2 3
4
3 1 4 2
2
1 5
{% endhighlight %}


# Output

For each case in the input, you must output the word `yes` on a line
by itself when the special offer applies to the cart, otherwise you
should ouput `no`. The following output corresponds to the example input.


{% highlight asciidoc %}
yes
no
{% endhighlight %}


# Solution

The problem asks that we check [multiset
inclusion](https://en.wikipedia.org/wiki/Multiset#Multiplicity_function)
for each case.
Consider the following case.

{% highlight asciidoc %}
5
1 1 2 3 4
4
1 1 2 4
{% endhighlight %}

For the case, the special offer applies to the cart.  The reason is
that multiset `{1, 1, 2, 4}` is included in `{1, 1, 2, 3, 4}` as
illustrated in the following diagram.

{% highlight asciidoc %}
1 1 2 3 4
^ ^ ^   ^
| | |  /
1 1 2 4
{% endhighlight %}

The following algorithm checks multiset inclusion on given sorted
sequences for cart `C` and special offer `S`.  The algorithm returns
true when `S` is subset of `C` and false otherwise.

{% highlight asciidoc %}
CHECK(C, S)
 10: IF length of C < length S
 20:   RETURN false
 30: i := 0
 40: j := 0
 50: WHILE j < length of S
 60:   IF C[i] < S[j]
 70:     i := i + 1
 80:   ELSE IF C[i] = S[j]
 90:     i := i + 1
100:     j := j + 1
110:   ELSE
120:     RETURN false
130: RETURN true
{% endhighlight %}

The input sequences may not be sorted.  Sort the input before applying
algorithm `CHECK`.

The end-to-end solution for a given case consists of the following
steps.

- Sort the $@n@$ cart items `C`. Sorting by [merge
  sort](https://en.wikipedia.org/wiki/Merge_sort) takes $@O(n \log
  n)@$ steps.
- Sort the $@m@$ special offer items `S`. Sorting by merge sort takes
  $@O(m \log m)@$ steps.
- Apply algorithm `CHECK`. Algorithm `CHECK` takes $@O(m)@$ steps.

The worst case execution time of the end-to-end
method is $@O(n \log n)@$ because $@n \ge m@$.

# Implementation

The following C program implements the solution.

{% highlight c %}
#include <stdio.h>

#define Si(i) scanf("%d", &i)
#define MAXLEN 100000
#define DEBUG 1

int t[MAXLEN];
void merge_sort(int *a, int n) {
  int m = n / 2;
  int i = 0;
  int j = m;
  int k = 0;
  if(n <= 1) return;
  merge_sort(a, m);
  merge_sort(a + m, n - m);
  while(k < n)
    if(i < m && j < n)
      if(a[i] <= a[j])
        t[k++] = a[i++];
      else
        t[k++] = a[j++];
    else if(i < m)
      t[k++] = a[i++];
    else
      t[k++] = a[j++];
  for(i = 0; i < n; i++)
    a[i] = t[i];
}

int check(int *C, int n, int *S, int m) {
  if(n < m)
    return 0;
  int i = 0;
  int j = 0;
  while(j < m)
    if(C[i] < S[j])
      i++;
    else if(C[i] == S[j]) {
      i++;
      j++;
    } else // C[i] > S[j]
      return 0;
  return 1;
}

void print(int *a, int n) {
  int i;
  for(i = 0; i < n - 1; i++)
    printf("%d ", a[i]);
  printf("%d\n", a[n-1]);
}

int main() {
  int i;
  int j;
  int n;
  int m;
  int C[MAXLEN];
  int S[MAXLEN];
  Si(i);
  while(i-- > 0) {
    Si(n);
    for(j = 0; j < n; j++)
      Si(C[j]);
    if(DEBUG) {
      printf("C: ");
      print(C, n);
    }
    merge_sort(C, n);
    if(DEBUG) {
      printf("C: ");
      print(C, n);
    }
    Si(m);
    for(j = 0; j < m; j++)
      Si(S[j]);
    if(DEBUG) {
      printf("S: ");
      print(S, m);
    }
    merge_sort(S, m);
    if(DEBUG) {
      printf("S: ");
      print(S, m);
    }
    if(check(C, n, S, m))
      printf("yes\n");
    else
      printf("no\n");
  }
  return 0;
}
{% endhighlight %}

{% include subscribe.html %}
{% include share.html %}

{% include comments.html %}
