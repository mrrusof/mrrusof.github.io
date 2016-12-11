---
layout: post
title: Unique Substrings in Wraparound String
date: 2016-12-10
author: Ruslan Ledesma-Garza
summary: "
Given a strings `s` and `p`, find how many unique non-empty substrings
of `p` appear in `s`.
"
---

{{page.summary}}

Consider the string `s` to be the following infinite repetition of string
"abcdefghijklmnopqrstuvwxyz".

{% highlight asciidoc %}
"abcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcd...."
{% endhighlight %}

String `p` consists of only lowercase English letters and up to 10000 letters.

**Input.**
The input file consists of one or more input strings, one per line and
is terminated by an EOF character. Consider the following example.

{% highlight asciidoc %}
a
cac
zab
{% endhighlight %}

**Output.**
The output file consists the count of unique non-empty substrings for
each input string. The following example corresponds to the previous
example input.

{% highlight asciidoc %}
1
2
6
{% endhighlight %}

# Solution

For a given input, we count the unique non-empty substrings by
counting the substrings that end with each given letter in the
following way.

Consider input string "zab" and its substrings.

{% highlight asciidoc %}
z
a
b
za
ab
zab
{% endhighlight %}

The input string is a substring of `s` and for that reason all of its
substrings appear in `s`. Therefore, the output for "zab" is the count
of substrings of "zab", which is 6.

To determine the count of substrings of "zab", we determine how many
of those substrings end with each letter in "zab" and add the
results.
For each letter in "zab", the corresponding substrings are the
following.

{% highlight asciidoc %}
substrings that end with z:
  z
substrings that end with a:
  a
  za
substrings that end with b:
  b
  ab
  zab
{% endhighlight %}

For each letter in "zab", the count of substrings that end with the
letter is given by the position of the letter in "zab". For example,
for letter "b", the count is 3.
The reason is that there is a substring that ends in "b" for each
position that precedes or is equal to the position of letter "b".

When the input string `p` is not a substring of `s`, the count of
substrings that end with a given letter is the maximum count amongst
the substrings of `s` that appear in `p` and are longest.

Consider input string "zabyza".
Substrings "zab" and "yza" appear in `s` and are longest.
Given that letter "b" appears only in "zab", the count of substrings
that end with "b" is given only by "zab".
In the case of letter "y", the count is given by "yza".
For letters "a" and "z", consider the corresponding substrings in
"zab" and "yza".

For "yza", the substrings that end with "a" and "z" are the
following.

{% highlight asciidoc %}
substrings that end with z:
  z
  yz
substrings that end with a:
  a
  za
  yza
{% endhighlight %}

The count of substrings that ends with "a" is 3 because the maximum
count between "zab" and "yza" is 3.
The reason is that all substrings given by "zab" that end with "a" are
also given by "yza".
In the case of "z", the count is 2 and the maximum is also given by "yza".

To determine the substrings of `s` in `p` that are longest, we
group each letter in `p` with its predecessor when either of the
following conditions is true.

1. The letter is 'a' and the predecessor letter is 'z'.
2. The predecessor letter is the previous letter in the alphabet.

Finally, when a letter is repeated in a longest substring, the count
of substrings that end in the letter corresponds to the last position
of the letter. For example, consider input string
"abcdefghijklmnopqrstuvwxyza" and letter "a". The count of substrings
that end with "a" is 27, which is the last position of letter "a".

# Ruby implementation

{% highlight ruby %}
def unique p
  pc = nil
  sl = 0
  ll = {}
  p.chars.each do |c|
    if pc != nil and (pc == 'z' and c == 'a' or pc.succ == c)
      sl += 1
    else
      sl = 1
    end
    ll[c] ||= 1
    ll[c] = [ll[c], sl].max
    pc = c
  end
  return ll.to_a.reduce(0) { |s,v| s+v[1] }
end

while true
  p = readline.strip rescue break
  puts unique(p)
end
{% endhighlight %}

# Python implementation

{% highlight python %}
def unique(p):
  pc = None
  sl = 0
  ll = {}
  for c in p:
    if pc != None and (ord(pc) + 1 == ord(c) or (pc == 'z' and c == 'a')):
      sl += 1
    else:
      sl = 1
    ll[c] = max([ll[c], sl]) if c in ll else sl
    pc = c
  s = 0
  for _, v in ll.items():
      s += v
  return s

with open('/dev/stdin', 'rt') as f:
  line = f.readline()
  while line:
    print unique(line.rstrip())
    line = f.readline()
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
        this.page.url = 'http://ruslanledesma.com/2016/12/10/unique-substrings-in-wraparound-string.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-12-10-unique-substrings-in-wraparound-string'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
