---
layout: documentation
title: Fixie
---
Fixie is a .NET convention-based test framework similar to NUnit and xUnit, but with an emphasis on low-ceremony defaults and flexible customization.

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

## Documentation

1. [Default Convention](default-convention)
2. [Custom Conventions](custom-conventions)
3. [Parameterized Test Methods](parameterized-test-methods)
4. [Skipping Tests](skipping-tests)
5. [Assertions](assertions)
6. [Continuous Integration](continuous-integration)
