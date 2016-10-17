---
layout: post
title: 'The Category Problem: graph traversal by recursive SQL queries'
date: 2016-10-15
author: Ruslan Ledesma-Garza
summary: '
An interview question in the wild, the Category Problem asks to model a graph of categories of goods in SQL.
The sample solution involves a couple of recursive SQL queries that traverse the edges of the model graph.
The sample solution works in PostgreSQL 9.6.
This post includes a reference implementation in Ruby.
'
---

{{page.summary}}

# Problem

The problem asks you to implement in SQL the following rules.

1. There are products. Each product has a name.
2. There are categories. Each category has a name.
3. Each product may belong to one or more categories.
4. Each category may have subcategories.
5. Categories that have products do not have subcategories.
7. You may ask a product for its corresponding categories.
8. You may ask a category for its corresponding products.

Consider the following diagram of an example state consisting of products and categories.

<img src="/assets/2016-10-16.example.png" alt="Example" style="width: 50%; display: block; margin-left: auto; margin-right: auto;" />

In the diagram, a box corresponds to a product and a circle to a category.
Arrows start from a parent category and end in a child category.
Dotted lines connect categories with their corresponding products.

Your database must disallow violations of the rules.
Consider category Food.
By rule 5, it must not be possible to add products to category Food.

# Solution

The sample solution is the following.

{% highlight sql %}
CREATE TABLE products (
  id serial PRIMARY KEY,
  name varchar(50)
);

INSERT INTO products (name) VALUES ('sandwich');
SELECT * FROM products;

CREATE TABLE categories (
  id serial PRIMARY KEY,
  name varchar(50)
);

INSERT INTO categories (name) VALUES ('gas station food');
SELECT * FROM categories;

CREATE TABLE p_c (
  pid integer REFERENCES products(id),
  cid integer REFERENCES categories(id)
);

INSERT INTO p_c (pid, cid) VALUES (1, 1);
SELECT products.name AS product, categories.name AS category
  FROM products, p_c, categories
  WHERE products.id = pid AND cid = categories.id;

CREATE TABLE sub_c (
  parent integer REFERENCES categories(id),
  child integer REFERENCES categories(id)
);

INSERT INTO categories (name) VALUES ('food');
INSERT INTO sub_c (parent, child) VALUES (2, 1);
SELECT c1.name AS parent, c2.name AS child
  FROM sub_c, categories AS c1, categories AS c2
  WHERE parent = c1.id AND child = c2.id;

CREATE FUNCTION if_no_sub_c(id integer)
  RETURNS bool AS
$func$
  SELECT NOT EXISTS (SELECT 1 FROM sub_c WHERE id = parent);
$func$ LANGUAGE sql STABLE;

ALTER TABLE p_c ADD CONSTRAINT if_no_sub_c_check
  CHECK (if_no_sub_c(cid));

INSERT INTO products (name) VALUES ('pizza');
INSERT INTO p_c (pid, cid) VALUES (2, 2);
SELECT count(*) from p_c, sub_c WHERE cid = parent;

CREATE FUNCTION if_no_p(id integer)
  RETURNS bool AS
$func$
  SELECT NOT EXISTS (SELECT 1 FROM p_c WHERE id = pid);
$func$ LANGUAGE sql STABLE;

ALTER TABLE sub_c ADD CONSTRAINT if_no_p_check
  CHECK (if_no_p(parent));

INSERT INTO categories (name) VALUES ('fast food');
INSERT INTO sub_c (parent, child) VALUES (1, 3);
SELECT count(*) from p_c, sub_c WHERE cid = parent;

CREATE FUNCTION categories(product_id integer)
  RETURNS TABLE (category varchar(50)) AS
$func$
  WITH RECURSIVE cats(pid, cid, category) AS (
      SELECT pid, cid, name
      FROM p_c, categories
      WHERE cid = id
    UNION ALL
      SELECT pid, parent, name
      FROM cats, sub_c, categories
      WHERE cid = child AND parent = id
  )
  SELECT category FROM cats WHERE pid = product_id;
$func$ LANGUAGE sql;

SELECT categories(id) FROM products WHERE name = 'sandwich';

CREATE FUNCTION products(category_id integer)
  RETURNS TABLE (products varchar(50)) AS
$func$
  WITH RECURSIVE prods(cid, pid, product) AS (
      SELECT cid, pid, name
      FROM p_c, products
      WHERE pid = id
    UNION
      SELECT parent, pid, product
      FROM prods, sub_c
      WHERE cid = child
  )
  SELECT product FROM prods WHERE cid = category_id
