---
layout: post
title: "ProTip: Poor man's pomodoro timer for Mac OS X"
date: 2016-12-29
author: Ruslan Ledesma-Garza
summary: "
One Bash command for timing regular 25 minutes pomodoro session and
one for 5 minutes breaks.
Because, why use a fancy app or website when you can use
an arcane piece of AppleScript in your beautiful Mac?
"
---

{{page.summary}}

**Copy and paste in your `~/.bashrc`.**
<script src="https://gist.github.com/mrrusof/30df845e29169c0c45910af3fe94d0c1.js"></script>

You are welcome.

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
