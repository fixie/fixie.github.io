---
title: Fixie - Test Discovery
---
# Test Discovery

## Defining Test Discovery in Custom Conventions

One reason to override the [default convention](../default-convention) with a [custom convention](../custom-conventions) is to define the rules for **test discovery**. In your `Convention` you will describe which classes are test classes and which methods are tests.

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
        }
    }
}
{% endhighlight %}

### Which Classes Contain Tests?

How do you tell Fixie which classes should be considered test classes? NUnit uses any class decorated with a `[TestFixture]` attribute. xUnit uses any class that contains at least one method decorated with a `[Fact]` attribute. You could decide to use a naming convention and say that test classes are any class ending with the word "Tests".

{% highlight csharp %}
Classes
    .NameEndsWith("Tests");
{% endhighlight %}

In the constructor of your `Convention` class, use the `Classes` property to describe the rules for what makes a class a test class. There are some built-in methods for common scenarios; anything else can be defined via the `Where()` method.

`NameEndsWith(params string[] suffixes)` lets you specify one or more strings. Classes whose name ends with any of those strings will be test classes (i.e., the suffixes are joined by *or*).

`Has<TAttribute>()` looks for classes decorated with that attribute, while `HasOrInherits<TAttribute>()` picks up classes with that attribute plus classes that inherit from a class with that attribute.

For any additional rules, use the `Where()` method. It takes a `Func` that accepts a `Type` (i.e., a class that is a candidate for being a test class) and returns a Boolean that will be true for types that are test classes.

You can add multiple conditions by chaining the methods together. A class must satisfy *all* the conditions to be considered a test class (i.e., conditions are joined by *and*).

{% highlight csharp %}
Classes
    .NameEndsWith("Tests")
    .Has<TestFixtureAttribute>;
{% endhighlight %}

Classes that meet all of your conditions will be considered test classes and will be instantiated according to the instructions you specify in the [test execution](../test-execution) section of your `Convention`.

### Which Methods Are Tests?

The methods within a test class are all candidates for being tests. Use the `Methods` property to tell Fixie how to determine which of those methods should be executed as tests.

{% highlight csharp %}
Methods
    .Where(method => method.IsVoid());
{% endhighlight %}

Similar to the way you defined rules for `Classes` above, `Methods` offers some built-in methods, in addition to a `Where()` method.

`Has<TAttribute>()` includes methods decorated with that attribute. `HasOrInherits<TAttribute>()` includes those plus methods that pick up that attribute by inheriting it from an ancestor.

Arbitrary rules can be defined using the `Where()` method. It takes a `Func` that accepts a method's `MethodInfo` metadata and returns a Boolean that will be true for methods that should be executed as tests.

You can add multiple conditions by chaining the methods together. A method must satisfy *all* the conditions to be considered a test (i.e., conditions are joined by *and*).

### Sample Conventions

Several sample conventions are available under the [Fixie.Samples](https://github.com/fixie/fixie/tree/{{page.codetag}}/src/Fixie.Samples) project. Check how the `Classes` and `Methods` properties are used in the constructors of the `CustomConvention` classes in the [NUnit style](https://github.com/fixie/fixie/blob/{{page.codetag}}/src/Fixie.Samples/NUnitStyle/CustomConvention.cs) and [xUnit style](https://github.com/fixie/fixie/blob/{{page.codetag}}/src/Fixie.Samples/xUnitStyle/CustomConvention.cs) examples.
