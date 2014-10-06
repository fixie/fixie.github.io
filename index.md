---
layout: default
title: Fixie
---
<div class="one-third column">
<h1>Default Convention</h1>
<h2>I just want to write a test.</h2>
<p>In your test project, add a class whose name ends with 'Tests'. The public methods are assumed to be tests. This is the 'default convention'.</p>
</div>
<div class="two-thirds column">
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
</div>

<hr/>

<div class="one-third column">
<h1>No Built-In Assertions</h1>
<h2>That doesn't even compile! What is this, amateur hour?</h2>
<p>Oh, right. Fixie has no built-in assertion library and never will. This example works when you use the third-party Should library.<p>
</div>
<div class="two-thirds column">
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
</div>

<hr />

<div class="one-third column">
<h1>Custom Conventions</h1>
<h2>But I'm used to NUnit and I like having explicit [Test] attributes.</h2>
<p>Tell Fixie what your tests look like, by including a custom convention in your test project. It'll supercede the default convention. With this convention in place, Fixie will only recognize [Test] methods as tests.</p>
</div>
<div class="one-third column">
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
</div>
<div class="one-third column">
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
</div>

<hr />

<div class="one-third column">
<h1>Setup and Teardown</h1>
<h2>But I miss NUnit's [SetUp] and [TearDown], too. What if I want to prepare the system under test in one place, and clean up after it after the tests?</h2>
<p>Like xUnit.NET, Fixie's default is to construct one instance of your test class for each test.  The constructor is your setup, and Dispose() is your teardown.</p>
</div>
<div class="two-thirds column">
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
</div>

<hr />

<div class="one-third column">
<h1>Construction Frequency</h1>
<h2>It reconstructs the class for every test!? What if I have costly state that could be shared across all the test in the class?</h2>
<p>Your custom convention can declare that construction of the test classes should happen once per class. Then the constructor is like NUnit's [FixtureSetUp] while the Dispose() method is like NUnit's [FixtureTearDown].</p>
</div>
<div class="two-thirds column">
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
</div>
