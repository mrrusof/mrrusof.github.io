---
layout: post
title: 'ProTip: Automagically setup PhantomJS for Capybara+Poltergeist'
date: 2016-07-27
author: Ruslan Ledesma-Garza
summary: 'This ProTip explains how to install PhantomJS automatically
on program start.  This way, running one-off programs that exercise
your web application becomes super easy.'
---

Are you looking for a way to install PhantomJS automatically on program start?
This ProTip is for you.

Consider the following program `titles.rb`.
The example prints the titles of posts that appear in the main page of this blog.

{% highlight ruby %}
#!/usr/bin/env ruby

require 'capybara/dsl'
require 'capybara/poltergeist'

include Capybara::DSL
Capybara.default_driver = :poltergeist

visit 'http://ruslanledesma.com/'
all('.post-link').each do |pl|
  puts pl.text
end
{% endhighlight %}

The example works fine when command `phantomjs` is in `$PATH`.

{% highlight asciidoc %}
ruslan$ ./titles.rb
including Capybara::DSL in the global scope is not recommended!
How to do before(:all)/after(:all) in minitest
Correole: The minimum feature newsletter in Ruby
Why does Heap's algorithm work?
How to count cycles in a complete graph
Arbitrage
Release ActiveRecord connections in Sinatra + Thin
Stacking Boxes
Ecological Bin Packing
The Blocks Problem
The 3n + 1 Problem
ProTip: Redirect stdout and stderr to console and different files in Bash
Jill Rides Again
The Captured Wise Men
The Adjacent Coins Problem
{% endhighlight %}

Otherwise you get this ugly error.

{% highlight asciidoc %}
ruslan$ ./titles.rb
including Capybara::DSL in the global scope is not recommended!
/Users/ruslan/.rvm/gems/ruby-2.3.1/gems/cliver-0.3.2/lib/cliver/dependency.rb:143:in `raise_not_found!': Could not find an executable ["phantomjs"] on your path. (Cliver::Dependency::NotFound)
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/cliver-0.3.2/lib/cliver/dependency.rb:116:in `detect!'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/cliver-0.3.2/lib/cliver.rb:24:in `detect!'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/poltergeist-1.10.0/lib/capybara/poltergeist/client.rb:37:in `initialize'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/poltergeist-1.10.0/lib/capybara/poltergeist/client.rb:14:in `new'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/poltergeist-1.10.0/lib/capybara/poltergeist/client.rb:14:in `start'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/poltergeist-1.10.0/lib/capybara/poltergeist/driver.rb:44:in `client'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/poltergeist-1.10.0/lib/capybara/poltergeist/driver.rb:25:in `browser'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/poltergeist-1.10.0/lib/capybara/poltergeist/driver.rb:97:in `visit'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/capybara-2.7.1/lib/capybara/session.rb:233:in `visit'
        from /Users/ruslan/.rvm/gems/ruby-2.3.1/gems/capybara-2.7.1/lib/capybara/dsl.rb:52:in `block (2 levels) in <module:DSL>'
        from ./automagically.rb:9:in `<main>'
{% endhighlight %}

In this case, try the following program `magic-titles.rb`.

{% highlight ruby %}
#!/usr/bin/env ruby

require 'phantomjs'
require 'capybara/dsl'
require 'capybara/poltergeist'

include Capybara::DSL
Capybara.default_driver = :poltergeist
page.driver.options[:phantomjs] = Phantomjs.path # MAGIC

visit 'http://ruslanledesma.com/'
all('.post-link').each do |pl|
  puts pl.text
end
{% endhighlight %}

Line `MAGIC` installs PhantomJS in `~/phantomjs`, so you might want to remove that directory afterwards.
Now you get titles.

{% highlight asciidoc %}
ruslan$ ./magic-titles.rb
including Capybara::DSL in the global scope is not recommended!
Phantomjs does not appear to be installed in /Users/ruslan.ledesma.garza/.phantomjs/2.1.1/darwin/bin/phantomjs, installing!
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 16.3M  100 16.3M    0     0  43736      0  0:06:32  0:06:32 --:--:-- 48617
Archive:  phantomjs-2.1.1-macosx.zip
   creating: phantomjs-2.1.1-macosx/
   creating: phantomjs-2.1.1-macosx/bin/
...

Successfully installed phantomjs. Yay!
Removed temporarily downloaded files.
How to do before(:all)/after(:all) in minitest
Correole: The minimum feature newsletter in Ruby
Why does Heap's algorithm work?
How to count cycles in a complete graph
Arbitrage
Release ActiveRecord connections in Sinatra + Thin
Stacking Boxes
Ecological Bin Packing
The Blocks Problem
The 3n + 1 Problem
ProTip: Redirect stdout and stderr to console and different files in Bash
Jill Rides Again
The Captured Wise Men
The Adjacent Coins Problem
{% endhighlight %}

The elided part consists of a list files that are installed in `~/.phantomjs`.

# How does `MAGIC` work?

Line `MAGIC` installs PhantomJS and tells Poltergeist where to find command `phantomjs`.

Installation happens when `MAGIC` calls method
[`Phantomjs.path`](https://github.com/colszowka/phantomjs-gem/blob/38a722deeb7989e2de093fd628139266386492c1/lib/phantomjs.rb#L24).
The code that does the installation is located in method
[`Phantomjs::Platform#install!`](https://github.com/colszowka/phantomjs-gem/blob/38a722deeb7989e2de093fd628139266386492c1/lib/phantomjs/platform.rb#L43).
The code installs PhantomJS in `~/.phantomjs` when PhantomJS is not
installed there and is not in your `$PATH`.

`MAGIC` tells Poltergeist the path of command `phantomjs` by
setting option `page.driver.options[:phantomjs]` to the path given by
[`Phantomjs.path`](https://github.com/colszowka/phantomjs-gem/blob/38a722deeb7989e2de093fd628139266386492c1/lib/phantomjs.rb#L24).
The option is applied when [`visit
'http://ruslanledesma.com'`](https://github.com/jnicklas/capybara/blob/master/lib/capybara/session.rb#L217)
executes.  [The code that applies the
option](https://github.com/teampoltergeist/poltergeist/blob/35cd07d768327f1a575dd5dc41e0ee888d43e794/lib/capybara/poltergeist/driver.rb#L45)
is located in Poltergeist.

# Gems known to work together

- Capybara 2.7.1
- Poltergeist 1.10.0
- PhantomJS 2.1.1.0 (that is [gem `phantomjs`](https://github.com/colszowka/phantomjs-gem), not the actual [PhantomJS](http://phantomjs.org/))

# Do's and don'ts

Do apply this ProTip when you are doing a one-off task.
Do remember to remove directory `~/phantomjs` when you don't need it anymore.

Do not apply this ProTip when installing PhantomJS by other means
gives a shorter development cycle.  For example, when you run you
tests in a Docker container, install PhantomJS and either [do not discard the
container's
data](https://docs.docker.com/engine/reference/run/#/clean-up-rm) or
[create a new image from the last state of the
container](https://docs.docker.com/engine/reference/commandline/commit/).
This way you will not lose time installing PhantomJS every time you
run tests.

Do not apply this ProTip when there is a dedicated mechanism for
installing dependencies.  For example, when you need PhantomJS for a
project and that project has a Gemfile, [indicate that PhantomJS
should be installed when dependencies for your project are
installed](https://github.com/colszowka/phantomjs-gem#usage-with-poltergeistcapybara).

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
        this.page.url = 'http://ruslanledesma.com/2016/07/27/automagically-phantomjs-poltergeist-capybara.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-07-27-automagically-phantomjs-poltergeist-capybara'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
