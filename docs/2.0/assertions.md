---
title: Fixie - Assertions
---
# Assertions

## How do I make assertions?

Most test frameworks such as NUnit or xUnit include their own assertion libraries so that you can make statements like this:

{% highlight csharp %}
Assert.AreEqual(expected, actual);
{% endhighlight %}

Assertion libraries are orthogonal to test frameworks.  Your choice of assertion library should be independent of your choice of test framework.  Therefore, Fixie will *never* include an assertion library.

Here are some useful third-party assertion libraries:

* [Should](https://nuget.org/packages/Should/)
* [Shouldly](https://nuget.org/packages/Shouldly/)
* [Fluent Assertions](https://www.nuget.org/packages/FluentAssertions)