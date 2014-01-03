---
layout: post
title:  Coding standards
---

Now that we've got a couple of simple things up and running, we're going to introduce the concept of **coding standards**. Having a set of coding standards help us to write code in a consistent style and avoid common pitfalls.

There's a library called [li3 quality](https://github.com/UnionOfRAD/li3_quality) which adds syntax checking to your /test area and also makes it available from the command line. Here's a way to run a quick syntax check of the app:

{% highlight bash %}
./libraries/lithium/console/li3 quality syntax app
{% endhighlight %}

I'd suggest that any code you write should adhere to these standards. If you are writing plugins, libraries or soforth for Lithium, or even committing back to core, it gives you a head start in getting accepted because you're doing things the same way as everyone else.

Take a few minutes to make sure your code meets the coding standards. Don't worry - I'll wait!

I ended up with:

	$ ./libraries/lithium/console/li3 quality syntax app
	--------------------
	Lithium Syntax Check
	--------------------
	Performing 26 rules on 7 classes.
	[OK] app\tests\cases\models\MockEmployees
	[OK] app\tests\cases\models\EmployeesTest
	[OK] app\tests\cases\controllers\EmployeesControllerTest
	[OK] app\models\Employees
	[FAIL] app\controllers\HelloWorldController
	Line	Position	Violation
	----	--------	---------
	11  	-       	Function "to_string" is not in camelBack style
	11  	-       	Function "to_json" is not in camelBack style
	[OK] app\controllers\HomeController
	[OK] app\controllers\EmployeesController

(I moved MockEmployees into its own file - which is good practise! One class per file.)

> See  [https://github.com/UnionOfRAD/li3_quality](https://github.com/UnionOfRAD/li3_quality) for full documentation of the li3 quality library - it's definitely worth getting to grips with!

You may notice that there are still a couple of problems in the HelloWorldController - I think we can just delete that one now, we're not using it :-) In fact, what else can we declutter? Onwards!