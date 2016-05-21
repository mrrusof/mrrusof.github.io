---
layout: post
title: Arbitrage
date: 2016-04-23
author: Ruslan Ledesma-Garza
summary: Something something
---

Arbitrage is [problem 104 in the UVa Online
Judge](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=40).
Even though I include the problem description in this post, I
encourage you to visit the [UVa Online
Judge](https://uva.onlinejudge.org/index.php) because there you will
be able to submit your solution to get it judged.

# Problem

Arbitrage is the trading of one currency for another with the hopes of
taking advantage of small differences in conversion rates among
several currencies in order to achieve a profit.  For example, if
1.00 in U.S. currency buys 0.7 British pounds currency,  in British
currency buys 9.5 French francs, and 1 French franc buys 0.16 in
U.S. dollars, then an arbitrage trader can start with 1.00 USD and earn
1 * 0.7 * 9.5 * 0.16 = 1.064 dollars thus earning a profit of 6.4 percent.

You will write a program that determines whether a sequence of
currency exchanges can yield a profit as described above.

To result in successful arbitrage, a sequence of exchanges must begin
and end with the same currency, but any starting currency may be
considered.


# Input

The input file consists of one or more conversion tables.  You must
solve the arbitrage problem for each of the tables in the input file.
Each table is preceded by an integer `n` on a line by itself giving
the dimensions of the table.  The maximum dimension is 20. The minimum
dimension is 2.

The table then follows in row major order but with the diagonal
elements of the table missing (these are assumed to have value 1.0).
Thus the first row of the table represents the conversion rates
between country `1` and `n - 1` other countries, i.e. the amount of
currency of country `i` `(2 <= i <= n)` that can be purchased with one
unit of the currency of country `1`.

Thus each table consists of `n + 1` lines in the input file: 1 line
containing `n` and `n` lines representing the conversion table.


# Output

For each table in the input file, you must determine whether a
sequence of exchanges exists that results in a profit of more than 1
percent (0.01).  If a sequence exists you must print the sequence of
exchanges that results in a profit.  If there is more than one
sequence that results in a profit of more than 1 percent you must
print a sequence of minimal length, i.e. one of the sequences that
uses the fewest exchanges of currencies to yield a profit.

Because the IRS (United States Internal Revenue Service) taxes long
transaction sequences with a high rate, all profiting sequences must
consist of `n` or fewer transactions where `n` is the dimension of
the table giving conversion rates.  The sequence `1 2 1` represents
two conversions.

If a profiting sequence exists you must print the sequence of
exchanges that results in a profit.  The sequence is printed as a
sequence of integers with the integer `i` representing the `i`-th line
of the conversion table (country `i`).  The first integer in the
sequence is the country from which the profiting sequence starts.
This integer also ends the sequence.

If no profiting sequence of `n` or fewer transactions exists, then the line
{% highlight asciidoc %}
no arbitrage sequence exists
{% endhighlight %}
should be printed.


# Sample Input

{% highlight asciidoc %}
3
1.2 .89
.88 5.1
1.1 0.15
4
3.1    0.0023    0.35
0.21   0.00353   8.13
200    180.559   10.339
2.11   0.089     0.06111
2
2.0
0.45
{% endhighlight %}


# Sample Output

{% highlight asciidoc %}
1 2 1
1 2 4 1
no arbitrage sequence exists
{% endhighlight %}


# Analysis

- A solution is a path
  - that is a cycle
  - that is profitable by 1%
  - that is shortest
- A solution may consist of two or more simple cycles.
- A solution may not consist of most profitable paths.

{% highlight asciidoc %}
                ------------->    USD     <-------------
              /              /           \               \
  1.01^(-1/2)|   1.01^(1/2) /             \ 1.01^(1/2)    | 1.01^(1/2)
             |             /               \              |
              \           v   1.01^(1/2)    v            /
                ------  MXN --------------> EUR --------
                         ^                   |
                         |    1.01^(-1/2)    |
                          \-----------------/
{% endhighlight %}

**Figure 1: Problem with two profitable cycles.**


# Approach



# Algorithm

{% highlight asciidoc %}
R(n, W)
 1: B[0] := Identity matrix n x n
 2: B[1] := 0
 3: FOR l := 2 TO n
 4:     B[l] := 0
 5:     FOR i := 1 TO n
 6:         FOR j := i + 1 TO n
 7:             FOR k := i TO n
 8:                 IF B[l - 1][j,i] < W[j,k] * B[l - 2][k,i]
 9:                     B[l - 1][j,i] = W[j,k] * B[l - 2][k,i]
12:             IF B[l][i,i] < W[i,j] * B[l - 1][j,i]
13:                 B[l][i,i] = W[i,j] * B[l - 1][j,i]
14:                 IF B[l][i,i] >= 1.01
15:                     RETURN B[l][i,i]
16: RETURN 0
{% endhighlight %}

{% highlight asciidoc %}
S(n, W)
 1: B[1] := W
 2: FOR l := 2 TO n
 3:     B[l] := 0
 4:     FOR i := 1 TO n
 5:         FOR j := 1 TO n
 6:             FOR k := 1 TO n
 7:                 IF B[l][i,j] < W[i,k] * B[l - 1][k,j]
 8:                     B[l][i,j] = W[i,k] & B[l - 1][k,j]
 9:                     IF B[l][i,i] >= 1.01
10:                         RETURN B[l][i,i]
11: RETURN 0
{% endhighlight %}

{% highlight asciidoc %}
C(i, n, l) = 0                                  if l <  2
C(i, n, l) = (n - i)^(l - 1) - C(i, n, l - 1)   otherwise
{% endhighlight %}

{% highlight asciidoc %}
C(n, l) = C(1, n, l) + C(2, n, l) + ... + C(n, n, l)
{% endhighlight %}

{% highlight asciidoc %}
C(n) = C(n, 2) + C(n, 2) + ... + C(n, n)
{% endhighlight %}

{% highlight ocaml %}
#!/usr/bin/env ocaml

let rec pow b e =
  if e = 0 then 1
  else b * pow b (e - 1)

let rec c i n l =
  if l < 2 then 0
  else pow (n - i) (l - 1) - c i n (l - 1)

let rec iter n f acc =
  if n = 0 then acc
  else iter (n - 1) f (f n acc)

let c n l =
  iter n (fun i acc -> acc + c i n l) 0

let c n =
  iter n (fun l acc -> acc + c n l) 0

let rec upto m n f =
  if m = n then f m
  else begin f m; upto (m + 1) n f end

let _ =
  upto 3 16 (fun n -> Printf.printf "K%2d %40d\n" n (c n))
{% endhighlight %}

{% highlight asciidoc %}
B'[i,j] = max { W[i,k] * B[k,j] | k \in 1 ... n }
{% endhighlight %}



# Implementation

{% highlight c %}
#include <stdio.h>

#define V 20
#define MIN_PROFIT 1.01

#define Si(n) scanf("%d", &n)
#define Sf(n) scanf("%f", &n)

void print_path(int s, int t, int l, int succ[V+1][V][V]) {
  if(l == 0)
    printf("%d\n", s + 1);
  else {
    printf("%d ", s + 1);
    print_path(succ[l][s][t], t, l - 1, succ);
  }
}

void set_to_zero(int n, float m[V][V]) {
  int i, j;
  for(i = 0; i < n; i++)
    for(j = 0; j < n; j++)
      m[i][j] = 0;
}

void R(int n, float rate[V][V]) {
  float benefit[V+1][V][V];
  int succ[V+1][V][V];
  int l, i, j, k;
  set_to_zero(n, benefit[0]);
  for(i = 0; i < n; i++)
    benefit[0][i][i] = 1;
  set_to_zero(n, benefit[1]);
  for(l = 2; l <= n; l++) {
    set_to_zero(n, benefit[l]);
    for(i = 0; i < n; i++)
      for(j = i + 1; j < n; j++) {
        for(k = i; k < n; k++)
          if(benefit[l - 1][j][i] < rate[j][k] * benefit[l - 2][k][i]) {
            benefit[l - 1][j][i] = rate[j][k] * benefit[l - 2][k][i];
            succ[l - 1][j][i] = k;
          }
        if(benefit[l][i][i] < rate[i][j] * benefit[l - 1][j][i]) {
          benefit[l][i][i] = rate[i][j] * benefit[l - 1][j][i];
          succ[l][i][i] = j;
          if(benefit[l][i][i] >= MIN_PROFIT) {
            print_path(i, i, l, succ);
            return;
          }
        }
      }
  }
  printf("no arbitrage sequence exists\n");
}

int main() {
  int n, i, j;
  float r;
  float rate[V][V];
  while(Si(n) != EOF) {
    for(i = 0; i < n; i++)
      for(j = 0; j < n; j++) {
        if(i == j) {
          rate[i][i] = 0;
          continue;
        }
        Sf(r);
        rate[i][j] = r;
      }
    R(n, rate);
  }
  return 0;
}
{% endhighlight %}

{% highlight c %}
#include <stdio.h>

#define V 20
#define MIN_PROFIT 1.01

#define Si(n) scanf("%d", &n)
#define Sf(n) scanf("%f", &n)

void print_path(int s, int t, int l, int succ[V][V][V]) {
  if(l == 0)
    printf("%d\n", s + 1);
  else {
    printf("%d ", s + 1);
    print_path(succ[l][s][t], t, l - 1, succ);
  }
}

void set_to_zero(int n, float m[V][V]) {
  int i, j;
  for(i = 0; i < n; i++)
    for(j = 0; j < n; j++)
      m[i][j] = 0;
}

void S(int n, float rate[V][V]) {
  float benefit[V][V][V];
  int succ[V][V][V];
  int l, i, j, k;
  for(i = 0; i < n; i++)
    for(j = 0; j < n; j++) {
      benefit[1][i][j] = rate[i][j];
      if(rate[i][j] > 0)
	succ[1][i][j] = j;
    }
  for(l = 2; l <= n; l++) {
    set_to_zero(n, benefit[l]);
    for(i = 0; i < n; i++)
      for(j = 0; j < n; j++)
	for(k = 0; k < n; k++) {
	  if(benefit[l][i][j] < rate[i][k] * benefit[l - 1][k][j]) {
	    benefit[l][i][j] = rate[i][k] * benefit[l - 1][k][j];
	    succ[l][i][j] = k;
	    if(benefit[l][i][i] >= MIN_PROFIT) {
	      print_path(i, i, l, succ);
	      return;
	    }
	  }
	}
  }
  printf("no arbitrage sequence exists\n");
}

int main() {
  int n, i, j;
  float r;
  float rate[V][V];
  while(Si(n) != EOF) {
    for(i = 0; i < n; i++)
      for(j = 0; j < n; j++) {
	if(i == j) {
	  rate[i][i] = 0;
	  continue;
	}
	Sf(r);
	rate[i][j] = r;
      }
    S(n, rate);
  }
  return 0;
}
{% endhighlight %}

# Related work

{% highlight asciidoc %}
SlowAllPairsShortestPath(n, W)
1: L[0] = Identity matrix n x n
2: FOR m := 1 TO n
3:     L[m] := INFINITY
4:     FOR i := 1 TO n
5:         FOR j := 1 TO n
6:             FOR k := 1 TO n
7:                 IF L[m][i,j] > L[m - 1][i,k] + W[k,j]
8:                     L[m][i,j] = L[m - 1][i,j] + W[k,j]
9: RETURN L[n]
{% endhighlight %}

{% highlight asciidoc %}
FloydWarshall(n, W)
1: L[0] = W
2: FOR k := 1 TO n
3:     FOR i := 1 TO n
4:         FOR j := 1 TO n
5:             IF L[k - 1][i,j] > L[k - 1][i,k] + L[k - 1][k,j]
6:                 L[k][i,j] = L[k - 1][i,j]
7:             ELSE
8:                 L[k][i,j] = L[k - 1][i,k] + L[k - 1][k,j]
9: RETURN L[n]
{% endhighlight %}

{% highlight asciidoc %}
L'[i,j] = min(L[i,j], L[i,k] + L[k,j])
{% endhighlight %}


# Summary



# References

[1] Implementation of Tarjan's algorithm by Johannes Schauer.
- https://blog.mister-muffin.de/2012/07/04/enumerating-elementary-circuits-of-a-directed_graph/
- https://github.com/josch/cycles_tarjan/blob/master/cycles.py

[2] Tarjan's algorithm is O(V*E), for dense graphs O(N^3).
- http://www.cs.colorado.edu/department/publications/reports/docs/CU-CS-024-73.pdf

[3] Counting cycles is NP hard.
- http://www.cs.umd.edu/~jkatz/complexity/f11/lecture23.pdf

[4] Solution that kind of resembles Floyd-Warshall but it really looks like Slow All Pairs Shortest Paths
- http://abitofcs.blogspot.mx/2014/08/a-bit-of-uva-uva-104-arbitrage.html


# Comments
