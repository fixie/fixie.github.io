---
layout: documentation
title: Fixie - Parameterized Test Methods
---
## Parameterized Test Methods

With the [default convention](../default-convention), Fixie is unable to run parameterized test methods, because it doesn't know where those input parameters should come from.  In a [custom convention](../custom-conventions), though, you can define the meaning of parameterized test methods.

In a custom convention, use the `Parameters` property to add a `ParameterSource` as the origin of test method parameters.  Your parameter source provides a single method, `IEnumerable<object[]> GetParameters(MethodInfo method)`.  In other words, for any given method, your parameter source must produce a series of object arrays.  Each object array corresponds with a single call to the test method.

You may want parameters to come from attributes, your IoC container, AutoFixture, metadata from the filesystem... anything that yields object arrays.

### Example - Parameters from Attributes

Let's say you want test method parameters to come from `[Input]` attributes.  Define `InputAttribute`:

{% highlight csharp %}
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true)]
public class InputAttribute : Attribute
{
    public InputAttribute(params object[] parameters)
    {
        Parameters = parameters;
    }
 
    public object[] Parameters { get; private set; }
}
{% endhighlight %}

Next, place `InputAttribute`s on parameterized tests:

{% highlight csharp %}
public class CalculatorTests
{
    readonly Calculator calculator;
 
    public CalculatorTests()
    {
        calculator = new Calculator();
    }
 
    [Input(2, 3, 5)]
    [Input(3, 5, 8)]
    public void ShouldAdd(int a, int b, int expectedSum)
    {
        calculator.Add(a, b).ShouldEqual(expectedSum);
    }
 
    [Input(5, 3, 2)]
    [Input(8, 5, 3)]
    [Input(10, 5, 5)]
    public void ShouldSubtract(int a, int b, int expectedDifference)
    {
        calculator.Subtract(a, b).ShouldEqual(expectedDifference);
    }
}
{% endhighlight %}

Lastly, define a custom convention which tells Fixie how to look up the parameters for each call to these test methods:

{% highlight csharp %}
public class CustomConvention : Convention
{
    public CustomConvention()
    {
        Classes
            .NameEndsWith("Tests");

        Methods
            .Where(method => method.IsVoid());

        Parameters
            .Add<FromInputAttributes>();
    }

    class FromInputAttributes : ParameterSource
    {
        public IEnumerable<object[]> GetParameters(MethodInfo method)
        {
            return method.GetCustomAttributes<InputAttribute>(true).Select(input => input.Parameters);
        }
    }
}
{% endhighlight %}

The `Parameters.Add` method is overloaded.  Above, we specify a type name, `FromInputAttributes`, allowing Fixie to construct the instance for you when needed.  This overload naturally assumes a zero-argument constructor.  If you would rather construct an instance of your parameter source yourself, such as when it has a more interesting constructor, call the constructor yourself and pass the instance to the `Add` method:

{% highlight csharp %}
Parameters
    .Add(new FromInputAttributes());
}
{% endhighlight %}

Defining your parameter source as a class makes it easy to share logic across multiple conventions, but may be overkill for a single-use source for a single convention. You could instead define your `Input` attributes with a lambda expression:

{% highlight csharp %}
Parameters
    .Add(method => method.GetCustomAttributes<InputAttribute>(true)
                         .Select(input => input.Parameters));
{% endhighlight %}

### Generic Parameterized Tests

When the system under test uses generics, you may want your parameterized test method to be generic as well. If a parameterized method happens to be a generic method, Fixie compares the runtime type of each incoming parameter value against the generic method declaration in order to pick the best concrete type for each generic type parameter.  This step is necessary because reflection does not allow you to simply pass an `object[]` of parameter values when invoking a generic method through its `MethodInfo`.  Fixie must first convert the generic method definition's `MethodInfo` into a more specific `MethodInfo` with the type arguments resolved.  For instance, consider what happens when we have a generic test method using the `[Input]` attribute as defined above:

{% highlight csharp %}
[Input(true)]
[Input(1)]
[Input("A")]
public void GenericTestMethod<T>(T input)
{
    Console.WriteLine(typeof(T).Name);
}
{% endhighlight %}

The output of running this test method is:

    Boolean
    Int32
    String

Instead of receiving the input as an `object` each time, the correct concrete type is substituted for the `T`. If there is any ambiguity over what concrete type should be selected, though, `object` will be assumed.