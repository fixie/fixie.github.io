---
layout: documentation
title: Fixie - Test Execution
---
# Test Execution

One reason to override the [default convention](../default-convention) with a [custom convention](../custom-conventions) is to define the rules for **test execution**. In your `Convention` you will describe how (and how often) to construct your test classes, as well as what to do before and after each part of the test class lifecycle.

For instance, let's say we want all of our integration tests to be automatically wrapped in a database transaction.  Beside our tests, we place a custom convention class:

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

Here, we're declaring that for each *Fixture* (in other words, each test class instance), wrap it in its own `TransactionScope` instance.  When the test class is constructed, you get a new `TransactionScope`.  After the tests are run for that instance, the `TransactionScope` is disposed.  You could alternatively wrap per-test-case (using the `CaseExecution` property) or per-test-class (using the `ClassExecution` property).

Contrasting with NUnit, `CaseExecution` wrapping corresponds with NUnit's `[SetUp]` and `[TearDown]`, and 'FixtureExecution' wrapping corresponds with NUnit's `[FixtureSetUp]` and `[FixtureTearDown]`.