---
layout: page
title: Are you sure you want to do that?
permalink: /unsubscribe/
exclude_from_menu: true
---

<p><a id="unsubscribe-link">Yes, I want to unsubscribe from the newsletter.</a></p>

<p>Do you like this minimim feature newsletter? <a href="https://github.com/mrrusof/correole">Fork it on Github!</a></p>

<script language="javascript">
var param = document.URL.split("?")[1].split("=")
if(param[0] == "email") {
  $("#unsubscribe-link").attr("href", "https://correole.herokuapp.com/unsubscribe/" + param[1]);
}
</script>