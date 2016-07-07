---
layout: post
title: 'ProTip: Redirect stdout and stderr to console and different
files in Bash'
date: 2016-03-08
author: Ruslan Ledesma-Garza
summary: 'When you troubleshoot command execution, sometimes you need
to analyze standard output and standard error side by side. Here is
how to distribute output amongst multiple files and the console.'
---

From time to time I need to troubleshoot the execution of a command
that produces verbose output.
Redirecting output to a file is fundamental to analyzing the
execution.
However, sometimes I also need to get immediate feedback that the
command is running.
In that case, it is usually enough to send all output to the console
and either all or part of it to a file.
But what if you need to look at more than one aspect of the output and
thus you need that output in more than one file?

# Output to a single file

Sending output to the console and to a file is precisely the purpose
of `tee`.
Command `tee` reads from standard input and outputs what reads to
the console and to a given file.
Consider the following program that outputs to standard output and
standard error.

{% highlight bash %}
(echo 'ok'; echo 'error' >&2)
{% endhighlight %}

I send standard output and error to the console and only standard
output to a file by means of `tee` in the following way.

{% highlight bash %}
(echo 'ok'; echo 'error' >&2) | tee out.log
{% endhighlight %}

I send both outputs to the console and to the same file in the
following way. 

{% highlight bash %}
(echo 'ok'; echo 'error' >&2) 2>&1 | tee all.log
{% endhighlight %}

I can even send both outputs to the console and only standard error to
a file like so.

{% highlight bash %}
(echo 'ok'; echo 'error' >&2) 3>&2 2>&1 1>&3 | tee err.log
{% endhighlight %}

Sometimes troubleshooting is easier when you look at standard output
and error separately. How do you do that?

# Output to two or more files

I can send standard output and standard error to the console and to a
file by running two instances of `tee`.
I do so by means of [process
substitution](https://www.gnu.org/software/bash/manual/bashref.html#Process-Substitution).
To illustrate process substitution, consider the following code that
sends standard error and standard output to separate files.

{% highlight bash %}
(echo 'ok'; echo 'error' >&2)  2> err.log > out.log
{% endhighlight %}

I send standard error and standard output the console and to separate
files in like so.

{% highlight bash %}
 (echo 'ok'; echo 'error' 1>&2) 2> >(tee err.log) > >(tee out.log)
#-----------------------------       -----------      -----------
#            CMD                         ERR              OUT
{% endhighlight %}

What the previous line of Bash does is execute commands `CMD`, `ERR`,
and `OUT` and connect the standard error of `CMD` to the input of `ERR`
and the standard output of `CMD` to the input of `OUT`.

Sometimes I still need to put error output in context.
In that case, I send standard error and standard output to the
console, to separate files, and to the same file like this.

{% highlight bash %}
(echo 'ok'; echo 'error' 1>&2) 2> >(tee err.log) > >(tee out.log) | tee all.log
{% endhighlight %}

When I only need to look at errors and put them in context, I do the
following.
I send standard error and standard output to the console and to the
same file.
While doing that, I send standard error to a separate file.
The following Bash line illustrates the technique.

{% highlight bash %}
(echo 'ok'; echo 'error' 1>&2) 2> >(tee err.log) | tee all.log
{% endhighlight %}

Do you have any interesting techniques for redirecting output? Let me
know in the comments.


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
        this.page.url = 'http://ruslanledesma.com/2016/03/08/protip-redirect-stdout-stderr-separate-files.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-03-08-protip-redirect-stdout-stderr-separate-files'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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


