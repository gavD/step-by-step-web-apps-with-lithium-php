---
layout: post
title:  Authentication - updating our tests
---

We've got a new test for our administrator controller, but unfortunately a quick test run shows a problem:

	$ ./libraries/lithium/console/li3 test app/tests 
	----
	Test
	----

	........F

	FAIL

	8 / 9 passes
	1 fail and 0 exceptions

	Failed assertion assertEqual.
	 File    : /var/www/employee-rolodex/app/tests/cases/controllers/EmployeesControllerTest.php
	 Class   : app\tests\cases\controllers\EmployeesControllerTest
	 Method  : testAdd()
	 Line    : 61
	 ________
	expected: 3
	result: 2

	 ________

app/tests/cases/controllers/EmployeesControllerTest.php will need to be updated so that it tests firstly that non-authenticated users cannot add Employee, and secondly that authenticated users can add Employee. 

Here is my updated test:

{% highlight php %}
<?php
namespace app\tests\cases\controllers;

use app\controllers\EmployeesController;
use lithium\action\Request;
use app\models\Employees;
use li3_fixtures\test\Fixtures;
use lithium\net\http\Router;
use app\controllers\AdministratorsController;

class EmployeesControllerTest extends \lithium\test\Unit {
	public function setUp() {
		Fixtures::config(array(
			'db' => array(
				'adapter' => 'Connection',
				'connection' => 'default',
				'fixtures' => array(
					'administrators' => 'app\tests\fixture\AdministratorsFixture',
					'employees' => 'app\tests\fixture\EmployeesFixture',
				)
			)
		));
		Fixtures::save('db');

		Router::connect('/{:controller}/{:action}/{:args}');
	}

	public function tearDown() {
		Fixtures::clear('db');
		\lithium\storage\Session::clear();
	}
	
	// helper methods

	protected function logIn() {
		$adminUser = \app\models\Administrators::create();
		$adminUser->username = 'foobar';
		$adminUser->password = 'barbar';
		$adminUser->save();
				
		$loginRequest = new Request();
		$loginRequest->data = array(
			'username' => 'foobar',
			'password' => 'barbar'
		);
		
		$adminController = new AdministratorsController(array('request' => $loginRequest));
		$response = $adminController->login();
		$this->assertEqual(302, $response->status['code']);
		
		return $response;
	}
	
	// actual tests
	
	public function testIndexListsEmployeesInTheDatabase() {
		$request = new Request();
		$request->data = array();
		$controller = new EmployeesController(array('request' => $request));

		$result = $controller->index();
		$this->assertEqual("Foobar", $result['employees'][1]->name);
		$this->assertEqual("Bazbip", $result['employees'][2]->name);
		$this->assertNull($result['employees'][3]);
	}

	public function testViewShowsAnIndividualEmployeeMember() {
		$request = new Request();
		$request->data = array();
		$controller = new EmployeesController(array('request' => $request));

		$result = $controller->view(1);
		$this->assertEqual("Foobar", $result['employee']->name);
	}

	public function testAddCannotBeUsedByNonAuthenticatedUsers() {
		$this->assertEqual(2, count(Employees::all()));
		$request = new Request();
		$request->data = array(
			'name' => 'Brand new user',
			'department' => 'Scamping and nonsense'
		);
		$controller = new EmployeesController(array('request' => $request));
		$response = $controller->add();
		$this->assertEqual(302, $response->status['code']);
		$this->assertEqual(2, count(Employees::all()));
	}
	
	public function testAddCanBeUsedByAdministrators() {
		$this->logIn();
		
		// create a new user
		$this->assertEqual(2, count(Employees::all()));
		$request = new Request();
		$request->data = array(
			'name' => 'Brand new user',
			'department' => 'Scamping and nonsense'
		);
		$controller = new EmployeesController(array('request' => $request));
		/* @var $response \lithium\action\Response */
		$response2 = $controller->add();
		$this->assertEqual(302, $response2->status['code']);

		$this->assertEqual(3, count(Employees::all()));
		$this->assertContains('employees/view/3', $response2->headers['Location']);
	}

	public function testDepartmentIsMandatory() {
		$this->logIn();
		
		$this->assertEqual(2, count(Employees::all()));
		$request = new Request();
		$request->data = array('name' => 'Departmentless user');
		$controller = new EmployeesController(array('request' => $request));
		$response = $controller->add();
		$this->assertEqual(
			'Please let us know what department this person works in.',
			$response['errors']['department'][0]
		);
		$this->assertEqual(2, count(Employees::all()));
	}

	//	public function testEdit() {} // exercise for the reader!
	//	public function testDelete() {} // exercise for the reader!
}
?>
{% endhighlight %}

Gosh, there's a lot of code in there! Once again, we're ending the session on every test - otherwise testDepartmentIsMandatory will work because the preceeding test leaves the user logged in, which is a very brittle way to test a system. Notice also that we pull in the Administrators fixture so that table is available to us.

Right, this should all work now! You should be able to do a complete test run which tests your entire system!

> It's a good idea to check that your tests work on both the command line runner and the web runner which lives at /test

I'm sure at this point you're positively gasping for something DIFFERENT to do! No problem - on to the next thing!