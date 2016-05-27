---
layout: post
title: Arbitrage
date: 2016-05-26
author: Ruslan Ledesma-Garza
summary: Do you want to make easy money? Maybe currency trading is what you are looking for.
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
1.00 in U.S. currency buys 0.7 British pounds currency, 1.00 in British
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

For a given problem, the conversion table corresponds to a graph and the solution corresponds to a shortest profitable cycle in that graph.
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
<br />

<a name="lassos-are-redundant" />
When we interpret the conversion table, we write 0 for the rates in the diagonal to indicate that we do not consider edges that start and end in the same vertex.
The reason is that those edges are redundant.
A lasso is an edge that starts and ends in the same vertex.
Lassos are redundant because from a given sequence that includes a lasso you obtain a shorter sequence with the same rate by removing the lasso.
For example, sequence `USD USD EUR USD` yields profit 1.01, the same profit that the shorter sequence `USD EUR USD` yields.


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
<br />


A cycle is profitable when its rate is greater or equal to 1.01.
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


A solution that is not a simple cycle is not necessarily the repetition of a simple cycle like in the previous example.
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
Even if there are edges with weight 0, we consider them because considering a complete graph makes for a simpler solution.
The exception are lassos, which we do not consider because [they are redundant](#lassos-are-redundant).
For complete graphs of size `2 <= n <= 20`, [the number of cycles of length `n` or less](/2016/05/26/how-to-count-cycles-in-kn.html) is the following.

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



# <a name='approach' /> Approach

We approach the problem by searching for a shortest profitable cycle amongst a limited number of candidates.
We guarantee that the profitable cycle we find is shortest by considering candidates in order of length.


Consider the following input graph.

<img src="/assets/2016-04-24.approach-example.png" alt="" style="width: 300px; display: block; margin-left: auto; margin-right: auto;" />

The candidates of length 2 are the cycles of length 2.
Thus, we consider the cycles of length 2 from each one of the vertices as illustrated in the following diagram.
These are all the cycles of length 2 because we consider all paths that start and end in each given vertex.

<img src="/assets/2016-04-24.approach-example-cycles-len-2.png" alt="" style="width: 600px; display: block; margin-left: auto; margin-right: auto;" />
<br />


We search for a profitable candidate of length 2 by considering each root and each corresponding child.
For example, for root `1` and child `2`, we consider the following candidate.

<img src="/assets/2016-04-24.approach-example-cycle-1-2-1.png" alt="" style="width: 400px; display: block; margin-left: auto; margin-right: auto;" />

The rate of the candidate is 1.005 which is not profitable and therefore not a solution.
We search the rest of the candidates by repeating the process for each root and child.
We find no profitable candidate of length 2.


Half of the candidates of length 2 are repeated but we consider them anyway.
For example, candidate for root `2` and child `1` (`2 -> 1 -> 2`) is the same cycle and candidate for root `1` and child `2` (`1 -> 2 -> 1`).
The reason we consider repetitions is that when we apply the process to longer candidates, the number of candidates we consider for each length remains 12 while the number of cycles for the length increases.
For example, when we consider candidates of length 4 for the input graph, we consider 12 candidates instead of the 28 cycles of length 4 that exist.


Given that there is no solution of length 2, we search for a profitable candidate of length 3.
The candidates of length 3 are most beneficial cycles of length 3.
The structure of each candidate consists of a prefix edge `i -> j` and a suffix path `j -> k -> i`.
For example, the candidate for root `1` and child `2` is the following.

[<img src="/assets/2016-04-24.approach-example-candidate-1-2-3-1.png" alt="" style="width: 700px; display: block; margin-left: auto; margin-right: auto;" />](/assets/2016-04-24.approach-example-candidate-1-2-3-1.png)

The prefix edge `1 -> 2` is the edge from `1` to `2` given by the input graph.
The suffix path `2 -> 3 -> 1` is a most beneficial path of length 2 from `2` to `1`.
In this case the suffix is the most beneficial path of length 2 from `2` to `1`.
The rate of the candidate is 1 which is not profitable and therefore not a solution.
Given that there is a candidate for each root and child, we search the rest of the candidates by repeating the process for each root and child.
We find no profitable candidate of length 3.

<a name="candidates-are-most-beneficial-paths" />
Candidates are most beneficial paths, for that reason we construct candidates by constructing most beneficial paths.

The construction of most beneficial paths is determined by their structure.
Consider the structure a most beneficial path.
{% highlight asciidoc %}
p = i -> k -> ... -> j
    ------------------
             |
          m edges
{% endhighlight %}
The candidate consists of a prefix edge `i -> k` and a suffix path `k -> ... -> j`.
The suffix path is a most beneficial path of length 2 from `k` to `j` because `p` is a most beneficial path.
If the suffix were not most beneficial, `p` would not be a most beneficial path from `i` to `j` because there would be a suffix from `k` to `j` with a higher rate.
The rate `B'[i,j]` of path `p` is determined by the rate of its edge `W[i,k]` and the rate of its suffix `B[k,j]` as follows.
{% highlight asciidoc %}
B'[i,j] = W[i,k] * B[k,j]
{% endhighlight %}
<br />

The construction of a most beneficial path corresponds to the calculation of its rate.
For given start `i` and end `j` vertices, we obtain the rate `B[m][i,j]` of most beneficial path of length `m` from the rates of most beneficial paths of length `m - 1` as follows.
{% highlight asciidoc %}
B[m][i,j] = max { W[i,k] * B[m - 1][k,j] | k in 1 ... n }
{% endhighlight %}
For the matrix of weights `W` corresponding to the input graph, the rate of most beneficial paths of length 1 `B[1]` is `W`.


It is possible to construct most beneficial paths on demand instead of upfront together with candidates.
We do not explain that approach because [the time complexity of the end-to-end algorithm is the same either way](#algorithm-r).


Given that there is no solution of length 3, we search for a profitable candidate of length 4.
For root 1 and child 2, the candidate is the following.

[<img src="/assets/2016-04-24.approach-example-solution-1-2-1-2-1.png" alt="" style="width: 700px; display: block; margin-left: auto; margin-right: auto;" />](/assets/2016-04-24.approach-example-closeup-solution-1-2-1-2-1.png)

The prefix edge `1 -> 2` is given by the input graph.
The suffix path is the most beneficial path of length 3 from `2` to `1`.
In this case there is only one most beneficial path, `2 1 2 1`.
The rate of the candidate is 1.010025 which is profitable and thus a solution.
We stop and return the solution.
If we repeated the process for the other candidates, we would find no other solution.
Thus, `1 2 1 2 1` is the only solution.



# Algorithm

We present algorithms S, S', and R.
Our solution is algorithm S'.
Algorithm S is a summary of algorithm S' and is easier to understand.
Algorithm R is an alternative to algorithm R that computes most beneficial paths on demand.
The execution time of the three algorithms is `O(n^4)`.

The following is algorithm S.

{% highlight asciidoc %}
S(n, W)
 1: B[1] := W
 2: FOR m := 2 TO n
 3:     B[m] := 0
 4:     FOR i := 1 TO n
 5:         FOR j := 1 TO n
 6:             FOR k := 1 TO n
 7:                 IF B[m][i,j] < W[i,k] * B[m - 1][k,j]
 8:                     B[m][i,j] = W[i,k] * B[m - 1][k,j]
 9:                     IF B[m][i,i] >= 1.01
10:                         RETURN B[m][i,i]
11: RETURN 0
{% endhighlight %}

Algorithm S returns the rate of a solution or zero if there is no solution for given weight matrix `W` of size `n x n`,
Algorithm S finds a solution rate by constructing rates of candidates of increasing length and returning the first rate that is profitable.
Algorithm S constructs rates of candidates by constructing rates of most beneficial paths.
The reason is that [candidates are most beneficial paths](#candidates-are-most-beneficial-paths).


The following is algorithm S'.

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
16: RETURN 0
{% endhighlight %}

Algorithm S' returns a solution and its rate when there is one.
If there is no solution, algorithm S' returns zero.
Algorithm S' computes rates of most beneficial paths in the way algorithm S does and at the same time constructs a solution in successor matrix `P`.
Algorithm S' constructs a solution by recording the successor `k` of vertex `i` in `P[m][i,j]` in line 13.
Algorithm S' stops as soon as it finds a profitable candidate in line 14.


<a name="algorithm-r" />
The following is algorithm R.
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

Algorithm R computes the same result as algorithm S by constructing most beneficial paths on demand.
We include algorithm R to illustrate that the time complexity of constructing most beneficial paths on demand is the same as the complexity of constructing them upfront.



# Implementation

The following C program is our implementation of algorithm S'.
For a given weight matrix `rate` of size `n x n`, function `S` executes algorithm S' and prints a solution if there is one.
If there is no solution, function `S` prints "no arbitrage sequence exists."

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

We present our C implementation of algorithm R for comparison.
Our implementation computes the rate of the solution as well as the solution path.
The main difference is that this program separates the construction of candidates from the construction of most beneficial paths.
In function `R`, the construction of candidates happens in if block `C` and the construction of most beneficial paths happens in loop `P`.
The separation corresponds to the construction of most beneficial paths on demand that we explained in section [Approach](#approach).

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
        for(k = i; k < n; k++)  /* ....................................... P */
          if(benefit[l - 1][j][i] < rate[j][k] * benefit[l - 2][k][i]) {
            benefit[l - 1][j][i] = rate[j][k] * benefit[l - 2][k][i];
            succ[l - 1][j][i] = k;
          }
        if(benefit[l][i][i] < rate[i][j] * benefit[l - 1][j][i]) { /* .... C */
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

The worst time complexity of function `R` is the same as that of function `S`, `O(n^4)`.
The [UVa Online Judge](https://uva.onlinejudge.org/index.php) reports the same execution time for both programs, 0.010 seconds.
We obtained 8th place by submitting function `R`.
Subsequent submission of function `S` produced the same execution time.

<img src="/assets/2016-04-24.uva-verdict.png" alt="" style="width: 700px; display: block; margin-left: auto; margin-right: auto;" />



<!--
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
-->



# Summary





<!--
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
-->



# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    var disqus_config = function () {
        this.page.url = 'http://ruslanledesma.com/2016/05/26/arbitrage.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-05-26-arbitrage'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    (function() {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');

        s.src = '//definecode.disqus.com/embed.js';

        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a
        href="https://disqus.com/?ref_noscript"
        rel="nofollow">comments powered by Disqus.</a></noscript>
