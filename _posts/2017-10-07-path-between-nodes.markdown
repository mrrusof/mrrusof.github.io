---
layout: post
title: Path between Nodes
date: 2017-10-07
author: Ruslan Ledesma-Garza
summary: Given an undirected graph, determine if there is a path from one node to another.
---

{{page.summary}}

**Input.**
The input consists of one or more graph specifications.  Each
specification consists of three parts.  The first part is a line with
the source and target nodes, separated by one space.  The second part
is a line consisting of integer `n`, the number of edges in the graph.
The third part consists of `n` edges.  Each edge is given on a line
and consists of a pair of nodes separated by spaces.  All nodes are integers.

{% highlight asciidoc %}
1
0 4
4
0 1
0 2
1 3
3 4
0 9
{% endhighlight %}

**Output.**
For each graph, output `true` when there is a path between
the source and target nodes, output `false` otherwise.

{% highlight asciidoc %}
true
{% endhighlight %}

# O.K. solution

Apply [Breadth-first Search](https://en.wikipedia.org/wiki/Breadth-first_search).

# Implementation of O.K. solution

Here is a Ruby implementation.

{% highlight ruby %}
#!/usr/bin/env ruby

def bfs s, t, g
  return true if s == t
  v = {s => true}
  frontier_queue = [s]
  while not frontier_queue.empty?
    p = frontier_queue.delete_at 0
    g[p].each do |child|
      next if v[child]
      return true if child == t
      v[child] = true
      frontier_queue << child
    end
  end
  return false
end

def read_graph
  n = readline.to_i
  g = {}
  while (n -= 1) >= 0
    a, b = readline.strip.split(' ').map(&:to_i)
    g[a] ||= []
    g[a] << b
    g[b] ||= []
    g[b] << a
  end
  return g
end

n = readline.to_i

while (n -= 1) >= 0
  s, t = readline.strip.split(' ').map(&:to_i)
  g = read_graph
  puts bfs s, t, g
end
{% endhighlight %}

# Solution

Apply [Bidirectional Search](https://en.wikipedia.org/wiki/Bidirectional_search).

# Implementation of solution

Here is a Ruby implementation.

{% highlight ruby %}
#!/usr/bin/env ruby

def bfs_step fq, vs, vt, g
  p = fq.delete_at 0
  g[p].each do |child|
    next if vs[child]
    return true if vt[child]
    vs[child] = true
    fq << child
  end
  return false
end

def bidirectional_search s, t, g
  return true if s == t
  vs = {s => true}
  vt = {t => true}
  fs = [s]
  ft = [t]
  while !(fs.empty? || ft.empty?)
    return true if bfs_step fs, vs, vt, g
    return true if bfs_step ft, vt, vs, g
  end
  return false
end

def read_graph
  n = readline.to_i
  g = {}
  while (n -= 1) >= 0
    a, b = readline.strip.split(' ').map(&:to_i)
    g[a] ||= []
    g[a] << b
    g[b] ||= []
    g[b] << a
  end
  return g
end

n = readline.to_i

while (n -= 1) >= 0
  s, t = readline.strip.split(' ').map(&:to_i)
  g = read_graph
  puts bidirectional_search s, t, g
end
{% endhighlight %}

{% include subscribe.html %}
{% include share.html %}

{% include comments.html %}