$func$ LANGUAGE sql;

SELECT products(id) FROM categories WHERE name = 'food';
INSERT INTO sub_c (parent, child)
  SELECT food.id, ff.id
  FROM categories AS food, categories AS ff
  WHERE food.name = 'food' AND ff.name = 'fast food';
INSERT INTO p_c (pid, cid)
  SELECT pizza.id, ff.id
  FROM products AS pizza, categories AS ff
  WHERE pizza.name = 'pizza' AND ff.name = 'fast food';
SELECT products(id) FROM categories WHERE name = 'food';
{% endhighlight %}

The sample solution consists of two parts.
The first part is the construction of the database schema indicated by rules 1 to 5.
The second part is the construction of two recursive queries, one for rule 6 and one for rule 7.
The next section includes a reference implementation in Ruby that you can use to understand the semantics of the sample solution.

<details>

<summary>
Click here for an explanation of the construction of the database schema.
</summary>
<br>

<b>1. Each product has a name.</b>
<br>
<br>

We create a table of products.

{% highlight sql %}
CREATE TABLE products (
  id serial PRIMARY KEY,
  name varchar(50)
);
{% endhighlight %}

Example usage.

{% highlight sql %}
INSERT INTO products (name) VALUES ('sandwich');
SELECT * FROM products;
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
INSERT 0 1
 id |   name   
----+----------
  1 | sandwich
(1 row)
{% endhighlight %}

<b>2. Each category has a name.</b>
<br>
<br>

We create a table of categories.

{% highlight sql %}
CREATE TABLE categories (
  id serial PRIMARY KEY,
  name varchar(50)
);
{% endhighlight %}

Example usage.

{% highlight sql %}
INSERT INTO categories (name) VALUES ('gas station food');
SELECT * FROM categories;
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
INSERT 0 1
 id |       name       
----+------------------
  1 | gas station food
(1 row)
{% endhighlight %}

<b>3. Each product may belong to one or more categories.</b>
<br>
<br>

**TODO**

{% highlight sql %}
CREATE TABLE p_c (
  pid integer REFERENCES products(id),
  cid integer REFERENCES categories(id)
);
{% endhighlight %}

Example usage.

{% highlight sql %}
INSERT INTO p_c (pid, cid) VALUES (1, 1);
SELECT products.name AS product, categories.name AS category
  FROM products, p_c, categories
  WHERE products.id = pid AND cid = categories.id;
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
INSERT 0 1
 product  |     category     
----------+------------------
 sandwich | gas station food
(1 row)
{% endhighlight %}

<b>4. Each category may have subcategories.</b>
<br>
<br>

{% highlight sql %}
CREATE TABLE sub_c (
  parent integer REFERENCES categories(id),
  child integer REFERENCES categories(id)
);
{% endhighlight %}

Example usage.

{% highlight sql %}
INSERT INTO categories (name) VALUES ('food');
INSERT INTO sub_c (parent, child) VALUES (2, 1);
SELECT c1.name AS parent, c2.name AS child
  FROM sub_c, categories AS c1, categories AS c2
  WHERE parent = c1.id AND child = c2.id;
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
INSERT 0 1
INSERT 0 1
 parent |      child       
--------+------------------
 food   | gas station food
(1 row)
{% endhighlight %}

<b>5. Categories that have products do not have subcategories.</b>
<br>
<br>

{% highlight sql %}
CREATE FUNCTION if_no_sub_c(id integer)
  RETURNS bool AS
$func$
  SELECT NOT EXISTS (SELECT 1 FROM sub_c WHERE id = parent);
$func$ LANGUAGE sql STABLE;

ALTER TABLE p_c ADD CONSTRAINT if_no_sub_c_check
  CHECK (if_no_sub_c(cid));

CREATE FUNCTION if_no_p(id integer)
  RETURNS bool AS
$func$
  SELECT NOT EXISTS (SELECT 1 FROM p_c WHERE id = pid);
$func$ LANGUAGE sql STABLE;

ALTER TABLE sub_c ADD CONSTRAINT if_no_p_check
  CHECK (if_no_p(parent));
{% endhighlight %}

Example usage.

{% highlight sql %}
INSERT INTO products (name) VALUES ('pizza');
INSERT INTO p_c (pid, cid) VALUES (2, 2);
SELECT count(*) from p_c, sub_c WHERE cid = parent;

