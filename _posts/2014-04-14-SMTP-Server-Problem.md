---
layout: post
title: SMTP Server - When you believe you did everything right
published: true
---

I was working on "Contact Us" page, and wasted embarrassingly long time troubleshooting because of one interesting behavior with gmail.

My code looks just like the one in Scott Gu's article, [Sending Email with System.Net.Mail](http://weblogs.asp.net/scottgu/archive/2005/12/10/432854.aspx). Everything on my end was right. Using [Pickup directory configuration](http://msdn.microsoft.com/en-us/library/ms164241%28v=vs.110%29.aspx) also proved that the code generates email request properly. When hooked up, my SMTP server did not respond any errors.

Here is the gotcha:
**If you send email from your own email address, Gmail will silently ignore it.** This is the behavior as of today, 4/2/2014. It doesn't go to Junk Email. It is just ignored. Your SMTP server will say everything went well. But you don't get an email.

So when you test, try different From email account before you doubt your code or the SMTP server.

Of course you can send an email to yourself within Gmail UI.