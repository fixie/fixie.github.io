---
layout: documentation
title: Fixie - Test Lifecycle
---
# Test Lifecycle

## Defining Test Lifecycle in Custom Conventions

In a [custom `Convention`](../custom-conventions) you can define the rules for **test lifecycle**, i.e., how often an instance of a test class is instantiated.

The two lifecycle schemes are "per case" and "per class", and you specify which one to use in the `ClassExecution` definition in your `Convention`. (To define additional behaviors for `ClassExecution`, `CaseExecution`, and `FixtureExecution`, see [Test Execution](../test-execution).)

`ClassExecution.CreateInstancePerCase()` will, for each test method within a test class, call the class's constructor, call the test method, then call the class's destructor. (See an example [xUnit-style `Convention`](https://github.com/fixie/fixie/blob/master/src/Fixie.Samples/xUnitStyle/CustomConvention.cs) that uses `CreateInstancePerCase`.) This makes the class's constructor and destructor act as *test* setup and teardown. This is the default behavior.

{% highlight csharp %}
using Fixie;

namespace UnitTests
{
    public class UnitTestConvention : Convention
    {
        public UnitTestConvention()
        {
            ClassExecution
                .CreateInstancePerCase();
        }
    }
}
{% endhighlight %}

`ClassExecution.CreateInstancePerClass()` will, for each test class, call the class's constructor, call each test method within the class, then call the class's destructor. (See an example [NUnit-style `Convention`](https://github.com/fixie/fixie/blob/master/src/Fixie.Samples/NUnitStyle/CustomConvention.cs) that uses `CreateInstancePerClass`.) This makes the class's constructor and destructor act as test *class* setup and teardown.

{% highlight csharp %}
using Fixie;

namespace UnitTests
{
    public class UnitTestConvention : Convention
    {
        public UnitTestConvention()
        {
            ClassExecution
                .CreateInstancePerClass();
        }
    }
}
{% endhighlight %}

## Using a Factory Method Instead of the Default Constructor

`ClassExecution.UsingFactory()` allows you to specify a method (or a `Func<Type, object>`) that will create the test class instance instead of calling the class's default constructor. (See an example [`Convention` using a factory](https://github.com/fixie/fixie/blob/master/src/Fixie.Samples/IoC/CustomConvention.cs) to fetch instances from an [IoC container](http://www.martinfowler.com/articles/injection.html).)

Other variations on what to do before and after test classes and cases can be defined with `Wrap` behaviors, described in [Test Execution](../test-execution).