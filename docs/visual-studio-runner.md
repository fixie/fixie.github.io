---
layout: documentation
title: Fixie - Visual Studio Runner
---
# Visual Studio Runner

## How do I run my tests with Visual Studio's built-in Test Explorer?

The *Test Explorer* window is provided by most editions of Visual Studio.  The Test Explorer window allows you to run tests within the IDE, including red/green visual feedback for failed/passed tests.

You may have previous experience using the Test Explorer with another test framework such as NUnit.  The experience with Fixie is *similar*, though the distribution model for Test Explorer plugins has become simpler now that test frameworks can deploy support for the Test Explorer via NuGet. Simply [install Fixie into your solution via Nuget](../quick-start), and then run tests using the same context menus and keyboard shortcuts you would use to run your NUnit tests.

> Visual Studio detects new test runners at *NuGet package installation time*, rather than simply detecting the presence of the package's DLLs.  Once that detection happens, though, Visual Studio caches a copy of the package elsewhere and uses that copy.  So, if you choose not to use NuGet on your project, you can still enable Fixie's Visual Studio integration by installing Fixie with NuGet into a throw-away Solution, and then closing all instances of Visual Studio.  It should be available in your own Solution after that. 

Specifically, make sure that the Test Explorer window is open (`Test > Windows > Test Exporer`). When you build your project, the runner will discover the tests in your project and display them in Test Exporer as "Not Run Tests".  Then, click `Run All` in the Test Explorer to execute the tests.  Test Explorer will provide visual feedback of which tests have passed and failed.  Exception stack traces for failed tests are clickable, taking you to the relevant test code.

Due to limiting assumptions made by Visual Studio, running [parameterized tests](../parameterized-test-methods) has one surprising detail: discovery will display one Visual Studio 'test case' for each test [method group](http://stackoverflow.com/questions/886822/what-is-a-method-group-in-c), then at execution time potentially-many individual passes and failures will be displayed **underneath** that. All in all, parameterized and overloaded test methods are fully supported, but Visual Studio's *count* of discovered tests is really a count of discovered test method names.

> Other test frameworks attempt to handle parameterized tests differently, resulting in a glitchy experience whenever a parameterized test method has a dynamically generated set of inputs at runtime. Fixie's grouping of dynamic test cases under method group names is a far safer compromise that better-fits the intended usage of the Visual Studio Test Explorer API. The counts are unfortunate, but it's better to be sure of what you are actually running.
