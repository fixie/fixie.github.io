---
layout: documentation
title: Fixie - Test Discovery
---
## Defining Test Discovery in Custom Conventions

One reason to override the [default convention](../default-convention) with a [custom convention](../custom-conventions) is to define the rules for **test discovery**. In your `Convention` you will describe which classes are test fixtures and which methods are tests.

{% highlight csharp %}
using Fixie;

namespace IntegrationTests
{
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
}
{% endhighlight %}

Several sample conventions are available under the [Fixie.Samples](https://github.com/plioi/fixie/tree/master/src/Fixie.Samples) project:

* [Imitate NUnit](https://github.com/plioi/fixie/blob/master/src/Fixie.Samples/NUnitStyle/CustomConvention.cs)
* [Imitate xUnit](https://github.com/plioi/fixie/blob/master/src/Fixie.Samples/xUnitStyle/CustomConvention.cs)
* [Simplified NUnit for cleaner test inheritance](https://github.com/plioi/fixie/blob/master/src/Fixie.Samples/LowCeremony/CustomConvention.cs)
* [Construct integration test classes with your IoC container](https://github.com/plioi/fixie/blob/master/src/Fixie.Samples/IoC/CustomConvention.cs)
* [Support arbitrary command line flags such as NUnit-style categories](https://github.com/plioi/fixie/blob/master/src/Fixie.Samples/Categories/CustomConvention.cs)