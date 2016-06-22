---
layout: post
title: Yes, yes, it is known, but how does the Johnson-Trotter algorithm work?
date: 2016-06-18
author: Ruslan Ledesma-Garza
summary: Something something
---

Q: What is the Johnson-Trotter algorithm?

If you are looking for an explanation of how Steinhaus-Johnson-Trotter algorithm constructs all permutations, keep reading.

There is a recursive approach and an iterative approach to generating the list of permutations.

- Recursive algorithm here is not found anywhere else.
- Iterative algorithm here is found in Sedgewick and Johnson.
  - Is my implementation efficient?


[Sedgewick] explains his interpretation of Johnson's algorithm.

- Sedgewick's interpretation accumulates offset $@x@$ instead of computing it from counters $@N@$.
- He explains his algorithm in two steps.
  - He explains how to determine the element to transpose in each permutations of the Johnson-Trotter sequence.
  - He explains how to compute direction and offset for the element. From direction we get relative position. From offset we get absolute position.


[Johnson](#johnson) formulated two rules that explain how to obtain from a permutation and a state another permutation by swapping two adjacent elements.

- Rules in terms of marks:
  - For largest mark `m` such that `T(m)` exists, apply exchange `T(m)`.
  - Flip direction of marks greater than `m`.
- Method is the following.
  - Set all directions to the left.
  - Apply the rules until there is no `m` such that `T(m)` exists.
- We apply `T(m)` only when all larger marks are at the extreme left or right.
- The method stops at permutation `2, 1, 3, 4, 5, 6, ..., n`.
- [Levitin] says that rules ARE Johnson's algorithm, which is not accurate.
  - [Bogomolny] says the same.
- Rules in terms of positions:
  - For permutation $@P_N@$, index $@N@$ corresponds to a configuration of counters $@(d_2, d_3, ..., d_{n-1}, d_n)@$.
  - $@a_N(k)@$ is the left-hand position of the pair of elements to exchange.
  - $@b_N(k)@$ is the offset of the pair to exchange. The offset is given by the number of elements greater than $@k@$ to the left of $@P_N@$.
  - Position and offset are determined by index $@N@$ as opposed to bookkeeping position and direction of each element.
  - Sedgewick accumulates offset $@x@$ instead of computing offset $@b_N(k)@$ from $@N@$.
  
[Trotter](#trotter) formulated an algorithm that creates the same list of permutations than Johnson.

- Guenter Rote says
  - "The excuse for adding PERM to the growing pile of permutation
    generators is that, at the expense of some extra global storage,
    it cuts the manipulation of x to the theoretical minimum of n!
    transpositions, and appears to offer an advantage in speed. It
    also has the (probably useless) property that the permutations
    it generates are alternately odd and even;"
- You can find Trotter's algorithm in CACM August 1962.

Related work

- Wikipedia does not give pseudocode.
- [Yorgey](#yorgey) does not give pseudocode.
- [Yorgey](#yorgey) explains by example Johnson-Trotter sequences.
- [Sedgewick](#sedgewick) presents an iterative algorithm that constructs Johnson-Trotter lists of permutations.
- [Levitin] says that element `m` is mobile when `T(m)` exists.
- Formulations that are not efficient?
  - [Levitin] explains a formulation based in search that is not efficient?
  - [Bogomolny](#bogomolny) presents the algorithm that [Levitin] presents.


For input sequence `1234`, the corresponding list of permutations is the following.

{% highlight asciidoc %}
 0 |  |  |  | 1234
 1 |  |  |--| 1243
 2 |  |--|  | 1423
 3 |--|  |  | 4123
 4 |  |  |--| 4132
 5 |--|  |  | 1432
 6 |  |--|  | 1342
 7 |  |  |--| 1324
 8 |--|  |  | 3124
 9 |  |  |--| 3142
10 |  |--|  | 3412
11 |--|  |  | 4312
12 |  |  |--| 4321
13 |--|  |  | 3421
14 |  |--|  | 3241
15 |  |  |--| 3214
16 |--|  |  | 2314
17 |  |  |--| 2341
18 |  |--|  | 2431
19 |--|  |  | 4231
20 |  |  |--| 4213
21 |--|  |  | 2413
22 |  |--|  | 2143
23 |  |  |--| 2134
{% endhighlight %}



The list of permutations for `1234` corresponds to the list of permutations for `123` in the following way.

{% highlight asciidoc %}
 0 |  |  |  | 1234
 1 |  |  |--| 1243		       		       
 2 |  |--|  | 1423		       		       
 3 |--|  |  | 4123		       		       
 4 |  |  |--| 4132  -->   1 |--|  | 132
 5 |--|  |  | 1432		       		       
 6 |  |--|  | 1342		       		       
 7 |  |  |--| 1324		       		       
 8 |--|  |  | 3124  -->   2 |  |--| 312
 9 |  |  |--| 3142		       		       
10 |  |--|  | 3412		       		       
11 |--|  |  | 4312		       		       
12 |  |  |--| 4321  -->   3 |--|  | 321  -->   1 |--| 21
13 |--|  |  | 3421		       		       
14 |  |--|  | 3241		       		       
15 |  |  |--| 3214		       		       
16 |--|  |  | 2314  -->   4 |  |--| 231
17 |  |  |--| 2341		       		       
18 |  |--|  | 2431		       		       
19 |--|  |  | 4231		       		       
20 |  |  |--| 4213  -->   5 |--|  | 213
21 |--|  |  | 2413
22 |  |--|  | 2143
23 |  |  |--| 2134
{% endhighlight %}



# Recursive approach



{% highlight asciidoc %}
 0 |  | 12
 1 |--| 21 L = 2
{% endhighlight %}



{% highlight asciidoc %}
 0 |  | 12	        -->  0 |  |  | 123
                             1 |  |--| 132 L = 3
                             2 |--|  | 312 L = 3
 1 |--| 21 L = 2        -->  3 |  |--| 321 L = 2
                             4 |--|  | 231 L = 3
                             5 |  |--| 213 L = 3
{% endhighlight %}



{% highlight asciidoc %}
 0 |  |  | 123         -->   0 |  |  |  | 1234	   
                             1 |  |  |--| 1243 L = 4
                             2 |  |--|  | 1423 L = 4
                             3 |--|  |  | 4123 L = 4
 1 |--|  | 132 L = 3   -->   4 |  |  |--| 4132 L = 3
                             5 |--|  |  | 1432 L = 4
                             6 |  |--|  | 1342 L = 4
                             7 |  |  |--| 1324 L = 4
 2 |  |--| 312 L = 3   -->   8 |--|  |  | 3124 L = 3
                             9 |  |  |--| 3142 L = 4
                            10 |  |--|  | 3412 L = 4
                            11 |--|  |  | 4312 L = 4
 3 |--|  | 321 L = 2   -->  12 |  |  |--| 4321 L = 2
                            13 |--|  |  | 3421 L = 4
                            14 |  |--|  | 3241 L = 4
                            15 |  |  |--| 3214 L = 4
 4 |  |--| 231 L = 3   -->  16 |--|  |  | 2314 L = 3
                            17 |  |  |--| 2341 L = 4
                            18 |  |--|  | 2431 L = 4
                            19 |--|  |  | 4231 L = 4
 5 |--|  | 213 L = 3   -->  20 |  |  |--| 4213 L = 3
                            21 |--|  |  | 2413 L = 4
                            22 |  |--|  | 2143 L = 4
                            23 |  |  |--| 2134 L = 4
{% endhighlight %}





{% highlight asciidoc %}
JohnsonTrotter(S, L, N, D)
 10: o := 0
 20: IF L < N THEN
 30:  o := JohnsonTrotter(S, L, N, D)
 40: IF D[L] = right
 50:   FOR i := 1 TO L - 1
 60:    swap_with_next(S, o + i)
 70:    IF L < N THEN
 80:      o := JohnsonTrotter(S, L, N, D)
 90:   D[L] := left
110:   RETURN o
120: ELSE
130:   FOR i := L - 1 DOWN TO 1
140:     swap_with_next(S, o + i)
150:     IF L < N THEN
160:       o := JohnsonTrotter(S, L, N, D)
170:   D[L] := right
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
    d[i] = 0;
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


# Iterative approach


{% highlight asciidoc %}
JohnsonTrotter(S, N)
 10: FOR e := 1 TO N
 20:   I[e] := e
 30:   O[e] := e
 40:   D[e] := -1
 50: DO
 60:   FOR e := N TO 1
 70:     IF 0 < I[e] + D[e] <= n AND e > O[I[e] + D[e]]
 80:       BREAK
 90:   IF e = 1
110:     RETURN
120:   SWAP(S, I[e], I[e] + D[e])
130:   SWAP(O, I[e], I[e] + D[e])
140:   SWAP(I, e, O[I[e]])
150:   FOR e := e + 1 TO N
160:     D[e] := -D[e]
170: LOOP
{% endhighlight %}


{% highlight c %}
#include <stdio.h>

#define MAX_LEN 30

#define Ss(s) scanf("%s", s)

#define MIN(x,y) ( x < y ? x : y )

int count = 0;
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

void swap_chars(char *s, int i, int j) {
  char t = s[i];
  s[i] = s[j];
  s[j] = t;
}

void swap_ints(int *s, int i, int j) {
  int t = s[i];
  s[i] = s[j];
  s[j] = t;
}

void johnson_trotter(char *s, int n) {
  print_swap(-1, n);
  printf(" %s\n", s);

  int e;
  int i[n];
  int o[n];
  int d[n];
  for(e = 0; e < n; e++) {
    i[e] = e;
    o[e] = e;
    d[e] = -1;
  }
  while(1) {
    for(e = n - 1; 0 < e; e--)
      if(0 <= i[e] + d[e] && i[e] + d[e] <= n - 1 && e > o[i[e] + d[e]])
	break;
    if(e == 0)
      return;
    swap_chars(s, i[e], i[e] + d[e]);
    swap_ints(o, i[e], i[e] + d[e]);
    swap_ints(i, e, o[i[e]]);
    print_swap(MIN(i[e], i[e] - d[e]), n);
    printf(" %s e = %d\n", s, e + 1);
    for(e += 1; e < n; e++)
      d[e] = -d[e];
  }
}

int main() {
  int slen;
  char s[MAX_LEN];
  Ss(s);
  for(slen = 0; s[slen] != '\0'; slen++);
  johnson_trotter(s, slen);
  return 0;
}
{% endhighlight %}


# References


<dl>
  <dt id="bogomolny">
    Bogomolny
  </dt>
  <dd>
    <a href="http://www.cut-the-knot.org/Curriculum/Combinatorics/JohnsonTrotter.shtml">http://www.cut-the-knot.org/Curriculum/Combinatorics/JohnsonTrotter.shtml</a>
  </dd>
  <dt id="johnson">
    Johnson
  </dt>
  <dd>
    something
  </dd>
  <dt id="levitin">
    Levitin
  </dt>
  <dd>
    <a href="https://www.google.com.mx/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=introduction%20to%20the%20design%20and%20analysis%20of%20algorithms">Introduction to the analysis and design of algorithms</a>
  </dd>
  <dt id="trotter">
    Trotter
  </dt>
  <dd>
    something
  </dd>
  <dt id="yorgey">
    Yorgey
  </dt>
  <dd>
    <a href="https://mathlesstraveled.com/2013/01/03/the-steinhaus-johnson-trotter-algorithm/">https://mathlesstraveled.com/2013/01/03/the-steinhaus-johnson-trotter-algorithm/</a>
  </dd>
</dl>
