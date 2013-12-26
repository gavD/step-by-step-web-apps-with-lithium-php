---
layout: post
title:  Testing our model
---

Right, let's give this a test! A lot of people leave testing to later on. Not me, no! Over to app/tests/cases/models/StaffTest.php:

{% highlight php %}
<?php
namespace app\tests\cases\models;

use app\models\Staff;

class StaffTest extends \lithium\test\Unit {

	public function setUp() {}

	public function tearDown() {}


}
?>
{% endhighlight %}

There's not much going on in here either. Lithium has its own unit testing gubbins - it doesn't use PHPUnit or whatever. I suppose you COULD use PHPUnit but we're going to stick with the [default Lithium testing utilities](http://lithify.me/docs/manual/quality-code/testing.wiki).

I'm not going to do a full treatment of unit testing or TDD (Test Driven Development) here. The absolute best book on the subject is [Kent Beck's Test-Driven Development by example](http://books.google.com/books/about/Test_Driven_Development.html?id=gFgnde_vwMAC&redir_esc=y) - it really is fantastic!

What we're going to test here is that our model has the fields we'd expect it to. The advantage of defining the schema up front is that we can do this test without needing a database yet! This is cool because if we change our schema and get it wrong, we will know right away that our change has broken something!

Here's my code for app/tests/cases/models/StaffTest.php:

{% highlight php %}
<?php
namespace app\tests\cases\models;

use app\models\Staff;

class MockStaff extends Staff {
    protected $_meta = array('connection' => false);
}

class StaffTest extends \lithium\test\Unit {
    public function testStaffHaveNames() {
        $this->assertEqual('string', MockStaff::hasField('name')['type']);
    }
}
?>
{% endhighlight %}

I've removed setUp and tearDown because we don't need them yet, but we have a whole other class now (MockStaff) - sorry about that! I'll explain, I promise, but first head over to [http://staff-rolodex.localhost/test/](http://staff-rolodex.localhost/test/) and you should see something like this:

![Homepage of the test runner](images/test-home.png)

Here is Lithium's web based test runner. OK now you see StaffTest under app? Click it! Click it now! Drink your weak lemon drink and click that fussy little link!

What you should get is:

![Result of the test runner](images/test-staff-1.png)

1/1 passes - great! Now, I know I promised I'd explain why there's so much code.

You see, Lithium requires a connection to a database (sorta, ish). So, if we tried to run this test without the MockStaff in the middle, it tries to connect to the database and, unless you've been a clever sausage and done some reading around, you probably haven't done yet! So, when you come to run the tests, you get all kind of errors. Try clicking a few of the links in the in the test runner under "lithium" - these run the core Lithium tests. Some might not work if you don't have a connection set up.

Now, generally, there are a couple of things you can do with connections and testing:

1. Disable them
2. Mock them out
3. Connect to something

We've gone for option 1 at the moment, but later on we will be mocking connections, and maybe we'll even go so far as connecting to an in-memory database or something. Exciting! For now, though, let's look at these codes:

{% highlight php %}
<?php

// ...
class MockStaff extends Staff {
    protected $_meta = array('connection' => false);
}
// ...

{% endhighlight %}

So we've used inheritance to override connection and set it to false, then we've done all our testing based on that. This prevents Lithium's Model class from scurrying around trying to find a database connection. This is a bit icky to do it this way but it does work. Generally, I prefer to test real things as they are where possible - so like I say we'll look at better testing strategies later.

OK so the actual test is quite simple really. Staff::hasField simply returns the field if it has it (I know, I expected it to be a boolean as well! It returns false if the field doesn't exist on the model).

Well, that's the very bare basics of writing a test for a model. Like all stories, I've skipped over some details so we can do some "big reveals" later. You might already have guessed some of the plot twists! Nevertheless, let's hope we can stay engaged with the characters annnnnnd the metaphor just totally fell apart, didn't it?