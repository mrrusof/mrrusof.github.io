---
layout: post
title: 'Longest substring with at most k different characters'
date: 2016-11-29
author: Ruslan Ledesma-Garza
summary: "
For a given string, find the length of the longest substring that
contains at most `k` different characters.
"
---

{{page.summary}}

For example, for string "abacd" and at most 2 characters, the longest
substring is "aba" and thus the answer is 3.

Your task is to write a method `longest` that takes an integer `k` and a string
and gives the longest substring that contains at most `k` different
characters.

**Input and output.**
The signature of the method is `longest(k, s)`, where `k` is an integer
and `s` is a string. The return value is an integer.

Consider the following test cases.

{% highlight asciidoc %}
0  == longest 2, ''                   
1  == longest 2, 'x'                  
2  == longest 2, 'xy'                 
2  == longest 2, 'xyz'                
3  == longest 2, 'aaa'                
3  == longest 2, 'baa'                
3  == longest 2, 'aba'                
3  == longest 2, 'aab'                
4  == longest 2, 'aabb'               
4  == longest 2, 'abab'               
4  == longest 2, 'abba'               
4  == longest 2, 'baba'               
4  == longest 2, 'bbaa'               
4  == longest 2, 'xaabb'              
3  == longest 2, 'axabb'              
3  == longest 2, 'aaxbb'              
3  == longest 2, 'aabxb'              
4  == longest 2, 'aabbx'              
5  == longest 2, 'aabbb'              
5  == longest 2, 'ababb'              
5  == longest 2, 'abbab'              
5  == longest 2, 'abbba'              
3  == longest 2, 'eceba'              
10 == longest 2, 'abcbbbbcccbdddadacb'
11 == longest 3, 'abcadcacacaca'      
{% endhighlight %}

# Solution

Here is a Ruby implementation.

{% highlight ruby %}
def longest k, s
  cc = {}
  i = 0
  max_len = 0
  s.chars.each_with_index do |c, j|
    if cc.has_key? c
      cc[c] += 1
    else
      max_len = [max_len, j - i].max
      if cc.length == k
        while cc[s[i]] != 1
          cc[s[i]] -= 1
          i += 1
        end
        cc.delete s[i]
        i += 1
      end
      cc[c] = 1
    end
  end
  return [max_len, s.length - i].max
end
{% endhighlight %}


{% include share.html %}

{% include comments.html %}
