---
layout: documentation
title: Fixie - Quick Start
---
## How do I install Fixie?

First, [install NuGet](http://docs.nuget.org/docs/start-here/installing-nuget). Then, install the [Fixie NuGet package](https://www.nuget.org/packages/Fixie) from the package manager console:

    PM> Install-Package Fixie

## How do I use Fixie?

1. Create a Class Library project to house your test classes.
2. Add a reference to Fixie.dll.
3. Add test classes and test cases to your testing project, following the [Default Convention](default-convention).
    * To use a different style, you can create a [Custom Convention](custom-conventions).
4. Use the console runner (should be at `your-solution-dir/packages/Fixie.x.x.x/lib/netXX/`) from a command line to execute your tests:

    `Fixie.Console.exe path/to/your/test/project.dll`
    
5. Use the TestDriven.NET runner from within Visual Studio, using the same keyboard shortcuts you would use for NUnit tests.