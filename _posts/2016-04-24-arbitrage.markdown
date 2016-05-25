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
transaction sequences with a high rate, all profitable sequences must
consist of `n` or fewer transactions where `n` is the dimension of
the table giving conversion rates.  The sequence `1 2 1` represents
two conversions.

If a profitable sequence exists you must print the sequence of
exchanges that results in a profit.  The sequence is printed as a
sequence of integers with the integer `i` representing the `i`-th line
of the conversion table (country `i`).  The first integer in the
sequence is the country from which the profitable sequence starts.
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

For a given problem, the conversion table corresponds to a graph and the solution corresponds to a cycle in that graph.
Consider the following conversion table over USD, MXN, and EUR.

{% highlight asciidoc %}
3
1.004987562112089 1.004987562112089
0.99503719020999 1.004987562112089
1.004987562112089 0.99503719020999
{% endhighlight %}

The table corresponds to the following interpretation.

{% highlight asciidoc %}
     | USD         | MXN         | EUR
----------------------------------------------
 USD | 0           | 1.01^(1/2)  | 1.01^(1/2)
 MXN | 1.01^(-1/2) | 0           | 1.01^(1/2)
 EUR | 1.01^(1/2)  | 1.01^(-1/2) | 0
{% endhighlight %}

The corresponding graph is the following.

<img src="/assets/2016-04-24.two-solutions.png" alt="" style="width: 300px; display: block; margin-left: auto; margin-right: auto;" />

A sequence of exchanges may yield a profit only if the sequence is a cycle.
For example, the cycle `USD -> MXN -> EUR -> USD` yields profit of `1.01^(3/2)`.
Given that the number of vertices in the graph is 3, we do not consider cycles longer than 3.
Consider the cycles of length 3 or less.

{% highlight asciidoc %}
CYCLE                    : RATE

USD -> MXN -> USD        : 1
USD -> EUR -> USD        : 1.01
EUR -> MXN -> EUR        : 1
USD -> MXN -> EUR -> USD : 1.01^(3/2)
USD -> EUR -> MXN -> USD : 1.01^(-1/2)
{% endhighlight %}

A cycle profitable when its rate is greater or equal to 1.01.
Out of the five cycles, only the following two are profitable.
{% highlight asciidoc %}
USD -> EUR -> USD
USD -> MXN -> EUR -> USD
{% endhighlight %}

Out of the two profitable cycles, `USD -> EUR -> USD` is the only solution because it is the shortest cycle.

