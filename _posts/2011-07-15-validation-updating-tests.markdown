---
layout: post
title:  Validation - updating tests
---

Open up `app/tests/cases/EmployeesControllerTest.php` and add a 'department' to `$_records`, e.g.:

{% highlight php %}
<?php
// ...
	public function testAdd() {
		$this->assertEqual(2, count(Employees::all()));
		$request = new Request();
		$request->data = array(
			'name' => 'Brand new user',
			'department' => 'Scamping and nonsense'
		);
		// ...
{% endhighlight %}

Let's check that the model won't save without a department. We open up `app/tests/cases/models/EmployeesTest.php` and we're adding a `setUp` method, as well as `testDepartmentIsMandatory`. We're also no longer using our `MockEmployee` because we can connect to our test database for extra realism:

{% highlight php %}
<?php
namespace app\tests\cases\models;

use app\models\Employees;
use li3_fixtures\test\Fixtures;

class EmployeesTest extends \lithium\test\Unit {

	public function setUp() {
        Fixtures::config(array(
            'db' => array(
                'adapter' => 'Connection',
                'connection' => 'default',
                'fixtures' => array(
                    'Employee' => 'app\tests\fixture\EmployeeFixture',
                )
            )
        ));
        Fixtures::save('db');
	}

	public function testEmployeeHaveNames() {
		$this->assertEqual('string', Employees::hasField('name')['type']);
	}

	public function testDepartmentIsMandatory() {
		$employee = Employees::create(array(
			'name' => 'No department'
		));
		$employee->save();

		$errors = $employee->errors();
		$this->assertEqual(
			'Please let us know what department this person works in.',
			$errors['department'][0]
		);
	}
}
?>
{% endhighlight %}


Now we're going to add a new test to `app/tests/cases/controllers/EmployeesControllerTest.php`:

{% highlight php %}
<?php
// ...

use app\models\Employees;

// ...
	public function testDepartmentIsMandatory() {
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
{% endhighlight %}

This test ensures that the user cannot save a member of Employees to the database without specifying a department.

> For more on validation, read the [Lithium documentation](http://li3.me/docs/manual/working-with-data/validation.wiki)