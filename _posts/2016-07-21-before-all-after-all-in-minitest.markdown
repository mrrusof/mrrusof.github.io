---
layout: post
title: 'How to do before(:all)/after(:all) in minitest'
date: 2016-07-21
author: Ruslan Ledesma-Garza
summary: 'Minitest does not come with
before(:all)/after(:all).  For flat describe blocks, extension
minitest-hooks is the way to go.  For nested describe blocks,
minitest-hooks may not behave as you want. This article provides two
alternatives to minitest-hooks for nested describe blocks and points
to examples that you can run for 16 cases, 11 of them not covered here.'
---

_[Are you looking for a case not here?](#something-else)_

So you like to write your tests in spec format (using `describe`,
`before`, `after`, and `it`) and running them with
[minitest](https://github.com/seattlerb/minitest).  You have a set of
tests with a common setup and that setup is orders of magnitude more
expensive than the body of each test, for example.

{% highlight ruby %}
require 'minitest/autorun'

describe 'end-to-end test on main component of RSS reader' do

  # before executes before each test
  before do
    # HTTPServer takes a couple of seconds to start
    @http_server = HTTPServer.new 'localhost', 9090
  end

  # after executes after each test
  after do
    @http_server.stop
    @http_server.join
  end

  it 'main component gets RSS feed' do
    # Takes several milliseconds to complete
    main.get('http://localhost:9090/feed.xml').wont_be nil
  end

  it 'main component raises RuntimeError when given malformed RSS feed' do
    # Takes several milliseconds to complete
    ->{ main.get('http://localhost:9090/bad.xml') }.must_raise RuntimeError
  end

end
{% endhighlight %}

Running the example takes you 4 to 5 seconds because starting the HTTP
server is expensive.  You would rather setup once before all tests and
teardown once after all tests.  In the case where you are a practical
person, you use blocks `before(:all)` and `after(:all)` provided by [gem
minitest-hooks](https://github.com/jeremyevans/minitest-hooks) like so.

{% highlight ruby %}
require 'minitest/autorun'
require 'minitest/hooks/default'

describe 'end-to-end test on main component of RSS reader' do

  # before(:all) executes before any test is run
  before(:all) do
    # HTTPServer takes a couple of seconds to start
    @http_server = HTTPServer.new 'localhost', 9090
  end

  # after(:all) executes after all tests have been run
  after(:all) do
    @http_server.stop
    @http_server.join
  end

  it 'main component gets RSS feed' do
    main.get('http://localhost:9090/feed.xml').wont_be nil
  end

  it 'main component raises RuntimeError when given malformed RSS feed' do
    ->{ main.get('http://localhost:9090/bad.xml') }.must_raise RuntimeError
  end

end
{% endhighlight %}

In the case where you prefer to do without minitest-hooks, you use
a [class instance
variable](http://www.railstips.org/blog/archives/2006/11/18/class-and-instance-variables-in-ruby/)
and a [minitest `after_run`
block](https://github.com/seattlerb/minitest/blob/5424c55e958fe5fe0dbbf1266140d6ccb8ef4415/lib/minitest.rb#L67)
like so.

{% highlight ruby %}
require 'minitest/autorun'

describe 'end-to-end test on main component of RSS reader' do

  # The block assigned to @expensive executes before any test is run
  @http_server = begin
    # HTTPServer takes a couple of seconds to start
    HTTPServer.new 'localhost', 9090
  end

  # The block given to Minitest::after_run executes way after all
  # tests have been run, right before minitest stops.
  Minitest.after_run do
    @http_server.stop
    @http_server.join
  end

  it 'main component gets RSS feed' do
    main.get('http://localhost:9090/feed.xml').wont_be nil
  end

  it 'main component raises RuntimeError when given malformed RSS feed' do
    ->{ main.get('http://localhost:9090/bad.xml') }.must_raise RuntimeError
  end

end
{% endhighlight %}

# I have nested describe blocks

Watch out. minitest-hooks executes a given `before(:all)` before EACH
nested describe block and a corresponding `after(:all)` after EACH
describe block.  Consider the following spec file.

{% highlight ruby %}
require 'minitest/autorun'
require 'minitest/hooks/default'

describe "spec file, eager evaluation, nested describe block, minitest-hooks" do

  before(:all) do
    puts 'executing something expensive'
    @expensive = 'woah! expensive!'
  end

  after(:all) do
    puts "cleaning expensive value: #{@expensive}"
  end

  describe 'describe1' do
    it 'test1' do
      puts "test1: #{@expensive}"
    end

    it 'test2' do
      puts "test2: #{@expensive}"
    end
  end

  describe 'describe2' do
    it 'test3' do
      puts "test3: #{@expensive}"
    end

    it 'test4' do
      puts "test4: #{@expensive}"
    end
  end

end
{% endhighlight %}

The spec file will give you an output like the following.

{% highlight asciidoc %}
ruslan$ bundle exec rake
Run options: --seed 36717

# Running:

executing something expensive
test1: woah! expensive!
.test2: woah! expensive!
.cleaning expensive value: woah! expensive!
executing something expensive
test4: woah! expensive!
.test3: woah! expensive!
.cleaning expensive value: woah! expensive!


Finished in 0.002084s, 1919.5342 runs/s, 0.0000 assertions/s.

4 runs, 0 assertions, 0 failures, 0 errors, 0 skips
{% endhighlight %}

To execute your setup before all describe blocks and your teardown
after all describe blocks, you could do the following.

{% highlight ruby %}
require 'minitest/autorun'

describe "spec file, eager evaluation, nested describe block" do

  $expensive = begin
                 puts 'executing something expensive'
                 'woah! expensive!'
               end

  def self.expensive
    $expensive
  end

  Minitest.after_run do
    puts "cleaning expensive value: #{$expensive}"
    puts "cleaning expensive value: #{expensive}"
    puts "cleaning expensive value: #{self.expensive}"
  end

  describe 'describe1' do
    it 'test1' do
      puts "test1: #{self.class.expensive}"
    end

    it 'test2' do
      puts "test2: #{self.class.expensive}"
    end
  end

  describe 'describe2' do
    it 'test3' do
      puts "test3: #{self.class.expensive}"
    end

    it 'test4' do
      puts "test4: #{self.class.expensive}"
    end
  end

end
{% endhighlight %}

If you have aversion to global variables, you can do the following.

{% highlight ruby %}
require 'minitest/autorun'

class ClassEagerNested < Minitest::Spec

  @@expensive = begin
                  puts 'executing something expensive'
                  'woah! expensive!'
                end

  Minitest.after_run do
    puts "cleaning expensive value: #{@@expensive}"
  end

  describe 'describe1' do
    it 'test1' do
      puts "test1: #{@@expensive}"
    end

    it 'test2' do
      puts "test2: #{@@expensive}"
    end
  end

  describe 'describe2' do
    it 'test3' do
      puts "test3: #{@@expensive}"
    end

    it 'test4' do
      puts "test4: #{@@expensive}"
    end
  end

end
{% endhighlight %}

# I am looking for another case <a name="something-else" />

You know what, maybe you like writing specs or classes, maybe you have
a flat or nested describe block, maybe you want eager or lazy
evaluation, maybe you like using minitest-hooks or not.  Why don't
you have a look at [16 cases](https://github.com/mrrusof/minitest-before-after-examples)?

FYI, [issue 61 of
minitest](https://github.com/seattlerb/minitest/issues/61) inspired me
to write this post and the 16 cases.  You may want to have a look at
that for an idea of why minitest does not come with `before(:all)`/`after(:all)`.


{% include subscribe.html %}
{% include share.html %}


{% include comments.html %}
