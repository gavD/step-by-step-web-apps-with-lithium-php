---
layout: post
title:  Authentication - testing our new controller
---

Let's test our admin controller. First, we're going to set up an administrator available to tests by creating the fixture `app/tests/fixture/AdministratorsFixture.php`:

{% highlight php %}
<?php
namespace app\tests\fixture;

class AdministratorsFixture extends \li3_fixtures\test\Fixture {

	protected $_model = 'app\models\Administrators';

	protected $_fields = array(
		'id' => array('type' => 'id'),
		'username' => array('type' => 'string'),
		'password' => array('type' => 'string'),
	);
}
?>
{% endhighlight %}

Then create the test `app/tests/cases/controllers/AdministratorsControllerTest.php`:

{% highlight php %}
<?php
namespace app\tests\cases\controllers;

use lithium\action\Request;
use li3_fixtures\test\Fixtures;
use lithium\net\http\Router;
use app\controllers\AdministratorsController;

class AdministratorsControllerTest extends \lithium\test\Integration {
	/**
	 * @var AdministratorsController System Under Test
	 */
	private $sut;
	
	public function setUp() {
		Fixtures::config(array(
			'db' => array(
				'adapter' => 'Connection',
				'connection' => 'default',
				'fixtures' => array(
					'administrators' => 'app\tests\fixture\AdministratorsFixture',
				)
			)
		));
		Fixtures::save('db');
		Router::connect('/{:controller}/{:action}/{:args}');
		
		$request = new Request();
		$request->data = array(
			'username' => 'foobar',
			'password' => 'barbar'
		);
		
		$this->sut = new AdministratorsController(array('request' => $request));
	}

	public function tearDown() {
		Fixtures::clear('db');
		\lithium\storage\Session::clear();
	}

	public function testCannotLogInAsNonExistingUser() {
		$response = $this->sut->login();
		$this->assertTrue($response['loginFailed']);
	}
	
	public function testCanLogInAsAdministrators() {
		$adminUser = \app\models\Administrators::create();
		$adminUser->username = 'foobar';
		$adminUser->password = 'barbar';
		$adminUser->save();
		
		// log in
		$response = $this->sut->login();
		$this->assertEqual(302, $response->status['code']);
		
		// log out
		$logoutResponse = $this->sut->logout();
		$this->assertEqual(302, $logoutResponse->status['code']);
	}
}
?>
{% endhighlight %}

There are a few things to note here. I create `$sut` for "System Under Test". This is just a little convention I use, you don't have to. The SUT is the controller, and I feed it a standard request. It works when there is a user in the database, just as we expect.

Notice also I clear the session on every `tearDown` - Li3 persists the session between tests otherwise so always work to make your tests **idempotent** - that is, that they can be run in any order, that they don't affect one another.

Let's run just this new test, let's not run the whole suite just yet:

	$ ./libraries/lithium/console/li3 test \
		app/tests/cases/controllers/AdministratorsControllerTest.php
	----
	Test
	----

	...

	OK

	3 / 3 passes
	0 fails and 0 exceptions

	
## Why can't I define my users in my fixture?

Good question! It's because the fixture does some hashing of the password (remember that filter we built in to `app/models/Administrators.php`?), so it's safer to create it in the test here. It's possible to do it in the fixture, but we're keeping things simple for now.

OK, now we need to bring the rest of our test suite up to date with the changes we've made...