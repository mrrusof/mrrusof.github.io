---
layout: post
title: "What does the protected access modifier mean?"
date: 2017-01-07
author: Ruslan Ledesma-Garza
summary: "

"
---

<!--

{{page.summary}}

-->

The [Oracle Java
Documentation](http://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html)
defines in the following way the effect of applying the protected
access modifier to a member.

> The protected modifier specifies that the member can only be accessed
  within its own package (as with package-private) and, in addition, by
  a subclass of its class in another package.

That definition is the first search result when I google "java
protected modifier".
The
[second](http://stackoverflow.com/questions/215497/in-java-difference-between-default-public-protected-and-private)
and
[third](http://stackoverflow.com/questions/8637781/what-does-the-protected-modifier-mean)
search results are questions in Stack Overflow that rephrase the
previous definition either in prose or in the form of a table.

Given that definition, you could be forgiven for thinking that there
is nothing wrong with the following code.

{% highlight java %}
 1: // file backend/Java.java
 2: 
 3: package backend;
 4: 
 5: public class Java {
 6:   protected void m(String c, String t) {
 7:     System.out.println("Location in class " + c + " can run protected methods of Java by means of reference of type " + t + ".");
 8:   }
 9: }

 1: // file frontend/Javascript.java
 2: 
 3: package frontend;
 4: 
 5: import backend.Java;
 6: 
 7: public class Javascript extends Java {
 8:   public void callProtectedMethods() {
 9:     new Java().m("Javascript", "Java");
10:   }
11: }
{% endhighlight %}

There is a compile error in line 9 of class Javascript.
Line 9 of class Javascript calls the protected method of Java.
The call is located in a subclass of Java that belongs to
another package, but we get a compile error.

You may also not be able to explain why the following code compiles
and prints "Location in class Ruby can run protected methods of Java by
means of reference of type Javascript" instead of giving a compile
error.

{% highlight java %}
 1: // file backend/Ruby.java
 2: 
 3: package backend;
 4: 
 5: import frontend.Javascript;
 6: 
 7: public class Ruby {
 8:   public void callProtectedMethods() {
 9:     new Javascript().m("Ruby", "Javascript");
10:   }
11: }
{% endhighlight %}

Line 9 of Ruby calls the protected method of Javascript even though
the line is in a different package than Javascript and Javascript is
not a superclass of Ruby.

Problems with Oracle's definition/explanation.

1. It does not explain that not all access in subclasses is allowed.
   JSL 6.6.2 covers this case.
2. It does not explain that code in package where protected member is
   declared may reach member beyond the boundaries of the package.
3. It does not explain that that same code may even call protected
   members declared in other packages.
4. It does not explain that code in a superclass that has protected
   method of same name may access protected members declared in other
   packages.

{% highlight asciidoc %}
Locations that can access a given protected member.

1. Methods.
   1. The package where method is declared.
   2. Every subclass that inherits the method.
   3. Every superclass that has a protected method of the same name.
   4. Every package where a super class declares a protected
      method of the same name.
2. Fields.
   1. Every location in package where field is declared.
   2. Every subclass that inherits the field.

Rules for accessing member from given location.

1. When Loc and Decl are in same package, Loc can do the following.
   1. Call a method of the same name by means of a reference that inherits method.
   2. Read/write field by means of a reference that inherits field.
2. When Loc is in a class that inherits member, Loc can do the following.
   1. Call a method of the same name by means of a reference R that
   inherits method and is subtype of Loc. Corresponds to test case
   ProtectedMemberAccess1.
   2. Read/write field by means of a reference that inherits field and
   is a subtype of Loc.
{% endhighlight %}

{% highlight asciidoc %}
Introduction

- Oracle's definition
- Four examples that illustrate problem of definition
- R.Q. or Problem of definition: definition is more aligned with the purpose
  of protected access modifier than with what protected concretely means
  in Java.
- Significance: If you are a beginer, take Oracle's definition. If you
  are a professional, you want to know concretely what you can and
  cannot do, even if what is possible does not always align with the
  purpose of protected access modifier.
- HYP. There are two approaches to defining protected.
  1. What locations can call a given method?
  2. What methods can a given location call?

# Failed hypotheses

- Oracle's definition is wrong.
- Oracle's definition is incomplete.

# Outline

I want to show that the two rules are a definition of the protected access modifier.

R. Every case that passes javac check also passes the rules.
R. Every case that fails  javac check also fails the rules.
R. There is not another check on protected.

--- V1
- R. The example repository satisfies the rules.
- R. The example repository is exhaustive.
- R. The javac check on protected satisfies the rules.
  - E. The javac tests satisfy the rules.

# Scope

- In the scope: Fields, methods, and constructors that are instance or static.
- Out of the scope: Inner classes, Inner interfaces.

# What happens when your reference points to a supertype?

Answer? Either a compile error or a runtime exception.
TODO: see example in 6.6.2.2 and study following quote from there.

"
The method delta could try to cast (§5.5, §15.16) its parameter to be
a Point3d, but this cast would fail, causing an exception, if the
class of p at run time were not Point3d.
"

# What are examples that satisfy Oracle's definition but not the rules or viceversa?

The four examples in the introduction.

# Which of the following obvious factors affect access to protected member?

- static: TODO
- final: TODO
- abstract: TODO

# Do the same rules apply to the following kinds of members?

- fields: TODO
- methods: TODO
- inner classes: TODO
- inner interfaces: TODO
- constructor: TODO

# Which of the following non-obvious factors affect access to protected member and which do not?

TODO

- Are loc and declaration in the same package?
- Relation of Loc to Decl:
  - Supertype
  - Subtype
  - None
- Relation of Ref type to Decl:
  - Supertype
  - Subtype
  - None
- Relation of Ref type to Loc:
  - Supertype
  - Subtype
  - None
- Relation of Obj type to Loc:
  - Supertype
  - Subtype
  - None
- Does Ref type inherit protected member?
- Does Obj type inherit protected member?
- When you have a method, reference is used to call method or is it a method reference expression?

Ref type = Type of 

# Every case that passes javac check also passes the rules.

## How does the Java Language Specification define protected?

[JLS 6.6.2](https://docs.oracle.com/javase/specs/jls/se8/html/jls-6.html#jls-6.6.2)

JLS defines protected access for members and for constructors.

It looks like definition for members **contradicts** access from superclasses that we know can happen.

It looks like the bullet points in 6.6.2.1 (which apply only to members) **satisfy** the rules but **not viceversa**.
In particular on the restriction on the Ref type is the same as in the rules.
What is not considered by the bullet points is access by superclasses.

Clarification on terminology.

- Example of qualified name Q.Id where Q is ExpressionName: TODO
- Example of method reference expression Q::Id where Q is ExpressionName: TODO
- Example of field access expression E.Id where E is Primary expression: TODO
- Example of method invocation expression E.Id(...) where E is Primary expression: TODO
- Example of method reference expression E.Id(...) where E is Primary expression: TODO
- Example of method reference expression T::Id where T is ReferenceType: TODO


## How does javac check protected access? (Could be evidence of `The javac tests satisfy the rules`)

When Javascript.java has an illegal access to m(), the sequence of verbose messages is the following.
- loading, given by method JavaCompiler.parse
- parsing, given by method JavaCompiler.parse
- checking, given by method JavaCompiler.attribute, this is where protected check happens
- writing, maybe given by method JavaCompiler.generate

Protected check in javac is located in Resolve.java:414, method isAccessible.

There are 6 atoms in the check.



## Checking Access to Protected Members in the Java Virtual Machine, by Alessandro Coglio





{% endhighlight %}



<!--
<img src="https://geirsson.com/assets/flatmap/img/wat.jpg" alt="wat" style="width: 20%; display: block; margin-left: auto; margin-right: auto;" />
<img src="https://s-media-cache-ak0.pinimg.com/originals/27/e5/31/27e5311e5075b62e03451701b4233267.jpg" alt="wat" style="width: 60%; display: block; margin-left: auto; margin-right: auto;" />
-->

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
        this.page.url = 'http://ruslanledesma.com/2016/12/29/pomodoro-timer-for-osx.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-12-29-pomodoro-timer-for-osx'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
