---
layout: post
title: MEAN Stack Issue
published: true
---

[MEAN stack](http://blog.mongodb.org/post/49262866911/the-mean-stack-mongodb-expressjs-angularjs-and) uses M (mongodb), E (ExpressJS), A (AngularJS) and N (NodeJS). As a developer who lived in Microsoft land mostly (other than my one off development of Korean Letters in iOS and Android), I had to give it a try when I heard of it.

There are various github projects that demonstrates application structure using MEAN stack, and [the one from mean.io](https://github.com/linnovate/mean) seems to be most popular.

NodeJS is good, ExpressJS is good, AngularJS is good, but MEAN was confusing to me.

1. Both server-side code and client-side code use javascript, so you want to have clear separation in your directory structure. This is more confusing since both has configuration section and routing section, one in ExpressJS style and one in AngularJS style, in a same language.
1. Server-side (express) uses Jade view engine. Client-side supports AngularJS directives, which all the sample codes are in HTML format. So there is minor translation effort.
1. CSS can be referred by both Jade view page and client-side AngularJS page, and you probably want to keep CSS under /public.

I'm sure I will get used to these and find a clear rule if I spend enough time. But then again, when I tried [ASP.NET MVC - AngularJS - Breeze example](http://www.breezejs.com/samples/breezeangular-template), those issues simply disappeared. Server side codes were in C#. Razor view uses HTML tag as-is, so AngularJS directives were naturally fitting there.

The only problem I have is performance. Node.js development and debugging is a lot faster, compared to ASP.NET MVC debugging.

(When you try [ASP.NET MVC - AngularJS - Breeze example](http://www.breezejs.com/samples/breezeangular-template), make sure you have Visual Studio 2012 with update 2 or later. Otherwise, the VSIX will not be installed as template.)