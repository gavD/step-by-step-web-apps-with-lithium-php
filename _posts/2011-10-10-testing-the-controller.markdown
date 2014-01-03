---
layout: post
title:  Testing the controller
---

Remember a couple chapters back, we looked at unit testing our models? Here's how I approach testing:

![Testing Pyramid](images/testing-pyramid.png)

I think I copped this off Kent Beck or Bob Martin, but here's my take on it. **Unit tests** are for the developer. For us, developing our Lithium application, we're testing the low level logic in our models behaves _as we understand it_, so we're verifying our comprehension of the system (as well as looking out for regressions).

**Integration tests** check that the subsystems play nice. For our Lithium application, this is testing our controllers - a lot of people don't test controllers so much but for me it's a point I like to test at because it's simulating user input and the output that comes back to the user.

**System tests** I like to tie directly into **user stories** using **functional testing**, we'll look at that later. How exciting!

To summarise with an extremely leaky abstraction: if our system were a duck, unit tests would look at the flapping around going on below the water, Integration tests make sure it's moving. System tests make sure it's moving in the right direction,

So, we're going to test our controllers, to make sure our systems integrate well.

## Writing our test

There is already a test case created for us in app/tests/cases/controllers/EmployeesControllerTest.php - seems like a good place to start!

{% highlight php %}
<?php

namespace app\tests\cases\controllers;

use app\controllers\EmployeesController;

class EmployeesControllerTest extends \lithium\test\Unit {

	public function setUp() {}

	public function tearDown() {}

	public function testIndex() {}
	public function testView() {}
	public function testAdd() {}
	public function testEdit() {}
	public function testDelete() {}
}

?>
{% endhighlight %}

Well, that's a lot of tests that don't do anything! Let's add a test that we can hit the index:

{% highlight php %}
<?php

namespace app\tests\cases\controllers;

use app\controllers\EmployeesController;
use lithium\action\Request;

class EmployeesControllerTest extends \lithium\test\Unit {

	public function setUp() {}

	public function tearDown() {}

	public function testIndex() {
        $this->assertTrue(false);
    }
	public function testView() {}
	public function testAdd() {}
	public function testEdit() {}
	public function testDelete() {}
}
?>
{% endhighlight %}

Remember how we used the web based test runner? Well, that's cool and all but now we're going to use the command line one! Why use this instead of the nice shiny web one? Well, same principal as we chatted about before (you remember, you were wearing your blue sweater!) - it's good to delve into some of the underpinnings. Also, for **continuous integration** it's always best to be able to run without a browser or web server! More on that later!

Here's my console output:

	$ ./libraries/lithium/console/li3 test app/tests/cases/controllers/EmployeesControllerTest.php
	----
	Test
	----

	F

	FAIL

	0 / 1 passes
	1 fail and 0 exceptions

	Failed assertion assertTrue.
	 File    : /var/www/staff-rolodex/app/tests/cases/controllers/EmployeesControllerTest.php
	 Class   : app\tests\cases\controllers\EmployeesControllerTest
	 Method  : testIndex()
	 Line    : 21
	 ________
	expected: true
	result: false

	 ________

Hey, that's pretty cool - it's told us that our test has failed, which is exactly what we wanted! Now we're not scared of failing tests because we know it tells us what line the problem is on! It's very important to not be scared to try stuff! For me, tests are one of the main ways I build my confidence as I go.

Right, let's actually test this thing. What's really cool about controllers in Lithium is they simply return an array and you can call the methods directly, just like any other class, which makes them far easier to test than controllers in other frameworks I've come across - it's remarkably simple and elegant. Let's see what I mean.

{% highlight php %}
<?php

namespace app\tests\cases\controllers;

use app\controllers\EmployeesController;
use lithium\action\Request;

class EmployeesControllerTest extends \lithium\test\Unit {

	public function setUp() {}

	public function tearDown() {}

	public function testIndex() {
        $request = new Request();
        $request->data = array();
        $controller = new EmployeesController(array('request' => $request));

        $result = $controller->index();
        $this->assertTrue(array_key_exists('Employees', $result));
        $this->assertFalse(array_key_exists('bungle', $result));
    }
	public function testView() {}
	public function testAdd() {}
	public function testEdit() {}
	public function testDelete() {}
}
?>
{% endhighlight %}

So I run this test again:

	$ ./libraries/lithium/console/li3 test app/tests/cases/controllers/EmployeesControllerTest.php
	----
	Test
	----

	..

	OK

	2 / 2 passes
	0 fails and 0 exceptions

Two nice little dots, 2 / 2 passes - great!

So you know what I was saying about the controller returning an array?

{% highlight php %}
<?php

// ...
        $result = $controller->index();
        $this->assertTrue(array_key_exists('Employees', $result));
{% endhighlight %}

Here's the corresponding code from the controller:

{% highlight php %}
<?php

// ...
	public function index() {
		$employees = Employees::all();
		return compact('Employees');
	}
{% endhighlight %}

Notice that it simply returns the result of the all() static method on the Employees model, which pulls back all the Employees.

## So what did we just test? And what do we need to test next?

We tested that the controller returns a collection of Employees. It would be useful, at this point, to have a test database and some fixtures perhaps, rather than using the MySQL connection we set up earlier. Otherwise, our system isn't in a "known state", and you using the app could affect results of tests. Tests have to be **idempotent** (i.e. same results no matter how they are run and in what sequence).

> For more on testing controllers in Lithium, [see this article from Richard McIntyre](http://mackstar.com/blog/2012/02/02/testing-controllers-lithium).

> There is also a very good article on testing Lithium [here](http://nitschinger.at/Testing-the-Lithium-core-for-fun-and-profit)