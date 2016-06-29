---
layout: post
title: .
date: 2016-06-18
author: Ruslan Ledesma-Garza
summary: Something something
---

If you are looking for an explanation of how Steinhaus-Johnson-Trotter algorithm constructs all permutations, keep reading.

- Johnson-Trotter sequences
- Johnson-Trotter method
- Iterative algorithm
- Johnson's algorithm
- Trotter's algorithm
- Recursive algorithm

There is a recursive approach and an iterative approach to generating the list of permutations.

- Recursive algorithm here is not found anywhere else.
- Iterative algorithm here is found in Sedgewick and Johnson.
  - Is my implementation efficient?

Related work

- Wikipedia does not give pseudocode.
- [Yorgey](#yorgey) does not give pseudocode.
- [Yorgey](#yorgey) explains by example Johnson-Trotter sequences.
- [Sedgewick](#sedgewick) presents an iterative algorithm that constructs Johnson-Trotter lists of permutations.
- [Levitin] says that element `m` is mobile when `T(m)` exists.
- Formulations that are not efficient?
  - [Levitin] explains a formulation based in search that is not efficient?
  - [Bogomolny](#bogomolny) presents the algorithm that [Levitin] presents.



# Johnson-Trotter sequences

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




# Johnson-Trotter Method

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

My interpretation of Johnson's rules or *Method in Terms of Marks*.

{% highlight asciidoc %}
JohnsonRules(S, N)
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


# Iterative algorithm

- Sedgewick's interpretation accumulates offset $@x@$ instead of computing it from counters $@N@$.
- He explains his algorithm in two steps.
  - He explains how to determine the element to transpose in each permutations of the Johnson-Trotter sequence.
  - He explains how to compute direction and offset for the element. From direction we get relative position. From offset we get absolute position.

Sedgewick's labeling of permutations.

{% highlight asciidoc %}
LABEL(N)
 10: i := 1
 20: WHILE i <= N
 30:   i := i + 1
 40:   C[i] := 1
 50: C[1] := 0
 60: WHILE TRUE
 70:   i := N
 80:   WHILE C[i] = i
 90:     C[i] := 1
100:     i := i - 1
110:   IF i = 1
120:     BREAK
130:   print i
140:   C[i] := C[i] + 1
{% endhighlight %}

{% highlight c %}
#include <stdio.h>

#define Si(i) scanf("%d", &i)

void label(n) {
  int i;
  int c[n];
  for(i = 1; i < n; i++)
    c[i] = 0;
  c[0] = -1;
  while(1) {
    for(i = n - 1; c[i] == i; i--)
      c[i] = 0;
    if(i == 0)
      break;
    printf("%d ", i + 1);
    c[i]++;
  }
  printf("\n");
}

int main() {
  int n;
  Si(n);
  label(n);
  return 0;
}
{% endhighlight %}

Sedgewick's interpretation of Johnson's algorithm.
I inverted the direction of movement by initializing `D[i]` to `false` for $@i \in 1, ..., N@$.

{% highlight asciidoc %}
JohnsonIterative(S, N)
 10: i := 1
 20: WHILE i <= N
 30:   i := i + 1
 40:   C[i] := 1
 50:   D[i] := false
 60: C[1] := 0
 70: WHILE TRUE
 80:   i := N
 90:   x := 0
100:   WHILE C[i] = i
110:     IF NOT D[i]
120:       x := x + 1
130:     D[i] := NOT D[i]
140:     C[i] := 1
150:     i := i - 1
160:   IF i = 1
170:     BREAK
180:   IF D[i]
190:     k := C[i] + x
200:   ELSE
210:     k := i - C[i] + x
220:   swap elements in positions k and k + 1 of S
230:   C[i] := C[i] + 1
{% endhighlight %}

{% highlight c %}
#include <stdio.h>

#define Ss(s) scanf("%s", s)

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

void swap_with_next(char *s, int i) {
  char t = s[i];
  s[i] = s[i + 1];
  s[i + 1] = t;
}

void johnson_trotter_iterative(char *s, int n) {
  int i;
  int x;
  int k;
  int c[n];
  int d[n];
  for(i = 1; i < n; i++) {
    c[i] = 0;
    d[i] = 0;
  }
  c[0] = -1;
  print_swap(-1, n);
  printf(" %s\n", s);
  while(1) {
    i = n - 1;
    x = 0;
    for(; c[i] == i; i--) {
      if(!d[i])
	x++;
      d[i] = !d[i];
      c[i] = 0;
    }
    if(i == 0)
      break;
    if(d[i])
      k = c[i] + x;
    else
      k = (i - 1) - c[i] + x;
    swap_with_next(s, k);
    print_swap(k, n);
    printf(" %s\n", s);
    c[i]++;
  }
}

int main() {
  char *s;
  int slen;
  Ss(s);
  for(slen = 0; s[slen] != '\0'; slen++);
  johnson_trotter_iterative(s, slen);
  return 0;
}
{% endhighlight %}


# Johnson's algorithm

- Rules in terms of positions:
  - For permutation $@P_N@$, index $@N@$ corresponds to a configuration of counters $@(d_2, d_3, ..., d_{n-1}, d_n)@$.
  - $@k@$ is the largest subscript for which $@d_k > 0@$
  - $@a_N(k)@$ is the left-hand position of the pair of elements to exchange.
  - $@b_N(k)@$ is the offset of the pair to exchange. The offset is given by the number of elements greater than $@k@$ to the left of $@P_N@$.
  - Position and offset are determined by index $@N@$ as opposed to bookkeeping position and direction of each element.
  - Sedgewick accumulates offset $@x@$ instead of computing offset $@b_N(k)@$ from $@N@$.

My interpretation of Johnson's algorithm or *Method in Terms of Positions*.

{% highlight asciidoc %}
JohnsonOriginal(S, N)
 10: i := 1
 20: WHILE i <= N
 30:   i := i + 1
 40:   C[i] := 0
 50: C[1] := 1
 70: WHILE TRUE
 80:   i := N
 90:   WHILE C[i] = i - 1
100:     C[i] := 0
110:     i := i - 1
120:   C[i] := C[i] + 1
130:   IF i = 1
140:     BREAK
150:   IF C[i - 1] + (i - 1) * C[i - 2] is even
160:     ank := i - C[i]
170:   ELSE
180:     ank := C[i]
190:   IF i = N
200:     bnk := 0
210:   ELSE IF i = N - 1
220:     IF C[N - 1] + (N - 1) * C[N - 2] is even
230:       bnk := 0
240:     ELSE
250:       bnk := 1
260:   ELSE IF i is even
270:     IF C[i] is even
280:       bnk := 0
290:     ELSE
300:       bnk := 2
310:   ELSE
320:     IF C[i] + C[i - 1] is even
330:       bnk := 0
340:     ELSE
350:       bnk := 1
360:   sn := ank + bnk
370:   swap elements in positions sn and sn + 1 of S
{% endhighlight %}

{% highlight c %}
#include <stdio.h>

#define Ss(s) scanf("%s", s)

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

void swap_with_next(char *s, int i) {
  char t = s[i];
  s[i] = s[i + 1];
  s[i + 1] = t;
}

void johnson_original(char *s, int n) {
  int j;
  int i;
  int c[n + 1];
  int ank;
  int bnk;
  int sn;
  for(i = 1; i <= n; i++)
    c[i] = 0;
  c[1] = 1;
  print_swap(-1, n);
  printf(" %s\n", s);
  while(1) {
    i = n;
    for(; c[i] == i - 1; i--)
      c[i] = 0;
    c[i]++;
    if(i == 1)
      break;
    if((c[i - 1] + (i - 1) * c[i - 2]) % 2 == 0)
      ank = i - c[i];
    else
      ank = c[i];
    if(i == n)
      bnk = 0;
    else if(i == n - 1) {
      if((c[n - 1] + (n - 1) * c[n - 2]) % 2 == 0)
	bnk = 0;
      else
	bnk = 1;
    } else if(i % 2 == 0) {
      if(c[i] % 2 == 0)
	bnk = 0;
      else
	bnk = 2;
    } else {
      if((c[i] + c[i - 1]) % 2 == 0)
	bnk = 0;
      else
	bnk = 1;
    }
    sn = ank + bnk - 1; /* Adjust index by subtracting 1. */
    swap_with_next(s, sn);
    print_swap(sn, n);
    printf(" %s\n", s);
  }
}

int main() {
  char *s;
  int slen;
  Ss(s);
  for(slen = 0; s[slen] != '\0'; slen++);
  johnson_original(s, slen);
  return 0;
}
{% endhighlight %}


# Trotter's algorithm

[Trotter](#trotter) formulated an algorithm that creates the same list of permutations than Johnson.

- Trotter says
  - "The excuse for adding PERM to the growing pile of permutation
    generators is that, at the expense of some extra global storage,
    it cuts the manipulation of x to the theoretical minimum of n!
    transpositions, and appears to offer an advantage in speed. It
    also has the (probably useless) property that the permutations
    it generates are alternately odd and even;"
- You can find Trotter's algorithm in CACM August 1962.

Trotter's algorithm.

{% highlight asciidoc %}
GLOBAL first
GLOBAL p[2:MAX_LEN]
GLOBAL d[2:MAX_LEN]

Perm(X, N)

IF first
   INITIALIZE:
     FOR k := 2 TO N
       p[k] := 0
       d[k] := 1
     first := false

k := 0

INDEX:
  q := p[n] + d[n]
  p[n] := q
  IF q = n
    d[n] := -1
    GOTO LOOP
  IF q != 0
    GOTO TRANSPOSE
  d[n] := 1
  k := k + 1

LOOP:
  IF n > 2
    n := n - 1
    GOTO INDEX

Final exit:
  q := 1
  first := true

TRANSPOSE:
  q := q + k
  t := x[q]
  x[q] := x[q + 1]
  x[q + 1] := t
{% endhighlight %}

{% highlight c %}
#include <stdio.h>

#define Ss(s) scanf("%s", s)
#define MAX_LEN 30

int first;
int p[MAX_LEN + 1]; /* p[k] is position of k */
int d[MAX_LEN + 1]; /* d[k] is direction of k, d[k] = {1,0} is {right,left} */

void trotter(char *x, int n) { /* x = " hola" */
  /* Variables not in the original algorithm. */
  int i;
  int nn = n;

  int k;
  int q;
  char t;

  if(first) {
  INITIALIZE:
    for(k = 2; k <= n; k++) {
      p[k] = 0;
      d[k] = 1;
    }
    first = 0;
  }

  k = 0;

 INDEX:
  q = p[n] + d[n];
  p[n] = q;
  if(q == n) {
    d[n] = -1;
    goto LOOP;
  }
  if(q != 0)
    goto TRANSPOSE;
  d[n] = 1;
  k++;

 LOOP:
  if(n > 2) {
    n--;
    goto INDEX;
  }

 FINAL_EXIT:
  q = 1;
  first = 1;

 TRANSPOSE:
  q = q + k - 1; /* Adjust index by subtracting 1 */
  t = x[q];
  x[q] = x[q + 1];
  x[q + 1] = t;

  /* Code not in the original algorithm */
  for(i = 2; i <= nn; i++) {
    printf("%5d  ", p[i]);
  }
  printf("%5d  %5d  ", k, q);
}

int main() {
  int slen;
  char s[MAX_LEN];
  Ss(s);
  for(slen = 0; s[slen] != '\0'; slen++);

  for(first = 2; first <= slen; first++)
    printf("p[%2d]  ", first);
  printf("    k      q\n");

  first = 1;
  trotter(s, slen);
  printf("%s\n", s);
  while(first == 0) {
    trotter(s, slen);
    printf("%s\n", s);
  }
  return 0;
}
{% endhighlight %}



# Recursive algorithm

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



My recursive version of Johnson-Trotter.

{% highlight asciidoc %}
JohnsonTrotterRecursive(S, L, N, D)
 10: o := 0
 20: IF L < N THEN
 30:  o := JohnsonTrotterRecursive(S, L, N, D)
 40: IF D[L] = right
 50:   FOR i := 1 TO L - 1
 60:    swap_with_next(S, o + i)
 70:    IF L < N THEN
 80:      o := JohnsonTrotterRecursive(S, L, N, D)
 90:   D[L] := left
110:   RETURN o
120: ELSE
130:   FOR i := L - 1 DOWN TO 1
140:     swap_with_next(S, o + i)
150:     IF L < N THEN
160:       o := JohnsonTrotterRecursive(S, L, N, D)
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
    <a href="http://www.ams.org/journals/mcom/1963-17-083/S0025-5718-1963-0159764-2/S0025-5718-1963-0159764-2.pdf">Generation of Permutations by Adjacent Transposition</a>.
    S. M. Johnson, Mathematics of Computation, 17(83) (1963), pp. 282-285
  </dd>
  <dt id="levitin">
    Levitin
  </dt>
  <dd>
    Levitin, Anany (2012) [2003]. Introduction to the analysis and design of algorithms (3rd ed.). Pearson. pp. 144-145. ISBN 978-0-13-231681-1.
  </dd>
  <dt id="sedgewick">
    Sedgewick
  </dt>
  <dd>
    <a href="http://homepage.math.uiowa.edu/~goodman/22m150.dir/2007/Permutation%20Generation%20Methods.pdf">Permutation Generation Methods</a>.
    R. Sedgewick, Computing Surveys, 9(2) (1977), pp. 137-164
  </dd>
  <dt id="trotter">
    Trotter
  </dt>
  <dd>
    <a href="http://dl.acm.org/citation.cfm?id=368660">Algorithm 115: Perm</a>.
    H. F. Trotter, Communications of the ACM, 5(8) (1962), pp. 434-435
  </dd>
  <dt id="yorgey">
    Yorgey
  </dt>
  <dd>
    <a href="https://mathlesstraveled.com/2013/01/03/the-steinhaus-johnson-trotter-algorithm/">https://mathlesstraveled.com/2013/01/03/the-steinhaus-johnson-trotter-algorithm/</a>
  </dd>
</dl>
