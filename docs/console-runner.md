---
layout: documentation
title: Fixie - Console Runner
---
# Console Runner

## How do I run my tests from the command line?

Use the console runner (at `your-solution-dir/packages/Fixie.x.x.x/lib/netXX/`) from a command line to execute your tests:

    Fixie.Console.exe path/to/your/test/project.dll

## How do I report results to my continuous integration server?

By default, when the console runner is invoked by TeamCity, the console output is formatted so that TeamCity can detect individual test results for display.

You can generate familiar NUnit- or xUnit-style XML reports by including an extra command line argument. These file formats are often supported by other CI tools:

    Fixie.Console.exe path/to/your/test/project.dll --NUnitXml TestResult.xml

or

    Fixie.Console.exe path/to/your/test/project.dll --XUnitXml TestResult.xml

If you opt into an XML report format under TeamCity, you may experience your tests being doubly-reported: once from the XML file, and once from the TeamCity-specific console output formatting.  In this case, you can explicitly suppress the TeamCity console output:

    Fixie.Console.exe path/to/your/test/project.dll  --NUnitXml TestResult.xml --TeamCity off

## How do I make custom options available to my custom conventions?

Arbitrary key-value pairs can be passed via the command line and made available to your [custom conventions](../custom-conventions).

For example, you may want to implement support for [NUnit-style "categories"](http://www.nunit.org/index.php?p=category&r=2.6.4). Here we define category attributes and a custom convention which looks for them on test methods:

{% highlight csharp %}
[AttributeUsage(AttributeTargets.Method, Inherited = false)]
public abstract class CategoryAttribute : Attribute
{
    public string Name
    {
        get { return GetType().Name.Replace("Attribute", ""); }
    }
}

[AttributeUsage(AttributeTargets.Method, Inherited = false)]
public class CategoryAAttribute : CategoryAttribute { }

[AttributeUsage(AttributeTargets.Method, Inherited = false)]
public class CategoryBAttribute : CategoryAttribute { }

public class CustomConvention : Convention
{
    public CustomConvention()
    {
        var desiredCategories = Options["include"].ToArray();
        var shouldRunAll = !desiredCategories.Any();

        Classes
            .NameEndsWith("Tests");

        Methods
            .Where(method => method.IsVoid())
            .Where(method => shouldRunAll || MethodHasAnyDesiredCategory(method, desiredCategories));
    }

    static bool MethodHasAnyDesiredCategory(MethodInfo method, string[] desiredCategories)
    {
        return Categories(method).Any(testCategory => desiredCategories.Contains(testCategory.Name));
    }

    static CategoryAttribute[] Categories(MethodInfo method)
    {
        return method.GetCustomAttributes<CategoryAttribute>(true).ToArray();
    }
}
{% endhighlight %}

Armed with these attributes and this custom convention, test methods can declare themselves to belong to various categories:

{% highlight csharp %}
public class CalculatorTests
{
    readonly Calculator calculator;

    public CalculatorTests()
    {
        calculator = new Calculator();
    }

    [CategoryA]
    public void ShouldAdd()
    {
        calculator.Add(2, 3).ShouldEqual(5);
    }

    [CategoryB]
    public void ShouldSubtract()
    {
        calculator.Add(2, 3).ShouldEqual(5);
    }
}
{% endhighlight %}

When you run the tests like normal, the `Options` collection is empty, so all of the *possible* test methods are treated as test methods and executed.  However, you may specify `Options` values at the command line using `key=value` pairs:

    Fixie.Console.exe path/to/your/test/project.dll --parameter include=CategoryA

Now, the `Options` collection *will* contain a value for the key "include", and *only* methods with `[CategoryA]` will run.  The same key can be specified multiple times at the command line, and as a result the `Options` collection will make *all* of them available to your convention:

    Fixie.Console.exe path/to/your/test/project.dll --parameter include=CategoryA --parameter include=CategoryB
