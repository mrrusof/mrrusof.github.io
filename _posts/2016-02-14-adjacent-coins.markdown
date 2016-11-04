---
layout: post
title: The Adjacent Coins Problem
date: 2016-02-14
edited: 2016-11-04
author: Ruslan Ledesma-Garza
summary: Choose a coin that maximizes your gain or minimizes your loss, but you have to do it in linear time and constant memory.
---

Here is a problem that I considered solved for some months: The Adjacent
Coins Problem.  This is a problem where you have to choose a coin that
maximizes your gain or minimizes your loss, but you have to do it in
linear time and constant memory.
Then, around three months ago, [vladon reviewed my
code](https://github.com/mrrusof/algorithms/issues/1).  I was
impressed.

This weekend, I finally put some time together and came up with the
following solution.


# Problem

Consider N coins aligned in a row. Each coin is showing either heads
or tails. The adjacency of these coins is the number of adjacent pairs
of coins with the same side facing up.

Write a C++ function

{% highlight cpp %}
int solution(const vector<int> &A);
{% endhighlight %}

that, given a non-empty zero-indexed array `A` consisting of N integers
representing the coins, returns the maximum possible adjacency that
can be obtained by reversing exactly one coin (that is, **one of the
coins must be reversed**). Consecutive elements of array `A` represent
consecutive coins in the row. Array `A` contains only 0s and/or 1s:

- 0 represents a coin with heads facing up;
- 1 represents a coin with tails facing up.

For example, given array `A` consisting of eight numbers, such that:

{% highlight cpp %}
A[0] = 1
A[1] = 1
A[2] = 0
A[3] = 1
A[4] = 0
A[5] = 0
A[6] = 1
A[7] = 1
{% endhighlight %}

the function should return 5. The initial adjacency is 3, as there are
three pairs of adjacent coins with the same side facing up, namely (0,
1), (4, 5) and (6, 7). After reversing the coin represented by `A[2]`,
the adjacency equals 5, as there are five pairs of adjacent coins with
the same side facing up, namely: (0, 1), (1, 2), (2, 3), (4, 5) and
(6, 7), and it is not possible to obtain a higher adjacency.

The same adjacency can be obtained by reversing the coin represented
by `A[3]`.

Assume that:

- N is an integer within the range [1..100,000];
- each element of array A is an integer within the range [0..1].

Complexity:

- expected worst-case time complexity is O(N);
- expected worst-case space complexity is O(1), beyond input storage
  (not counting the storage required for input arguments). 

# Analysis

Given a sequence of coins, the adjacency of the sequence either
changes or remains the same when we flip a coin because we either gain
pairs, lose pairs, or keep the same amount of pairs.  Consider the
following sequence and the delta adjacency for each coin.

{% highlight asciidoc %}
Index  | Coin | Delta adjacency
-------------------------------
0      | 1    | -1
1      | 1    |  0
2      | 0    |  2
3      | 1    |  2
4      | 0    |  0
5      | 0    |  0
6      | 1    |  0
7      | 1    | -1
{% endhighlight %}

The adjacency for the sequence is 3.  If we flip the first or last
coin, we lose one pair and the adjacency becomes 2.  If we flip
coin 2 or 3, we gain two pairs and the adjacency becomes 5.
Flipping one of the other coins destroys a pair and creates another
pair, so the adjacency does not change if we flip any of them.

Because **we must flip a coin**, we choose the coin to flip in the
following way.  When we can gain pairs, we choose to flip a coin that
causes the biggest gain.  When we cannot gain pairs, we choose to flip
a coin that causes the smallest loss. That way we get the maximum
number of pairs possible.  For the sequence, we flip coin 2, which
gives the maximum possible adjacency 5.  Coin 3 is an alternative.

# Solution

When the given sequence consists of one coin we return 0.  For this
case, 0 is always the right answer because adjacency is 0 and remains 0
after flipping the coin.

When the sequence has more coins, we compute the adjacency of the
sequence, compute the maximum delta adjacency, and return the sum
of these two values.

We compute the adjacency of the sequence by counting adjacent pairs.
We count adjacent pairs by iterating the sequence and inspecting the
current coin and the previous.  At each iteration, the current and
previous coins either form a pair or not.  Each time the current coin
and the previous show the same side, we count one pair.  Our
implementation is the following.

{% highlight cpp %}
int adjacency(const vector<int> &A) {
  int adjacency = 0;
  int previous = -1;
  for(int i = 0; i < A.size(); i++) {
    if(previous == A[i]) adjacency++;
    previous = A[i];
  }
  return adjacency;
}
{% endhighlight %}

We compute the maximum delta adjacency by computing the delta
adjacency for each coin and keeping the maximum.  For the first and
last coins, we apply the following rule and keep the maximum.

{% highlight asciidoc %}
The first or last coin
| Adjacent coin
|/      Delta adjacency
||     /
||     |
vv     v

00 -> -1
01 ->  1
10 ->  1
11 -> -1
{% endhighlight %}

When there are coins in the middle, we apply the following rule to
each coin and update the maximum delta.

{% highlight asciidoc %}
Coin to the left
| A coin in the middle
|/ Coin to the right
||/      Delta adjacency
|||     /
|||     |
vvv     v

000 -> -2
001 ->  0
010 ->  2
011 ->  0
100 ->  0
101 ->  2
110 ->  0
111 -> -2
{% endhighlight %}

Our implementation is the following.

{% highlight cpp %}
int max(int x, int y) {
  if(x < y) return y;
  return x;
}

int maximum_delta_adjacency(const vector<int> &A) {
  int maximum;
  int last_position = A.size() - 1;
  maximum = A[0] == A[1] ? -1 : 1;
  maximum = max(A[last_position] == A[last_position-1] ? -1 : 1, maximum);
  int middle[2][2][2] = { { {-2, 0}, {2, 0} }, { {0, 2}, {0, -2} } };
  for(int i = 1; i < last_position; i++) {
    maximum = max(middle[A[i-1]][A[i]][A[i+1]], maximum);
  }
  return maximum;
}
{% endhighlight %}

The last touch is adding the adjacency and the maximum delta
adjacency in function `solution` as follows.

{% highlight cpp %}
int solution(const vector<int> &A) {
  if(A.size() == 1) return 0;
  return adjacency(A) + maximum_delta_adjacency(A);
}
{% endhighlight %}

We illustrate usage of `solution` with the following program.

{% highlight cpp %}
#include <iostream>
#include <vector>

using namespace std;

int adjacency(const vector<int> &A) {
...
}

int max(int x, int y) {
...
}

int maximum_delta_adjacency(const vector<int> &A) {
...
}

int solution(const vector<int> &A) {
...
}

void print_vector(const vector<int> &A) {
  cout << A[0];
  for(int i = 1; i < A.size(); i++)
    cout << ", " << A[i];
  cout << "\n";
}

void solve_vector(const int vA[], const int N) {
  vector<int> A(&vA[0], &vA[0]+N);
  print_vector(A);
  cout << "Maximum possible adjacency is " << solution(A) << ".\n";
}

int main() {
  int vA[] = {0};
  solve_vector(vA, 1);
  int vB[] = {0, 0};
  solve_vector(vB, 2);
  int vC[] = {0, 1};
  solve_vector(vC, 2);
  int vD[] = {0, 1, 0, 1, 0, 0, 1, 0};
  solve_vector(vD, 8);
  int vE[] = {1, 1, 0, 1, 0, 0, 1, 1};
  solve_vector(vE, 8);
  return 0;
}
{% endhighlight %}


# Discussion

The first time I tried to solve the problem, I had the impression that
a solution involved ranking the coins and selecting the highest rank.
So I created the two ranking rules.  During my second attempt, my
biggest obstacle was figuring how to apply the rules to sequences of
any length.  Then, I figured that I could handle sequences of length
1, length 2, and the rest separately.  Afterwards, I figured that I
only had to handle sequences of length 1 and the rest.

What was your experience trying to solve the problem?  Let me know in
the comments.


# A constant-memory solution

Thanks to [sltkr](https://www.reddit.com/user/sltkr) for posting the
following solution on
[reddit](https://www.reddit.com/r/coding/comments/56jr67/programming_problem_adjacent_coins/).

> Your analysis makes sense.
>
> My take was that the "adjacency number" is just the number of coins
  minus the number of runs (where a "run" is a maximal sequence of equal
  values in the input. For example, "0100" consists of three runs: "0",
  "1" and "00", and the "adjacency number" is thus 4 - 3 =
  1). Maximizing the adjacency number means minimizing the number of
  runs, and the only way to do that is by flipping coins at the edge of
  runs.
>
> To summarize:
>
> * If we have a run of length 1 between other runs, then we can improve
  the answer by 2. Example: "00100" -> "00000".
> * If we have a run of length 1 at the beginning or end of the sequence,
  we can only improve the answer by 1. Example: "1001" -> "0001".
> * If we have several runs, all of length 2 or more, then the best we can
  do is flip a coin between runs without changing the answer. Example:
  "0011" -> "0001".
> * If we have only one run (i.e. all coins shows the same value) then
  flipping a coin makes things worse! The best we can do is flip a coin
  at the edge, for an "improvement" of -1. Example: "00000" -> "00001".
> * Exception to the above: if we only have 1 coin, then flipping it
  doesn't make a difference. The answer is always 0. To be honest, I
  missed the case at first, and I see you made the same mistake. :-)
>
> In code, this becomes:
>
> {% highlight cpp %}
int solve(int N) {  // assumes N > 0
    int pairs = 0;     // number of pairs in the input
    int improve = -1;  // maximum improvement possible
    int a, b, c;       // values of last three coins
    cin >> c;
    for (int i = 1; i < N; ++i) {
        a = b;
        b = c;
        cin >> c;
        if (b == c) {
            ++pairs;
        } else if (i >= 2 && a != b) {
            improve = max(improve, 2);
        } else if (i == 1 || i == N - 1) {
            improve = max(improve, 1);
        } else {
            improve = max(improve, 0);
        }
    }
    return N == 1 ? 0 : pairs + improve;
}

int main() {
    int N = 0;
    cin >> N;
    cout << solve(N) << endl;
}
{% endhighlight %}
>
> Note that this function combines the calculation of the number of
  pairs in the input with the maximum improvement, and it only keeps
  track of the values of the last three coins in the input, thus
  making it a truly constant-memory solution.


# Acknowledgements

Special thanks to [Vladislav Yaroslavlev
(vladon)](https://github.com/vladon) for reviewing my original attempt
at solving the problem.

Many thanks to [María
Rabelero](https://mx.linkedin.com/in/mariarabelero/en) and [Jan
Křetínský](https://www7.in.tum.de/~kretinsk/) for their valuable
feedback on an early version of this post and my solution.

Jan included a valuable insight in his review. When we look for
the maximum gain or minimum loss, we can stop iterating the coins in
the middle as soon as we find a sequence `010` or `101`.


{% include subscribe.html %}
{% include share.html %}


# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    var disqus_config = function () {
        this.page.url = 'http://ruslanledesma.com/2016/02/14/adjacent-coins.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-02-14-adjacent-coins'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    (function() {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');

        s.src = '//definecode.disqus.com/embed.js';

        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
