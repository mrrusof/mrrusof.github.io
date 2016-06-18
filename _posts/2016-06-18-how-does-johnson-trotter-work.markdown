---
layout: post
title: How does Johnson-Trotter algorithm work?
date: 2016-06-18
author: Ruslan Ledesma-Garza
summary: Something something
---

If you are looking for an explanation of how Johnson-Trotter algorithm constructs all permutations, keep reading.

- Recursive formulation
- Iterative formulation



# How does Johnson-Trotter algorithm work?


Move element 1 from left to right.
Take a step for elements 2 and 3.
That is, move element 2 from left to right.
Move element 1 from right to left.

{% highlight asciidoc %}
 0 |  |  | 123
 1 |--|  | 213 L = 3
 2 |  |--| 231 L = 3
 3 |--|  | 321 L = 2
 4 |  |--| 312 L = 3
 5 |--|  | 132 L = 3
{% endhighlight %}

{% highlight asciidoc %}
 0 |  |  |  | 1234        -->   0 |  |  |  | 1234
                                1 |--|  |  | 2134 L = 4
                                2 |  |--|  | 2314 L = 4
                                3 |  |  |--| 2341 L = 4
 1 |--|  |  | 3241 L = 3  -->   4 |--|  |  | 3241 L = 3
                                5 |  |  |--| 3214 L = 4
                                6 |  |--|  | 3124 L = 4
                                7 |--|  |  | 1324 L = 4
 2 |  |  |--| 1342 L = 3  -->   8 |  |  |--| 1342 L = 3
                                9 |--|  |  | 3142 L = 4
                               10 |  |--|  | 3412 L = 4
                               11 |  |  |--| 3421 L = 4
 3 |--|  |  | 4321 L = 2  -->  12 |--|  |  | 4321 L = 2
                               13 |  |  |--| 4312 L = 4
                               14 |  |--|  | 4132 L = 4
                               15 |--|  |  | 1432 L = 4
 4 |  |  |--| 1423 L = 3  -->  16 |  |  |--| 1423 L = 3
                               17 |--|  |  | 4123 L = 4
                               18 |  |--|  | 4213 L = 4
                               19 |  |  |--| 4231 L = 4
 5 |--|  |  | 2431 L = 3  -->  20 |--|  |  | 2431 L = 3
                               21 |  |  |--| 2413 L = 4
                               22 |  |--|  | 2143 L = 4
                               23 |--|  |  | 1243 L = 4
{% endhighlight %}


{% highlight asciidoc %}
JohnsonTrotter(S, L, N)
 10: o := 0
 20: IF L < N THEN
 30:  o := JohnsonTrotter(S, L, N)
 40: IF D[L] = forwards
 50:  FOR i := 1 TO L - 1
 60:    swap_with_next(S, o + i)
 70:    IF L < N THEN
 80:      o := JohnsonTrotter(S, L, N)
 90:   D[L] := backwards
110:   RETURN o
120: ELSE
130:   FOR i := L - 1 DOWN TO 1
140:     swap_with_next(S, o + i)
150:     IF L < N THEN
160:       o := JohnsonTrotter(S, L, N)  
170:   D[L] := forwards
180:   RETURN o + 1
{% endhighlight %}



{% highlight c %}
#include <stdio.h>

#define MAX_LEN 30

#define Ss(s) scanf("%s", s)


int count = 1;
void print_swap(int i, int n) {
  int j;
  printf("%4d ", count++);
  for(j = 0; j < n - 1; j++)
    if(j == i)
      printf("|--");
    else
      printf("|  ");
  printf("|");
}

void swap_w_next(char *s, int i) {
  char t = s[i];
  s[i] = s[i + 1];
  s[i + 1] = t;
}

int jt(char *s, int l, int n, int *d) {
  int i;
  int o = 0;
  if(l < n)
    o = jt(s, l + 1, n, d);
  if(d[l])
    for(i = 0; i < l - 1; i++) {
      swap_w_next(s, o + i);
      print_swap(o + i, n);
      printf(" %s L = %2d\n", s, l);
      if(l < n)
	o = jt(s, l + 1, n, d);
    }
  else {
    for(i = l - 2; 0 <= i; i--) {
      swap_w_next(s, o + i);
      print_swap(o + i, n);
      printf(" %s L = %2d\n", s, l);
      if(l < n)
	o = jt(s, l + 1, n, d);
    }
    o++;
  }
  d[l] = !d[l];
  return o;
}

void johnson_trotter(char *s, int n) {
  int i;
  int d[MAX_LEN + 1];
  for(i = 0; i < MAX_LEN + 1; i++)
    d[i] = 1;
  print_swap(-1, n);
  printf(" %s\n", s);
  jt(s, 2, n, d);
}

int main() {
  char s[MAX_LEN];
  int slen;
  Ss(s);
  for(slen = 0; s[slen] != '\0'; slen++);
  johnson_trotter(s, slen);
  return 0;
}
{% endhighlight %}

{% highlight c %}

{% endhighlight %}



# References


<dl>
  <dt id="johnson">
    Johnson
  </dt>
  <dd>
    something
  </dd>
  <dt id="trotter">
    Trotter
  </dt>
  <dd>
    something
  </dd>
</dl>
