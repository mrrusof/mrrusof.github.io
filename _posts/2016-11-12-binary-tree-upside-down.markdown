---
layout: post
title: 'Binary Tree Upside Down'
date: 2016-11-12
author: Ruslan Ledesma-Garza
summary: "
A popular problem associated with Leetcode's online judge.
You are given binary trees of a very particular form and you are asked to flip them upside down.
A problem not too easy and not too hard.
"
---

{{page.summary}}

# Problem

Consider the following binary tree.

{% highlight asciidoc %}
    1
   / \
  2   3
 / \
4   5
{% endhighlight %}

All right nodes in the tree are leaf nodes and have a sibling.
The corresponding upside down tree is the following.

{% highlight asciidoc %}
   4
  / \
 5   2
    / \
   3   1
{% endhighlight %}

In the upside down tree, the original right nodes are left nodes and all left nodes are leaf nodes.

**Input.**
The first line of input indicates the root node.
Each subsequent line indicates a parent and its two children.
Each line consists of three integers separated by one space.
The first number is a parent node, the second is the left child of the parent, and the third number is the right node.
When a child does not exist, the corresponding integer is -1.
For example, the following input corresponds to the original tree.

{% highlight asciidoc %}
1
1 2 3
2 4 5
{% endhighlight %}

Input is terminated by an EOF character.
All trees are such that all right nodes are leaf nodes and have a sibling.

**Output.**
The first line of input indicates the root of the upside down tree.
Each line of output consists of three integers separated by one space and indicate a parent and its two children like in the input.
For example, the following input corresponds to the upside down tree.

{% highlight asciidoc %}
4
2 3 1
4 5 2
{% endhighlight %}

Output is terminated by an EOF character.

# Solution

Here is a Ruby implementation.

{% highlight ruby %}
def flip r, pp
  nr = r
  npp = {}
  while !!pp[r]
    nr = pp[r][0]
    npp[nr] = [pp[r][1], r]
    r = nr
  end
  return nr, npp
end

def main
  pp = {}
  r = readline.strip rescue return
  while true
    i = readline.strip.split ' ' rescue break
    pp[i[0]] = i[1..2]
  end
  nr, npp = flip r, pp
  puts nr
  npp.each { |k, v| puts "#{k} #{v[0]} #{v[1]}" }
end

main
{% endhighlight %}


{% include subscribe.html %}
{% include share.html %}

{% include comments.html %}
