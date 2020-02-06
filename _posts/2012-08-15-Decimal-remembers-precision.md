---
layout: post
title: Decimal Remembers Precision
published: true
---

I was puzzled by decimal type - when I load currency value from database, it is serialized as 0.0000, whereas if I assign the value directly within c# code it is serialized as 0. Thus, my `AssertEqual()` function fails.

{% highlight csharp %}
[Test]
public void ObjectShouldBePersisted()
{
    var obj = _service.BuildNew();
    AssignTestValues(obj);

    var result = _service.Save(obj);
    result.IsSaved.ShouldBeTrue();

    var objFromDb = _service.GetById(result.Id);

    AssertEqual(obj, objFromDb);
}

private void AssertEqual(object before, object after)
{
    var beforeContent = JsonConvert.SerializeObject(before);
    var afterContent = JsonConvert.SerializeObject(after);

    beforeContent.ShouldEqual(afterContent);
}
{% endhighlight %}

After some digging, I found the problem can be simplified into these two sets of `Debug.Prints`.

{% highlight csharp %}

Debug.Print(JsonConvert.SerializeObject((decimal)0));                           // returns 0
Debug.Print(JsonConvert.SerializeObject((decimal)Decimal.Parse("0.0000")));     // returns 0.0000

Debug.Print(JsonConvert.SerializeObject((double)0));                            // returns 0
Debug.Print(JsonConvert.SerializeObject((double)Double.Parse("0.0000")));       // returns 0

{% endhighlight %}

The difference is because decimal remembers precision. You can confirm that by using `Decimal.GetBits()`. Even though 0 and 0.0000 are all 0, their `GetBits()` result is different.