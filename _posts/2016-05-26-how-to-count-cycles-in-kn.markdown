---
layout: post
title: How to count cycles in a complete graph
date: 2016-05-26
author: Ruslan Ledesma-Garza
summary: An example of a question whose answer is an exponential function.
---

The count of cycles (simple and not simple) of length `n` or less for `Kn` is given by the following function `C(n)`.
The count consists of the count of cycles for each length `2 <= l <= n` given by `C(n, l)`.
The count for a given length `l` consists of the count of cycles (simple and not simple) from each vertex `1 <= i <= n` given by `C(i, n, l)`.

{% highlight asciidoc %}
C(n) = C(n, 2) + C(n, 3) + ... + C(n, n)

C(n, l) = C(1, n, l) + C(2, n, l) + ... + C(n, n, l)

C(i, n, l) = 0                                  if l < 2
C(i, n, l) = (n - i)^(l - 1) - C(i, n, l - 1)   otherwise
{% endhighlight %}

The following OCaml program counts the number of cycles for complete graphs K2 to K20.

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

To run the program in Linux or OSX, install the OCaml interpreter, save the program in a file, and grant execution permission to the file.
In OSX with [Homebrew](http://brew.sh/), when you save the program in file `count-cycles.ml` you install and run the program as follows.

{% highlight asciidoc %}
ruslan$ brew install ocaml
==> Downloading https://homebrew.bintray.com/bottles/ocaml-4.02.3.el_capitan.bottle.tar.gz
...
ruslan$ chmod a+x count-cycles.ml
ruslan$ ./count-cycles.ml
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
        this.page.url = 'http://ruslanledesma.com/2016/05/26/how-to-count-cycles-in-kn.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-05-26-how-to-count-cycles-in-kn'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
