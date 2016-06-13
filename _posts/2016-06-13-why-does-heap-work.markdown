---
layout: post
title: Why does Heap's algorithm work?
date: 2016-06-13
author: Ruslan Ledesma-Garza
summary: Permutations are easy, right?
---

Example 1.

Consider the input sequence `1234`

{% highlight asciidoc %}
 1 |  | 12
 2 |--| 21 N =     2
{% endhighlight %}

{% highlight asciidoc %}
 1 |  |  | 123
 2 |--|  | 213 N =     2
 3 |-----| 312 N =     3
 4 |--|  | 132 N =     2
 5 |-----| 231 N =     3
 6 |--|  | 321 N =     2
{% endhighlight %}

{% highlight asciidoc %}
 1 |  |  |  | 1234
 2 |--|  |  | 2134 N =     2
 3 |-----|  | 3124 N =     3
 4 |--|  |  | 1324 N =     2
 5 |-----|  | 2314 N =     3
 6 |--|  |  | 3214 N =     2
 7 |--------| 4213 N =     4
 8 |--|  |  | 2413 N =     2
 9 |-----|  | 1423 N =     3
10 |--|  |  | 4123 N =     2
11 |-----|  | 2143 N =     3
12 |--|  |  | 1243 N =     2
13 |  |-----| 1342 N =     4
14 |--|  |  | 3142 N =     2
15 |-----|  | 4132 N =     3
16 |--|  |  | 1432 N =     2
17 |-----|  | 3412 N =     3
18 |--|  |  | 4312 N =     2
19 |  |  |--| 4321 N =     4
20 |--|  |  | 3421 N =     2
21 |-----|  | 2431 N =     3
22 |--|  |  | 4231 N =     2
23 |-----|  | 3241 N =     3
24 |--|  |  | 2341 N =     2
{% endhighlight %}

Heap's Algorithm is the following.
{% highlight asciidoc %}
Heap(S, n)
 10: FOR i := 1 TO n - 1
 20:   IF n > 2 THEN
 30:     Heap(S, n - 1)
 40:   IF n is even THEN
 50:     swap S[i] and S[n]
 60:   ELSE
 70:     swap S[0] and S[n]
 80:   process S
 90: IF n > 2 THEN
100:   Heap(S, n - 1)
{% endhighlight %}

Our implementation is the following.

{% highlight c %}
#include <stdio.h>

#define Ss(s) scanf("%s", s)

#define MAX_LEN 1000

int count;

void print_swap(int a, int b, int n) {
  int i;
  printf("%5d ", count++);
  for(i = 0; i < n - 1; i++)
    if(a == i)
      printf("|--");
    else if(a < i && i < b)
      printf("---");
    else
      printf("|  ");
  printf("|");
}

void swap(char *s, int a, int b) {
  char t = s[a];
  s[a] = s[b];
  s[b] = t;
}

void h(char *s, int n, int len) {
  int i;
  for(i = 0; ; i++) {
    if(n > 2)
      h(s, n - 1, len);
    if(i == n - 1)
      break;
    if(n % 2 == 0) {
      swap(s, i, n - 1);
      print_swap(i, n - 1, len);
    } else {
      swap(s, 0, n - 1);
      print_swap(0, n - 1, len);
    }
    printf(" %s N = %5d\n", s, n);
  }
}

void heap(char *s, int len) {
  if(len < 2) {
    printf("The only permutation is %s\n", s);
    return;
  }
  count = 1;
  print_swap(-1, -1, len);
  printf(" %s\n", s);
  h(s, len, len);
}

int main() {
  char s[MAX_LEN];
  int len;
  Ss(s);
  for(len = 0; s[len] != '\0'; len++);
  heap(s, len);
  return 0;
}
{% endhighlight %}

Consider the enumeration of permutations for sequence `12345`.

{% highlight asciidoc %}
    1 |  |  |  |  | 12345
  ...
   24 |--|  |  |  | 23415 N =     2
   25 |-----------| 53412 N =     5
  ...
   48 |--|  |  |  | 34152 N =     2
   49 |-----------| 24153 N =     5
  ...
   72 |--|  |  |  | 41523 N =     2
   73 |-----------| 31524 N =     5
  ...
   96 |--|  |  |  | 15234 N =     2
   97 |-----------| 45231 N =     5
  ...
  120 |--|  |  |  | 52341 N =     2
{% endhighlight %}

Consider the enumeration of permutations for sequence `123456`.

{% highlight asciidoc %}
    1 |  |  |  |  |  | 123456
  ...
  120 |--|  |  |  |  | 523416 N =     2
  121 |--------------| 623415 N =     6
  ...
  240 |--|  |  |  |  | 123465 N =     2
  241 |  |-----------| 153462 N =     6
  ...
  360 |--|  |  |  |  | 653412 N =     2
  361 |  |  |--------| 652413 N =     6
  ...
  480 |--|  |  |  |  | 152463 N =     2
  481 |  |  |  |-----| 152364 N =     6
  ...
  600 |--|  |  |  |  | 652314 N =     2
  601 |  |  |  |  |--| 652341 N =     6
  ...
  720 |--|  |  |  |  | 452361 N =     2
{% endhighlight %}


Consider sequence `1 2 3 ... n`

- When `n` is even and greater or equal to 4, the last permutation is `(n-2) (n-1) 2 3 ... (n-3) n 1`.
- When `n` is odd and greater or equal to 5, the last permutation is `n 2 3 ... (n-1) 1`.
- After every step for level `n`, a different element reaches the `n`-th position.

