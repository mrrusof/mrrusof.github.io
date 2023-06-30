---
layout: post
title: The Large Directory
date: 2017-04-10
author: Ruslan Ledesma-Garza
summary: '
You are given a bucket with four directories in a very limited
imitation of Amazon S3.  Your task is to find the directory that has
more files than any of the other three by issuing a given <a
href="https://aws.amazon.com/cli/">CLI</a> command only once.
'
---

{{page.summary}}

The structure of the directories is illustrated by the following
diagram.

<img src="/assets/2017.04.10.bucket-structure.jpg" alt="Bucket Structure" style="width:80%; display: block; margin-left: auto; margin-right: auto;" />

Three of the four directories contain four subdirectories and each of
those subdirectories contains 1000 files.  The remaining directory
contains four subdirectories and each of those subdirectories contains
1001 files.

You may only use CLI command `count`.  The Syntax of the command is
`count <file list>`.  An element of the file list may be a file, a
directory or a pattern.  A pattern may consist of any glob pattern,
regular expression, and brace expansion that Bash accepts (reference
[here](http://mywiki.wooledge.org/BashGuide/Patterns)). The command
returns the file count that corresponds to the file list.  For
example, when the subdirectories of directory `A` contain 1000 files
each, the output of command `count /A/1 /A/2` is 2000.  The following
command uses brace expansion and gives the same output: `count
/A/{1,2}`.


# Solution

<details>
<summary>Click here to see solution.</summary>

We compose the following command that returns a different file count
for each possible case.

{% highlight asciidoc %}
count /B/1 /C/1 /C/2 /D/1 /D/2 /D/3
{% endhighlight %}

The cases and their corresponding file count are the following.

{% highlight asciidoc %}
 Directory that has more files  |  File count
----------------------------------------------
            A                   |     6000
            B                   |     6001
            C                   |     6002
            D                   |     6003
{% endhighlight %}

By inspecting the output of a single run of the command, we determine
the directory that has more files.

</details>
<br>


{% include share.html %}

{% include comments.html %}