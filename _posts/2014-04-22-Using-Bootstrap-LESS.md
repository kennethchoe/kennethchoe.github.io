---
layout: post
title: Using Bootstrap LESS on ASP.NET MVC Project
published: true
---

There are two ways I tried for using Bootstrap LESS.

### Option 1: [Use dotless](http://brendanforster.com/blog/yet-another-implement-less-in-aspnetmvc-post.html)

Downside: [dotless does not understand &amp;:extend() syntax](http://stackoverflow.com/questions/21940204/dotless-on-azure-web-project-doesnt-understand-extend), which is used in 3.1.x bootstrap.

### Option 2: [Use V8 Engine](http://www.neowin.net/forum/topic/1191221-guide-to-getting-less-working-with-twitter-bootstrap-in-mvc-5/)

Downside: this approach can compile 3.1.x bootstrap, but does not work on GoDaddy windows web hosting environment. I don't have details of issues with me for the moment.


Problem with GoDaddy was deal breaker for me, so I had to use Bootstrap 3.0.3. You can install prior version of Bootstrap by running command on the Tools => NuGet Package Manager => Package Manager Console:

{% highlight powershell %}
PM> Install-Package twitter.bootstrap.less -ProjectName YourProjectName -Version 3.0.3
{% endhighlight %}