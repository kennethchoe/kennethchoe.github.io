---
layout: post
title: "Bookmarks - ELMAH on ASP.NET MVC"
published: true
---

### ELMAH setup

After installing ELMAH.MVC using NuGet, you need to add two verb lines.

web.config:
{% highlight xml %}
<system.web>
  <httpHandlers>
    ...
    <add verb="POST,GET,HEAD" path="elmah.axd" type="Elmah.ErrorLogPageFactory, Elmah" />
    ...
  </httpHandlers>
</system.web>
<system.webServer>
  <handlers>
    ...
    <add name="Elmah" verb="POST,GET,HEAD" path="elmah.axd" type="Elmah.ErrorLogPageFactory, Elmah" />
    ...
  </handlers>
<system.webServer>
{% endhighlight %}
[http://stackoverflow.com/questions/933554/elmah-not-working-with-asp-net-site/1175023#1175023](http://stackoverflow.com/questions/933554/elmah-not-working-with-asp-net-site/1175023#1175023)

### ELMAH setup for Web API

Install Elmah.Contrib.WebApi using NuGet and add this line on WebApiConfig.cs.

{% highlight c# %}
public static class WebApiConfig
{
    public static void Register(HttpConfiguration config)
    {
        config.Routes.MapHttpRoute(
            name: "DefaultApi",
            routeTemplate: "api/{controller}/{id}",
            defaults: new { id = RouteParameter.Optional }
        );

        config.Filters.Add(new ElmahHandleErrorApiAttribute());
    }
}
{% endhighlight %}

[http://blogs.msdn.com/b/webdev/archive/2012/11/16/capturing-unhandled-exceptions-in-asp-net-web-api-s-with-elmah.aspx](http://blogs.msdn.com/b/webdev/archive/2012/11/16/capturing-unhandled-exceptions-in-asp-net-web-api-s-with-elmah.aspx)
[https://github.com/rdingwall/elmah-contrib-webapi](https://github.com/rdingwall/elmah-contrib-webapi)