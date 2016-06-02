---
layout: post
title: Release ActiveRecord connections in Sinatra + Thin
date: 2016-04-23
edited: 2016-05-31
author: Ruslan Ledesma-Garza 
summary: 'Are you experiencing database connection timeouts in your Sinatra + Thin + ActiveRecord application? Then you want to have a look at this.'
---

When you use ActiveRecord in Sinatra + Thin, you may run into a
timeout like the following.

{% highlight asciidoc %}
ActiveRecord::ConnectionTimeoutError: could not obtain a database connection
within 5.000 seconds (waited 5.002 seconds)
{% endhighlight %}

You may find the timeout even if you use [middleware
`ConnectionManagement`](https://github.com/rails/rails/blob/4-2-stable/activerecord/lib/active_record/connection_adapters/abstract/connection_pool.rb). This
is a serious problem.

One possible reason for the timeout is that connections do not return to ActiveRecord's connection pool after use.
If you are not using `ConnectionManagement` or you want to try a solution right away, go to [How do I release ActiveRecord connections?](#solution).
If you are using `ConnectionManagement`, you may be running Thin in threaded mode and thus not closing or returning to the pool any connections.
Keep reading for an explanation.

**EDIT 2016-05-31**

[I followed up the issue with macournoyer](https://github.com/macournoyer/thin/issues/307) and the problem is fixed in version 1.7.0 of Thin.
The newsletter API example does not run out of connections with that version of Thin.
Kudos to [macournoyer](https://github.com/macournoyer/)!

# ActiveRecord 4.2 and Thin 1.6 threaded mode do not get along

There are two reasons why ActiveRecord's `ConnectionManagement` does not release database connections under Thin's threaded mode.
One reason is that `ConnectionManagement` releases only the one connection that is open for the current thread.
The other reason is that for each HTTP request, Thin executes the corresponding Sinatra route handler in one thread and applies `ConnectionManagement` in another thread.

To illustrate the situation, consider the following newsletter API.

{% highlight ruby %}
  1 #!/usr/bin/env ruby
  2 
  3 require 'sinatra/base'
  4 require 'active_record'
  5 
  6 ActiveRecord::Base.establish_connection(adapter:  'sqlite3',
  7                                         database: 'newsletter.db')
  8 
  9 class Subscriber < ActiveRecord::Base
 10   validates :email, presence: true
 11 end
 12 
 13 class Newsletter < Sinatra::Base
 14   set :server, :thin
 15   use ActiveRecord::ConnectionAdapters::ConnectionManagement
 16 
 17   put '/subscribers/:email' do
 18     Subscriber.create(email: params[:email])
 19     puts Kernel.caller
 20   end
 21 end
 22 
 23 Newsletter.run!
{% endhighlight %}

To run the API, copy paste the contents in file `newsletter.rb`, give execution permissions, and execute `newsletter.rb` like so.

{% highlight asciidoc %}
ruslan$ ./newsletter.rb
== Sinatra (v1.4.7) has taken the stage on 4567 for development with backup from Thin
Thin web server (v1.6.4 codename Gob Bluth)
Maximum connections set to 1024
Listening on localhost:4567, CTRL+C to stop
{% endhighlight %}

When the API is running, register subscribers by putting to path `/subscribers/:email` like so.

{% highlight asciidoc %}
ruslan$ curl --verbose -X PUT localhost:4567/subscribers/mrrusof@gmail.com
*   Trying ::1...
* Connected to localhost (::1) port 4567 (#0)
> PUT /subscribers/mrrusof@gmail.com HTTP/1.1
> Host: localhost:4567
> User-Agent: curl/7.43.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: text/html;charset=utf-8
< Content-Length: 0
< X-XSS-Protection: 1; mode=block
< X-Content-Type-Options: nosniff
< X-Frame-Options: SAMEORIGIN
< Connection: keep-alive
< Server: thin
<
* Connection #0 to host localhost left intact
{% endhighlight %}

When we register a subscriber, the call stack printed by line `newsletter.rb:19` is the following.

{% highlight asciidoc %}
ruslan$ ./newsletter.rb
== Sinatra (v1.4.7) has taken the stage on 4567 for development with backup from Thin
Thin web server (v1.6.4 codename Gob Bluth)
Maximum connections set to 1024
Listening on localhost:4567, CTRL+C to stop
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1611:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1611:in `block in compile!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:975:in `[]'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:975:in `block (3 levels) in route!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:994:in `route_eval'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:975:in `block (2 levels) in route!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1015:in `block in process_route'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1013:in `catch'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1013:in `process_route'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:973:in `block in route!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:972:in `each'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:972:in `route!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1085:in `block in dispatch!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1067:in `block in invoke'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1067:in `catch'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1067:in `invoke'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1082:in `dispatch!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:907:in `block in call!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1067:in `block in invoke'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1067:in `catch'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1067:in `invoke'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:907:in `call!'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:895:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract/connection_pool.rb:653:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/xss_header.rb:18:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/path_traversal.rb:16:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/json_csrf.rb:18:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/base.rb:49:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/base.rb:49:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/frame_options.rb:31:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-1.6.4/lib/rack/nulllogger.rb:9:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-1.6.4/lib/rack/head.rb:13:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/show_exceptions.rb:25:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:182:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:2013:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1487:in `block in call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1787:in `synchronize'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1487:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:86:in `block in pre_process'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:84:in `catch'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:84:in `pre_process'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:1076:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:1076:in `block in spawn_threadpool'
{% endhighlight %}

The following line of the call stack indicates that the execution of route handler for `/subscribers/:email` is preceded by a call to method `ConnectionManagement.call`.

{% highlight asciidoc %}
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract/connection_pool.rb:653:in `call'
{% endhighlight %}

We are using middleware `ConnectionManagement` because we asked for it in line `newsletter.rb:15`.
The code of `ConnectionManagement` in file `connection_pool.rb` is the following.

{% highlight ruby %}
645 class ConnectionManagement
646   def initialize(app)
647     @app = app
648   end
649
650   def call(env)
651     testing = env['rack.test']
652
653     response = @app.call(env)
654     response[2] = ::Rack::BodyProxy.new(response[2]) do
655       ActiveRecord::Base.clear_active_connections! unless testing
656     end
657
658     response
659   rescue Exception
660     ActiveRecord::Base.clear_active_connections! unless testing
661     raise
662   end
663 end
{% endhighlight %}

The route handler is executed during execution of line `connection_pool.rb:653` and that is when a database connection is created for the current thread.


Line `connnection_pool.rb:655` puportedly clears (releases) active connections, but only only for the current thread.
The reason is that the sequence of calls that starts in that line eventually releases a default connection identified by a thread id.
Consider the code for `ActiveRecord::Base.clear_active_connections!` in `connection_pool.rb`.

{% highlight ruby %}
548 # Returns any connections in use by the current thread back to the pool,
549 # and also returns connections to the pool cached by threads that are no
550 # longer alive.
551 def clear_active_connections!
552   connection_pool_list.each(&:release_connection)
553 end
{% endhighlight %}

Line `connection_pool.rb:552` calls `ConnectionPool#release_connection` for each existing thread.
The code for `ConnectionPool#release_connection` is the following.

{% highlight ruby %}
276 # Signal that the thread is finished with the current connection.
277 # #release_connection releases the connection-thread association
278 # and returns the connection to the pool.
279 def release_connection(with_id = current_connection_id)
280   synchronize do
281     conn = @reserved_connections.delete(with_id)
282     checkin conn if conn
283   end
284 end
{% endhighlight %}

The current connection id is the id of the current thread and is given by line `connection_pool.rb:442`.

{% highlight ruby %}
441 def current_connection_id #:nodoc:
442   Base.connection_id ||= Thread.current.object_id
443 end
{% endhighlight %}

Thus, `ConnectionPool#release_connection` releases the connection that has the same id as the current thread id. When you take from a given pool `p` a connection in one thread and try to release that connection in another thread by a call to `p.release_connection`, you will not release anything.


Given the way `ActiveRecord::Base.clear_active_connections!` works, you may expect that lines `653` and `655` of `connection_pool.rb` execute in the same thread.
They do not.
You may verify this fact by inspecting the thread id and call stack for those lines in the following way.

{% highlight ruby %}
class ConnectionManagement
  def initialize(app)
    @app = app
  end

  def call(env)
    testing = env['rack.test']

    puts '*******************************************************'
    puts 'ConnectionManagement.call'
    puts Kernel.caller
    puts "Thread.current.object_id = #{Thread.current.object_id}"
    puts '*******************************************************'
    response = @app.call(env)
    response[2] = ::Rack::BodyProxy.new(response[2]) do
      puts '*******************************************************'
      puts '::Rack::BodyProxy.new(response[2]) do'
      puts Kernel.caller
      puts "Thread.current.object_id = #{Thread.current.object_id}"
      puts '*******************************************************'
      ActiveRecord::Base.clear_active_connections! unless testing
    end

    response
  rescue Exception
    ActiveRecord::Base.clear_active_connections! unless testing
    raise
  end
end
{% endhighlight %}

The registration of the subscriber produces the following output.

{% highlight asciidoc %}
ruslan$ ./newsletter.rb
== Sinatra (v1.4.7) has taken the stage on 4567 for development with backup from Thin
Thin web server (v1.6.4 codename Gob Bluth)
Maximum connections set to 1024
Listening on localhost:4567, CTRL+C to stop
*******************************************************************************
ConnectionManagement.call
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/xss_header.rb:18:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/path_traversal.rb:16:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/json_csrf.rb:18:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/base.rb:49:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/base.rb:49:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-protection-1.5.3/lib/rack/protection/frame_options.rb:31:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-1.6.4/lib/rack/nulllogger.rb:9:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-1.6.4/lib/rack/head.rb:13:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/show_exceptions.rb:25:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:182:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:2013:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1487:in `block in call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1787:in `synchronize'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1487:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:86:in `block in pre_process'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:84:in `catch'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:84:in `pre_process'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:1076:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:1076:in `block in spawn_threadpool'
Thread.current.object_id = 70307221466660
*******************************************************************************
...
*******************************************************************************
::Rack::BodyProxy.new(response[2]) do
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-1.6.4/lib/rack/body_proxy.rb:18:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-1.6.4/lib/rack/body_proxy.rb:18:in `close'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/response.rb:83:in `close'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:142:in `close_request_response'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:154:in `terminate_request'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/connection.rb:130:in `post_process'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:969:in `call'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:969:in `run_deferred_callbacks'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:194:in `run_machine'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/eventmachine-1.2.0.1/lib/eventmachine.rb:194:in `run'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/backends/base.rb:73:in `start'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/thin-1.6.4/lib/thin/server.rb:162:in `start'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/rack-1.6.4/lib/rack/handler/thin.rb:19:in `run'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1506:in `start_server'
/Users/ruslan/.rvm/gems/ruby-2.2.1/gems/sinatra-1.4.7/lib/sinatra/base.rb:1444:in `run!'
./example.rb:37:in `<main>'
Thread.current.object_id = 70307218276860
*******************************************************************************
{% endhighlight %}

The thread ids and the call stacks are different, thus the call to `ActiveRecord::Base.clear_active_connections!` does not return the connection that was created in thread 70307221466660.
We reach the default maximum count of active connections after registering the fifth subscriber.
The API times out in subsequent requests.

# To split or not to split hairs

I think that ActiveRecord's `ConnectionManagement` and Thin should work together in threaded mode.
I can think of three approaches for reconciling them.

- Approach 1: ActiveRecord's `ConnectionManagement` releases connections for a specific thread.
- Approach 2: [Thin processes each request from beginning to end in the same thread](https://github.com/macournoyer/thin/issues/307).
- Approach 3: [Sinatra-ActiveRecord reconciles ActiveRecord and Thin by offering a substitute for `ConnectionManagement`](https://github.com/janko-m/sinatra-activerecord/issues/62).

However, reconciliation seems to be easier said than done for two reasons.
One is that after [raising the problem to macournoyer](https://github.com/macournoyer/thin/issues/307), I am not sure that Thin is responsible for accomodating to ActiveRecord's `ConnectionManagement` approach to releasing connections.
The other reason is that I am not sure that the [recent elimination of `ConnectionManagement` from ActiveRecord](https://github.com/rails/rails/pull/23807) takes the problem in consideration and thus raising the problem to ActiveRecord may be complicated.

# <a name="solution"></a>How do I release ActiveRecord connections?

Whether Sinatra, ActiveRecord, and Thin reconcile or not, you will still want to clear active connections.
For Sinatra 1.4, ActiveRecord 4.2, and Thin 1.6 running in threaded mode, one way of releasing connections is calling `ActiveRecord::Base.clear_active_connections!` in an after filter, like so.

{% highlight ruby %}
after do
  ActiveRecord::Base.clear_active_connections!
end
{% endhighlight %}

[Some people suggest closing all connections altogether by calling `ActiveRecord::Base.connection.close`](http://stackoverflow.com/questions/13675879/activerecordconnectiontimeouterror), but that's not always necessary.


{% include subscribe.ext %}


# Comments

<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    var disqus_config = function () {
        this.page.url = 'http://ruslanledesma.com/2016/04/23/release-connections-thin-and-active-record.html';  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = '2016-04-23-release-connections-thin-and-active-record'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
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