Given sequence `1 2 3 ... n` where `n` is even and greater or equal than 4, the last permutation is `(n-2) (n-1) 2 3 ... (n-3) n 1`.

{% highlight asciidoc %}
Swap:              Permutation

  0 :  1     2     3     4    ...  (n-2) (n-1)   n
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
     (n-1)   2     3     4    ...  (n-2)   1     n
  1 :  n     2     3     4    ...  (n-2)   1   (n-1)
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
       1     2     3     4   ...   (n-2)   n   (n-1)
  2 :  1   (n-1)   3     4   ...   (n-2)   n     2
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
       n   (n-1)   3     4   ...   (n-2)   1     2
  3 :  n   (n-1)   2     4   ...   (n-2)   1     3
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
       1   (n-1)   2     4   ...   (n-2)   n     3
  4 :  1   (n-1)   2     3   ...   (n-2)   n     4

     ...

       n   (n-1)   2     4   ...   (n-3)   1   (n-2)
 n-1:  n   (n-1)   2     4   ...   (n-3) (n-2)   1
        \_______________   _______________/
                        \ /
                         \
         _______________/ \_______________
        /                                 \
     (n-2) (n-1)   2     4   ...   (n-3)   n     1
{% endhighlight %}

Each of the elided swaps for position `n` corresponds to either an even or odd count of swaps for position `n`.
For a given swap, when the count of swaps including the swap is even, the shape of the swap is the following.

{% highlight asciidoc %}
  1   (n-1)   3     4    ...  (i-1) (i+1)  ...  (n-2)   n     i
  1   (n-1)   3     4    ...  (i-1)   i    ...  (n-2)   n   (i+1)
{% endhighlight %}

For a given swap, when the count of swaps including the swap is odd, the shape of the swap is the following.

{% highlight asciidoc %}
  n   (n-1)   3     4    ...  (i-1) (i+1)  ...  (n-2)   1     i
  n   (n-1)   3     4    ...  (i-1)   i    ...  (n-2)   1   (i+1)
{% endhighlight %}

The crucial difference between the shap of even and odd swaps is the position of elements `1` and `n`.

The sequence of elements for position `n` is `n, (n-1), 2, 3, 4, ..., (n-2), 1`.

Given sequence `1 2 3 ... n` where `n` is odd and greater or equal than 5, the last permutation is `n 2 3 ... (n-1) 1`.

{% highlight asciidoc %}
Swap:                  Permutation

  0 :   1     2     3    ...  (n-4) (n-3) (n-2) (n-1)   n
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-3) (n-2)   2     3    ...  (n-4) (n-1)   1     n
  1 :    n   (n-2)   2     3    ...  (n-4) (n-1)   1   (n-3)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-4) (n-1) (n-2)   2    ...  (n-5)   1     n   (n-3)
  2 :  (n-3) (n-1) (n-2)   2    ...  (n-5)   1     n   (n-4)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-5)   1   (n-1) (n-2)  ...  (n-6)   n   (n-3) (n-4)
  3 :  (n-4)   1   (n-1) (n-2)  ...  (n-6)   n   (n-3) (n-5)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-6)   n     1   (n-1)  ...  (n-7) (n-3) (n-4) (n-5)
  4 :  (n-5)   n     1   (n-1)  ...  (n-7) (n-3) (n-4) (n-6)

       ...

 n-4:    3     6     7    ...  (n-1) (n-2)   5    4      2
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-2)   5     6     7    ...  (n-1)   4     3     2
 n-3:    2     5     6     7    ...  (n-1)   4     3   (n-2)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
       (n-1)  4      5     6    ...    1     3     2   (n-2)
 n-2:  (n-2)  4      5     6    ...    1     3     2   (n-1)
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
         1    3      4      5   ...    n     2   (n-2) (n-1)
 n-1:  (n-1)  3      4      5   ...    n     2   (n-2)   1
        /    ----------------------- ----------   /
       *                  \              /       /
              _____________\____________/       /
             /              \                  /
            /                \                /     *
       ----------- -----------------------   /     /
         n    2      3      4   ...  (n-3) (n-2) (n-1)   1
{% endhighlight %}

The sequence of elements for position `n` is `n, (n-3), (n-4), (n-5), ..., 2, (n-2), (n-1), 1`.

The sequence of elements for position `i` after the `j`-th swap is.

{% highlight asciidoc %}
        1  |  2  |  3  |  4  | ... | n-3 | n-2 | n-1 |  n
     |------------------------------------------------------
  0  |  1  |  2  |  3  |  4  |     | n-3 | n-2 | n-1 |  n
  1  |  n  | n-2 |  2  |  3  |     | n-4 | n-1 |  1  | n-3
  2  | n-3 | n-1 | n-2 |  2  |     | n-5 |  1  |  n  | n-4
  3  | n-4 |  1  | n-1 | n-2 |     | n-6 |  n  | n-3 | n-5
 ... | ... | ... | ... | ... |     | ... | ... | ... | ...
 n-5 |  4  |  7  |  8  |  9  |     |  2  |  6  |  5  |  3
 n-4 |  3  |  6  |  7  |  8  |     | n-2 |  5  |  4  |  2
 n-3 |  2  |  5  |  6  |  7  |     | n-1 |  4  |  3  | n-2
 n-2 | n-2 |  4  |  5  |  6  |     |  1  |  3  |  2  | n-1
 n-1 | n-1 |  3  |  4  |  5  |     |  n  |  2  | n-2 |  1
{% endhighlight %}


# References

- Eric Martin's lecture notes on Principles of Programming.
https://webcms3.cse.unsw.edu.au/COMP9021/15s2/resources/1544