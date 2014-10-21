---
layout: documentation
title: Fixie - Quick Start
---
## How do I use Fixie?

Create a Class Library project to house your test classes.

Next, [Install NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) and then install the [Fixie NuGet package](https://www.nuget.org/packages/Fixie) from the package manager console:

    PM> Install-Package Fixie

Install a third-party assertion library.  The example below assumes you've installed [Should](https://nuget.org/packages/Should/).

Add test classes and test cases to your testing project, following the [Default Convention](default-convention):

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

*To use a different style, you can create a [Custom Convention](custom-conventions).*

Use the console runner (should be at `your-solution-dir/packages/Fixie.x.x.x/lib/netXX/`) from a command line to execute your tests:

    `Fixie.Console.exe path/to/your/test/project.dll`
    
Use the TestDriven.NET runner from within Visual Studio, using the same keyboard shortcuts you would use for NUnit tests.