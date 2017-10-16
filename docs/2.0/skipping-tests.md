---
layout: documentation
title: Fixie - Skipping Tests
---
# Skipping Tests

## How do I skip tests?

Don't skip tests.

*No, really, how do I skip tests?*

The default convention does not support skipped tests. In a custom convention, use the `Skip(...)` method to define what makes a test skipped. `Skip(...)` accepts a delegate of type `Func<Case, bool>`. In other words, for any given test case, your delegate must return true when the test case should be skipped.

You may want to skip based on an attribute, an attribute with an expiration date, a naming convention, or some other rule. Here we define a `[Skip]` attribute and a custom convention which looks for it on test methods:

{% highlight csharp %}
[AttributeUsage(AttributeTargets.Method, Inherited = false, AllowMultiple = false)]
public class SkipAttribute : Attribute { }

public class CustomConvention : Convention
{
    public CustomConvention()
    {
        Classes
            .NameEndsWith("Tests");
     
        Methods
            .Where(method => method.IsVoid());
     
        CaseExecution
            .Skip(@case => @case.Method.HasOrInherits<SkipAttribute>());
    }
}
{% endhighlight %}

Now, any test method marked with `[Skip]` will be skipped by the test runner.

The `Skip(...)` method has an overload which accepts a skip reason provider, a delegate of type `Func<Case, string>`.  When provided, this delegate is called in order to generate a skip reason string to include in the output. For instance, we could modify the above example by including a `Reason` property on the `SkipAttribute` class, and then declare that this property is the source of skip reasons:

{% highlight csharp %}
[AttributeUsage(AttributeTargets.Method, Inherited = false, AllowMultiple = false)]
public class SkipAttribute : Attribute
{
    public string Reasons { get; set; }
}

public class CustomConvention : Convention
{
    public CustomConvention()
    {
        Classes
            .NameEndsWith("Tests");
     
        Methods
            .Where(method => method.IsVoid());
     
        CaseExecution
            .Skip(@case => @case.Method.HasOrInherits<SkipAttribute>(),
                  @case => @case.Method.GetCustomAttribute<SkipAttribute>(true).Reason);
    }
}
{% endhighlight %}

The `Skip(...)` method can be called multiple times, where each call introduces a distinct rule for skipping tests. When the test runner is considering a given test case, each skip rule is applied in order until one of them flags the test case as skipped.  That rule's own reason string is used to describe the skip.  For instance, we could modify the above example to also emulate NUnit's own `[Explicit]` attribute, in which a test will be skipped unless it is explicitly selected for execution in isolation. We'd like the `[Explicit]` rule to take precedence over the normal `[Skip]` rule, so we declare the `[Explicit]` rule first:

{% highlight csharp %}
[AttributeUsage(AttributeTargets.Method, Inherited = false, AllowMultiple = false)]
public class SkipAttribute : Attribute
{
    public string Reasons { get; set; }
}

[AttributeUsage(AttributeTargets.Method, Inherited = false, AllowMultiple = false)]
public class ExplicitAttribute : Attribute { }

public class CustomConvention : Convention
{
    public CustomConvention()
    {
        Classes
            .NameEndsWith("Tests");
     
        Methods
            .Where(method => method.IsVoid());
     
        CaseExecution
            .Skip(SkipDueToExplicitAttribute,
                  @case => "[Explicit] tests run only when they are individually selected for execution.")
            .Skip(@case => @case.Method.HasOrInherits<SkipAttribute>(),
                  @case => @case.Method.GetCustomAttribute<SkipAttribute>(true).Reason);
    }
    
    bool SkipDueToExplicitAttribute(Case @case)
    {
        var method = @case.Method;

        var isMarkedExplicit = method.Has<ExplicitAttribute>();

        return isMarkedExplicit && TargetMember != method;
    }
}
{% endhighlight %}

Defining multiple skip rules with these `Func`-accepting overloads can make a convention class hard to read. The `Skip(...)` method has overloads which allow you to specify custom `SkipBehavior` classes, each of which defines the skip predicate and skip reason generator as normal methods intead of `Func`s.  We separate concerns and keep our convention class easier to read at a glance:

{% highlight csharp %}
[AttributeUsage(AttributeTargets.Method, Inherited = false, AllowMultiple = false)]
public class SkipAttribute : Attribute
{
    public string Reasons { get; set; }
}

[AttributeUsage(AttributeTargets.Method, Inherited = false, AllowMultiple = false)]
public class ExplicitAttribute : Attribute { }

public class CustomConvention : Convention
{
    public CustomConvention()
    {
        Classes
            .NameEndsWith("Tests");
     
        Methods
            .Where(method => method.IsVoid());
     
        CaseExecution
            .Skip(new SkipDueToExplicitAttribute(TargetMember))
            .Skip<SkipDueToSkipAttribute>();
    }
}

class SkipDueToExplicitAttribute : SkipBehavior
{
    private readonly MemberInfo _targetMember;

    public SkipDueToExplicitAttribute(MemberInfo targetMember)
    {
        _targetMember = targetMember;
    }

    public override bool SkipCase(Case @case)
    {
        var method = @case.Method;

        var isMarkedExplicit = method.Has<ExplicitAttribute>();

        return isMarkedExplicit && _targetMember != method;
    }

    public override string GetSkipReason(Case @case)
        => "[Explicit] tests run only when they are individually selected for execution.";
}

class SkipDueToSkipAttribute : SkipBehavior
{
    public override bool SkipCase(Case @case)
        => return @case.Method.HasOrInherits<SkipAttribute>();

    public override string GetSkipReason(Case @case)
        => @case.Method.GetCustomAttribute<SkipAttribute>().Reason;
}
{% endhighlight %}
