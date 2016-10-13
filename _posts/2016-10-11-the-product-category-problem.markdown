---
layout: post
title: . #The Product Category Problem
date: 2016-10-11
author: Ruslan Ledesma-Garza
summary: Something something
---

This post explains how to write a recursive SQL query.

# Problem

Business rules.

1. Each product has a name.
2. Each category has a name.
3. Each product may belong to one or more categories.
4. Each category may have subcategories.
5. Categories that have products do not have subcategories.

Features.

7. You may ask a product for its corresponding categories.
8. You may ask a category for its corresponding products.

# Ruby

Solution.

{% highlight ruby %}
class Product

  attr_reader :name, :categories

  def initialize name
    @name = name
    @categories = []
  end

  def to_s
    @name
  end

  alias inspect to_s

  def categories
    @categories.reduce [] do |acc, c|
      begin
        acc << c
        c = c.parent
      end while c
      acc
    end
  end

end

class Category

  attr_reader :name, :products, :children, :parent

  def initialize name
    @name = name
    @products = []
    @children = []
    @parent = nil
  end

  def to_s
    name
  end

  alias inspect to_s

  def add_product p
    if @children.empty?
      @products << p
      p.instance_variable_get(:@categories) << self
    end
  end

  def add_category c
    if @products.empty?
      @children << c
      c.instance_variable_set :@parent, self
    end
  end

  def products
    if @children.empty?
      @products
    else
      @children.reduce [] { |acc, c| acc.concat c.products }
    end
  end

end
{% endhighlight %}

Example.

{% highlight ruby %}
food = Category.new 'food'
gsf = Category.new 'gas station food'
ff = Category.new 'fast food'
sandwich = Product.new 'sandwich'
pizza = Product.new 'pizza'

food.add_category gsf
food.add_category ff
gsf.add_product sandwich
ff.add_product pizza

puts "sandwich.categories = #{sandwich.categories}"
puts "pizza.categories = #{pizza.categories}"
puts "food.products = #{food.products}"
{% endhighlight %}

Gives.

{% highlight asciidoc %}
sandwich.categories = [gas station food, food]
pizza.categories = [fast food, food]
food.products = [sandwich, pizza]
{% endhighlight %}

<details>

<summary>
This is how you address the rules and features step by step.
</summary>
<br>

<b>1. Each product has a name.</b>

{% highlight ruby %}
class Product

  attr_reader :name

  def initialize name
    @name = name
  end

  def to_s
    @name
  end

  alias inspect to_s

end
{% endhighlight %}

Example.

{% highlight ruby %}
sandwich = Product.new 'sandwich'
puts "sandwich = #{sandwich}"
puts "[sandwich] = #{[sandwich]}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
sandwich = sandwich
[sandwich] = [sandwich]
{% endhighlight %}

<b>2. Each category has a name.</b>

{% highlight ruby %}
class Category

  attr_reader :name

  def initialize name
    @name = name
  end

  def to_s
    name
  end

  alias inspect to_s

end
{% endhighlight %}

Example.

{% highlight ruby %}
gsf = Category.new 'gas station food'
puts "gsf = #{gsf}"
puts "[gsf] = #{[gsf]}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
gsf = gas station food
[gsf] = [gas station food]
{% endhighlight %}

<b>3. Each product may belong to one or more categories.</b>

{% highlight ruby %}
class Category

  attr_reader :name, :products

  def initialize name
    @name = name
    @products = []
  end

  ...

  def add_product p
    @products << p
  end

end
{% endhighlight %}

Example.

{% highlight ruby %}
gsf.add_product sandwich
puts "gsf.products = #{gsf.products}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
gsf.products = [sandwich]
{% endhighlight %}

<b>4. Each category may have subcategories.</b>

{% highlight ruby %}
class Category

  attr_reader :name, :products, :children

  def initialize name
    @name = name
    @products = []
    @children = []
  end

  ...

  def add_category c
    @children << c
  end

end
{% endhighlight %}

Example.

{% highlight ruby %}
food = Category.new 'food'
food.add_category gsf
puts "food.children = #{food.children}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
food.children = [gas station food]
{% endhighlight %}

<b>5. Categories that have products do not have subcategories.</b>

{% highlight ruby %}
class Category

  ...

  def add_product p
    @products << p if @children.empty?
  end

  def add_category c
    @children << c if @products.empty?
  end

end
{% endhighlight %}

Example.

{% highlight ruby %}
ff = Category.new 'fast food'
gsf.add_category ff
puts "gsf.children = #{gsf.children}"

pizza = Product.new 'pizza'
food.add_product pizza
puts "food.products = #{food.products}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
gsf.children = []
food.products = []
{% endhighlight %}

<b>6. You may ask a product for its corresponding categories.</b>

{% highlight ruby %}
class Product

  attr_reader :name, :categories

  def initialize name
    @name = name
    @categories = []
  end

  ...

  def categories
    @categories.reduce [] do |acc, c|
      begin
        acc << c
        c = c.parent
      end while c
      acc
    end
  end

end

class Category

  attr_reader :name, :products, :children, :parent

  def initialize name
    @name = name
    @products = []
    @children = []
    @parent = nil
  end

  ...

  def add_product p
    if @children.empty?
      @products << p
      p.instance_variable_get(:@categories) << self
    end
  end

  def add_category c
    if @products.empty?
      @children << c
      c.instance_variable_set :@parent, self
    end
  end

end
{% endhighlight %}

Example.

{% highlight ruby %}
puts "sandwich.categories = #{sandwich.categories}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
sandwich.categories = [gas station food, food]
{% endhighlight %}

<b>7. You may ask a category for its corresponding products.</b>

{% highlight ruby %}
class Category

  def products
    if @children.empty?
      @products
    else
      @children.reduce [] { |acc, c| acc.concat c.products }
    end
  end

end
{% endhighlight %}

Example.

{% highlight ruby %}
food.add_category ff
ff.add_product pizza
puts "food.products = #{food.products}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
food.products = [sandwich, pizza]
{% endhighlight %}

</details>

# PostgreSQL


