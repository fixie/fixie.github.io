---
layout: documentation
title: Fixie - Reusing Conventions
---
## Reusing Conventions

As described in [Custom Conventions](../custom-conventions), a custom subclass of `Convention` will reach out into the containing test assembly, looking for tests to execute.  This default behavior is useful in simple projects, but is insufficient in two scenarios:

1. You may want to define a convention once in your Solution, and have it applied to multiple test assemblies in that Solution.
2. Someone may create a useful convention and share it in the form of a DLL.

By default, conventions only reach out into their containing assembly when looking for tests.  In order to take advantage of a convention defined in another assembly, you can add a subclass of `TestAssembly` and explicitly list which conventions apply here:

{% highlight csharp %}
using Fixie;
using SomeThirdPartyConventionsLibrary;

namespace IntegrationTests
{
    public class IntegrationTestAssembly : TestAssembly
    {
        public IntegrationTestAssembly ()
        {
            Apply<NUnitConvention>();
            Apply<xUnitConvention>();
        }
    }
}
{% endhighlight %}
