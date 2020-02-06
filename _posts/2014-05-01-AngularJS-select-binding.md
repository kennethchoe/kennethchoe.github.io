---
layout: post
title: "AngularJS Select Binding with C# Enum"
published: true
---

### Problem

I have C# enum.

{% highlight c# %}
public enum ClueType
{
    Text,
    Image
}
    
public class Clue
{
    public ClueType ClueType { get; set; }
}
{% endhighlight %}

I have Web API that provides the enum as `System.Web.Mvc.SelectListItem`.

{% highlight c# %}
public class ClueTypesController: ApiController
{
    [System.Web.Http.HttpGet]
    public IEnumerable<SelectListItem> Get()
    {
        var list = EnumHelper.GetSelectList(typeof(ClueType));

        return list;
    }
}
{% endhighlight %}

And finally, I have AngularJS partial html that uses the enum. `core.ClueTypes` holds the result of the Web API.

{% highlight html %}
<select ng-model="clue.ClueType"
    ng-options="c.Value as c.Text for c in core.ClueTypes">
</select>
{% endhighlight %}

When I run it, the dropdown shows item list well and it saves the selected value well. But when it loads, `ng-model="clue.ClueType"` doesn't seem to work. It always selects blank item with `?` value.

Angular-rendered HTML content:
{% highlight html %}
<select ng-model="gate.Clue.ClueType" 
        ng-options="c.Value as c.Text for c in core.ClueTypes" 
        class="ng-pristine ng-valid">
    <option value="?" selected="selected"></option>
    <option value="0">Text</option>
    <option value="1">Image</option>
</select>
{% endhighlight %}

What went wrong?

### Solution

`Clue` class's `ClueType` is int value, and `core.ClueTypes` holds string type value for `Value`. Instead of raising any error, AngularJS silently ignores the int value.

You can solve it by making these two types match. Here I am giving int type `Value` to `core.ClueTypes`, making both `core.ClueTypes.Value` and `clue.ClueType` to be int.

new Web API to return int type Value:
{% highlight c# %}
public class ClueTypesController: ApiController
{
    [System.Web.Http.HttpGet]
    public IEnumerable<NumericValueSelectListItem> Get()
    {
        var list = EnumHelper.GetSelectList(typeof(ClueType))
            .Select(x => new NumericValueSelectListItem()
                {
                    Text = x.Text,
                    Value = int.Parse(x.Value)
                });

        return list;
    }
}

public class NumericValueSelectListItem
{
    public string Text { get; set; }
    public int Value { get; set; }
}
{% endhighlight %}

I ended up with my own `EnumHelper` since I needed spaces in the Text, which also made my Web API controller code briefer.