INSERT INTO categories (name) VALUES ('fast food');
INSERT INTO sub_c (parent, child) VALUES (1, 3);
SELECT count(*) from p_c, sub_c WHERE cid = parent;
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
INSERT 0 1
ERROR:  new row for relation "p_c" violates check constraint "if_no_sub_c_check"
DETAIL:  Failing row contains (2, 2).
 count 
-------
     0
(1 row)

INSERT 0 1
ERROR:  new row for relation "sub_c" violates check constraint "if_no_p_check"
DETAIL:  Failing row contains (1, 3).
 count 
-------
     0
(1 row)
{% endhighlight %}

</details>
<br>

<details>

<summary>
Click here for an explanation of the two recursive queries.
</summary>
<br>

For rule 6, we construct SQL function `categories(product_id)` that returns the categories for given product id.
For example, for product sandwich, the categories are `gas station food` and `food`.

**TODO**

<b>6. You may ask a product for its corresponding categories.</b>

{% highlight sql %}
CREATE FUNCTION categories(product_id integer)
  RETURNS TABLE (category varchar(50)) AS
$func$
  WITH RECURSIVE cats(pid, cid, category) AS (
      SELECT pid, cid, name
      FROM p_c, categories
      WHERE cid = id
    UNION ALL
      SELECT pid, parent, name
      FROM cats, sub_c, categories
      WHERE cid = child AND parent = id
  )
  SELECT category FROM cats WHERE pid = product_id;
$func$ LANGUAGE sql;
{% endhighlight %}

Example usage.

{% highlight sql %}
SELECT categories(id) FROM products WHERE name = 'sandwich';
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
    categories    
------------------
 gas station food
 food
(2 rows)
{% endhighlight %}

<b>7. You may ask a category for its corresponding products.</b>

{% highlight sql %}
CREATE FUNCTION products(category_id integer)
  RETURNS TABLE (products varchar(50)) AS
$func$
  WITH RECURSIVE prods(cid, pid, product) AS (
      SELECT cid, pid, name
      FROM p_c, products
      WHERE pid = id
    UNION
      SELECT parent, pid, product
      FROM prods, sub_c
      WHERE cid = child
  )
  SELECT product FROM prods WHERE cid = category_id
$func$ LANGUAGE sql;
{% endhighlight %}

Example usage.

{% highlight sql %}
SELECT products(id) FROM categories WHERE name = 'food';
INSERT INTO sub_c (parent, child)
  SELECT food.id, ff.id
  FROM categories AS food, categories AS ff
  WHERE food.name = 'food' AND ff.name = 'fast food';
INSERT INTO p_c (pid, cid)
  SELECT pizza.id, ff.id
  FROM products AS pizza, categories AS ff
  WHERE pizza.name = 'pizza' AND ff.name = 'fast food';
SELECT products(id) FROM categories WHERE name = 'food';
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
 products 
----------
 sandwich
(1 row)

INSERT 0 1
INSERT 0 1
 products 
----------
 sandwich
 pizza
(2 rows)
{% endhighlight %}

</details>
<br>

# Reference implementation in Ruby

The reference implementation is the following.

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

Example usage.

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

<summary>
Click here for an explanation of the reference implementation.
</summary>
<br>

<b>1. Each product has a name.</b>
<br>

We create a class of products.

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

Example usage.

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
<br>

We create a class of categories.

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

Example usage.

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
<br>

We declare an array of products and create method `Category#add_product`.

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

Example usage.

{% highlight ruby %}
gsf.add_product sandwich
puts "gsf.products = #{gsf.products}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
gsf.products = [sandwich]
{% endhighlight %}

<b>4. Each category may have subcategories.</b>
<br>

We create method

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

Example usage.

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

Example usage.

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

Example usage.

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

Example usage.

{% highlight ruby %}
food.add_category ff
ff.add_product pizza
puts "food.products = #{food.products}"
{% endhighlight %}

Gives.

{% highlight asciidoc  %}
food.products = [sandwich, pizza]
{% endhighlight %}


# Other uses of WITH clauses



- Apply several data modifications in the same query ([section 7.8.2](https://www.postgresql.org/docs/9.6/static/queries-with.html)).
- Apply recursive self-references in a data-modifying query ([section 7.8.2](https://www.postgresql.org/docs/9.6/static/queries-with.html)).