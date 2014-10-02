---
layout: default
title: Fixie
---
<img src="images/fixie_256.png"/>

Conventional Testing for .NET

Fixie lets you describe what your tests look like, and how to run them.

{% highlight csharp %}
public class IntegrationTestConvention : Convention
{
    public IntegrationTestConvention()
    {
        Classes
            .NameEndsWith("Tests");

        Methods
            .Where(method => method.IsVoid());

        FixtureExecution
            .Wrap<Transaction>();
    }

    class Transaction : FixtureBehavior
    {
        public void Execute(Fixture context, Action next)
        {
            using (new TransactionScope())
                next();
        }
    }
}
{% endhighlight %}

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