A solution may not be a [simple cycle](https://en.wikipedia.org/wiki/Cycle_(graph_theory)#Definitions) like in the previous example.
For example, consider the following conversion table and its corresponding graph.

{% highlight asciidoc %}
4
1 0 0
1.005 0 0
0 0 0
0 0 0
{% endhighlight %}

<img src="/assets/2016-04-24.repeated-simple-cycle-solution.png" alt="" style="width: 300px; display: block; margin-left: auto; margin-right: auto;" />

For the graph, the cycles of length 4 or less are the following

{% highlight asciidoc %}
CYCLE     : RATE
1 2 1     : 1.005
1 2 1 2 1 : 1.010025
{% endhighlight %}

The only profitable cycle is `1 2 1 2 1` and therefore it is the only solution.
The cycle is a solution regardless of the fact that it consists of the repetition of simple cycle `1 2 1`.

A solution that is not a simple cycle may not be the repetition of a simple cycle like in the previous example.
For example, consider the following conversion table and its corresponding graph.

{% highlight asciidoc %}
5
1.001992047666533 0 0 1.001992047666533
1.001992047666533 0 0 0
1.001992047666533 0 0 0
0 0 0 0
0 0 1.001992047666533 0
{% endhighlight %}

<img src="/assets/2016-04-24.two-simple-cycle-solution.png" alt="" style="width: 300px; display: block; margin-left: auto; margin-right: auto;" />

For the graph, the cycles of length 5 or less are the following.

{% highlight asciidoc %}
CYCLE       : RATE
1 2 1       : 1.01^(2/5)
1 5 3 1     : 1.01^(3/5)
1 2 1 5 3 1 : 1.01
{% endhighlight %}

The only profitable cycle is `1 2 1 5 3 1` and therefore it is the only solution.
The cycle consists of simple cycles `1 2 1` and `1 5 3 1`.

Searching for a solution is difficult because there may be many cycles for a given problem.
The reason is that a conversion table of length `n` corresponds to [complete graph](https://en.wikipedia.org/wiki/Complete_graph) `Kn`.
For complete graphs of size `2 <= n <= 20`, the number of cycles of length `n` or less is the following.

{% highlight asciidoc %}
 K2:                                       1
 K3:                                       5
 K4:                                      42
 K5:                                     384
 K6:                                   4,665
 K7:                                  69,537
 K8:                               1,230,124
 K9:                              25,140,552
K10:                             582,508,305
K11:                          15,084,077,381
K12:                         431,646,196,806
K13:                      13,525,545,361,080
K14:                     460,576,563,322,057
K15:                  16,935,036,272,292,001
K16:                 668,691,718,661,091,000
K17:              28,220,125,532,003,984,176
K18:           1,267,597,789,008,779,578,401
K19:          60,381,304,029,673,985,693,205
K20:       3,040,239,935,992,309,703,757,730
{% endhighlight %}



# Approach

We consider a limited number of candidates for each length.

We consider the paths of length 2 for example 2.
The cycles of length 2 are paths of length 2.

We consider all cycles of length 2 for example 2.
For root `1`, we consider child `2` and construct candidate `1 -> 2 -> 1`.

For a given root `i`, we consider each child `j` and construct candidate `i -> j ~> i`.

<img src="/assets/2016-04-24.two-solutions-cycles-len-2.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />

When we do not find a solution, we consider candidates of lenght 3 for example 2.
For root `1`, we consider child `2` and construct the following candidate.

{% highlight asciidoc %}
1 -> 2 -> 3 -> 1
^    ^
|    |
|    child
root
{% endhighlight %}

We construct the rest of the candidates of length 3 in the following way.
For a given root `i`, we consider each child `j` and construct candidate `i -> j ~> i`.

<img src="/assets/2016-04-24.two-solutions-cycles-len-3.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />

When we do not find a solution, we consider the cycles of length 4.



{% highlight asciidoc %}
B[1] = W
B'[i,j] = max { W[i,k] * B[k,j] | k \in 1 ... n }
{% endhighlight %}

<img src="/assets/2016-04-24.two-solutions-trees-len-2.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />



<img src="/assets/2016-04-24.two-solutions-repeated-cycles-len-2.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />

<img src="/assets/2016-04-24.two-solutions-trees-len-3.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />

<img src="/assets/2016-04-24.two-solutions-cycles-len-3.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />

<img src="/assets/2016-04-24.two-solutions-repeated-cycles-len-3.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />





# Algorithm

{% highlight asciidoc %}
R(n, W)
 1: B[0] := Identity matrix n x n
 2: B[1] := 0
 3: FOR m := 2 TO n
 4:     B[m] := 0
 5:     FOR i := 1 TO n
 6:         FOR j := i + 1 TO n
 7:             FOR k := i TO n
 8:                 IF B[m - 1][j,i] < W[j,k] * B[m - 2][k,i]
 9:                     B[m - 1][j,i] = W[j,k] * B[m - 2][k,i]
12:             IF B[m][i,i] < W[i,j] * B[m - 1][j,i]
13:                 B[m][i,i] = W[i,j] * B[m - 1][j,i]
14:                 IF B[m][i,i] >= 1.01
15:                     RETURN B[m][i,i]
16: RETURN 0
{% endhighlight %}

{% highlight asciidoc %}
S(n, W)
 1: B[1] := W
 2: FOR m := 2 TO n
 3:     B[m] := 0
 4:     FOR i := 1 TO n
 5:         FOR j := 1 TO n
 6:             FOR k := 1 TO n
 7:                 IF B[m][i,j] < W[i,k] * B[m - 1][k,j]
 8:                     B[m][i,j] = W[i,k] & B[m - 1][k,j]
 9:                     IF B[m][i,i] >= 1.01
10:                         RETURN B[m][i,i]
11: RETURN 0
{% endhighlight %}

{% highlight asciidoc %}
S'(n, W)
 1: B[1] := W
 2: FOR i := 1 TO n
 3:     FOR j := 1 TO n
 4:         IF W[i,j] > 0
 5:             P[1][i,j] := j 
 6: FOR m := 2 TO n
 7:     B[m] := 0
 8:     FOR i := 1 TO n
 9:         FOR j := 1 TO n
10:             FOR k := 1 TO n
11:                 IF B[m][i,j] < W[i,k] * B[m - 1][k,j]
12:                     B[m][i,j] = W[i,k] & B[m - 1][k,j]
13:                     P[m][i,j] = k
14:                     IF B[m][i,i] >= 1.01
15:                         RETURN m, i, B, P
16: RETURN 0, 0, B, P
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

{% highlight asciidoc %}
global vertex r
global stack path
global stack banned
global map banned_map

TarjanSimpleCycles(n, E)
 1: path := empty
 2: banned := empty
 3: FOR v := 1 TO n
 4:     banned_map[v] := FALSE
 5: FOR r := 1 TO n
 6:     Backtrack(r, n, E)
 7:     WHILE banned is not empty
 8:         v := POP(banned)
 9:         banned_map[v] := FALSE

Backtrack(v, n, E)
 1: found_cycle := FALSE
 2: PUSH(path, v)
 3: PUSH(banned, v)
 4: banned_map[v] := TRUE
 5: FOR w := r TO n
 6:     IF (v, w) not in E
 7:         CONTINUE
 8:     IF w = v
 9:         found_cycle := TRUE
10:         PRINT_PATH(path)
11:     ELSE IF not banned_map[w]
12:         found_cycle := found_cycle || Backtrack(r, w, n, E)
13: IF found_cycle
14:    WHILE PEEK(banned) != w
15:        w := pop(banned)
16:        banned_map[w] := FALSE
17:    POP(banned)
18:    banned_map[v] := FALSE
19: POP(path)
20: RETURN found_cycle
{% endhighlight %}


# Summary

# How to count cycles for a given complete graph

The number of cycles (simple and not simple) for `Kn` of length `n` or less is given by `C(n)`.

{% highlight asciidoc %}
C(i, n, l) = 0                                  if l < 2
C(i, n, l) = (n - i)^(l - 1) - C(i, n, l - 1)   otherwise
{% endhighlight %}

{% highlight asciidoc %}
C(n, l) = C(1, n, l) + C(2, n, l) + ... + C(n, n, l)
{% endhighlight %}

{% highlight asciidoc %}
C(n) = C(n, 1) + C(n, 2) + ... + C(n, n)
{% endhighlight %}

{% highlight ocaml %}
#!/usr/bin/env ocaml

#load "nums.cma"

include Big_int

let rec pow b e =
  if e = 0 then unit_big_int
  else mult_big_int (big_int_of_int b) (pow b (e - 1))

let rec c i n l =
  if l < 2 then zero_big_int
  else sub_big_int (pow (n - i) (l - 1)) (c i n (l - 1))

let rec iter n f acc =
  if n = 0 then acc
  else iter (n - 1) f (f n acc)

let c n l =
  iter n (fun i acc -> add_big_int acc (c i n l)) zero_big_int

let c n =
  iter n (fun l acc -> add_big_int acc (c n l)) zero_big_int

let rec upto m n f =
  if m == n then f m
  else begin f m; upto (m + 1) n f end

let rec format_number s =
  let l = String.length s in
  if l <= 3 then s
  else
    let prefix = String.sub s 0 (l - 3) in
    let suffix = String.sub s (l - 3) 3 in
      Printf.sprintf "%s,%s" (format_number prefix) suffix

let (>>) x f = f x

let count n =
  let count = c n >> string_of_big_int >> format_number in
  Printf.printf "K%2d %40s\n" n count

let _ = upto 2 20 count
{% endhighlight %}


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
