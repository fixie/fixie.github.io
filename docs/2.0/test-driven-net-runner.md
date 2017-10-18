---
title: Fixie - TestDriven.NET Runner
---
# TestDriven.NET Runner

## How do I run my tests with TestDriven.NET?

You may have previous experience using TestDriven.NET with another test framework such as NUnit.  The experience with Fixie is exactly the same.

> [TestDriven.NET](http://www.testdriven.net/) is a third-party plugin for Visual Studio which allows you to run tests within the IDE for tests written for a number of different test frameworks.  It is free for students, open source developers, and trial users.  Companies, organizations or other individual developers can purchase the plugin for a reasonable fee.
>
> TestDriven.NET can be set up to run tests from your whole solution, an individual test project, a single namespace, a single class, or a single test method, all depending on where you are focused in the IDE when you invoke it.

Install the third-party TestDriven.NET plugin into Visual Studio, [install Fixie into your solution via NuGet](../quick-start), and then run tests using the same context menus and keyboard shortcuts you would use to run your NUnit tests.  Results and console output are directed to the Visual Studio *Output* window, and you can double-click on test failures in that window to navigate to the offending tests.