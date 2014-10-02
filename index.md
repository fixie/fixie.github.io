---
layout: default
title: Fixie
---
<img src="images/fixie_256.png"/>

Conventional Testing for .NET

Fixie lets you describe what your tests look like, and how to run them.

1. [GitHub](https://github.com/plioi/fixie)
2. [Docs](https://github.com/plioi/fixie/blob/master/README.md)

---

# Default Convention

## I just want to write a test.

In your test project, add a class whose name ends with 'Tests'. The public methods are assumed to be tests. This is the 'default convention'.

{% highlight csharp %}
public class CalculatorTests
{
    public void ShouldAdd()
    {
        var calculator = new Calculator();
        calculator.Add(2, 3).ShouldEqual(5);
    }

    public void ShouldSubtract()
    {
        var calculator = new Calculator();
        calculator.Subtract(5, 3).ShouldEqual(2);
    }
}
{% endhighlight %}

---

# No Built-In Assertions

## That doesn't even compile! What is this, amateur hour?

Oh, right. Fixie has no built-in assertion library and never will. This example works when you use the third-party Should library.

{% highlight csharp %}
using Should;

public class CalculatorTests
{
    public void ShouldAdd()
    {
        var calculator = new Calculator();
        calculator.Add(2, 3).ShouldEqual(5);
    }

    public void ShouldSubtract()
    {
        var calculator = new Calculator();
        calculator.Subtract(5, 3).ShouldEqual(2);
    }
}
{% endhighlight %}

---

# Custom Conventions

## But I'm used to NUnit and I like having explicit [Test] attributes.

Tell Fixie what your tests look like, by including a custom convention in your test project. It'll supercede the default convention. With this convention in place, Fixie will only recognize [Test] methods as tests.

{% highlight csharp %}
//CustomConvention.cs
using System;
using Fixie;

public class CustomConvention : Convention
{
    public CustomConvention()
    {
        Classes
            .NameEndsWith("Tests");

        Methods
            .HasOrInherits<TestAttribute>();
    }
}

[AttributeUsage(AttributeTargets.Method)]
public class TestAttribute : Attribute { }
{% endhighlight %}

{% highlight csharp %}
//CalculatorTests.cs
using Should;

public class CalculatorTests
{
    [Test]
    public void ShouldAdd()
    {
        var calculator = new Calculator();
        calculator.Add(2, 3).ShouldEqual(5);
    }

    [Test]
    public void ShouldSubtract()
    {
        var calculator = new Calculator();
        calculator.Subtract(5, 3).ShouldEqual(2);
    }
}
{% endhighlight %}

---

# Setup and Teardown

## But I miss NUnit's [SetUp] and [TearDown], too. What if I want to prepare the system under test in one place, and clean up after it after the tests?

Like xUnit.NET, Fixie's default is to construct one instance of your test class for each test.  The constructor is your setup, and Dispose() is your teardown.

{% highlight csharp %}
using System;
using Should;

public class CalculatorTests : IDisposable
{
    private readonly Calculator calculator;

    public CalculatorTests()
    {
        calculator = new Calculator();
    }

    public void ShouldAdd()
    {
        calculator.Add(2, 3).ShouldEqual(5);
    }

    public void ShouldSubtract()
    {
        calculator.Subtract(5, 3).ShouldEqual(2);
    }

    public void Dispose()
    {
        calculator.Dispose();
    }
}
{% endhighlight %}

---

# Construction Frequency

## It reconstructs the class for every test!? What if I have costly state that could be shared across all the test in the class?

Your custom convention can declare that construction of the test classes should happen once per class. Then the constructor is like NUnit's [FixtureSetUp] while the Dispose() method is like NUnit's [FixtureTearDown].

{% highlight csharp %}
using Fixie;

public class CustomConvention : Convention
{
    public CustomConvention()
    {
        Classes
            .NameEndsWith("Tests");

        ClassExecution
            .CreateInstancePerClass();
    }
}
{% endhighlight %}
