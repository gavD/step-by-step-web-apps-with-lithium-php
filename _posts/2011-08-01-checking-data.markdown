---
layout: post
title:  Validation - checking data
---

It's a common requirement to have some checks on data. For example, we might not want to save a member of staff unless the department has been entered. Thankfully, in Lithium, this is quite straightforward!

Open up app/models/Staff.php and replace the validation array:

{% highlight php %}
<?php
namespace app\models;

class Staff extends \lithium\data\Model {
	public $validates = array(
		'department' => array(
			array(
				'notEmpty',
				'required' => true,
				'message' => 'Please let us know what department this person works in.'
			)
		)
	);
{% endhighlight %}

Now, we're going to modify app/views/staff/add.html.php and add some error output:

{% highlight php %}
<h2>Add new member of staff</h2>

<?php
if (count($errors) > 0) {
	foreach ($errors as $error) {
	?>
		<p class="text-error"><?= $error[0]; ?></p>
	<?php
	}
}
?>

...
{% endhighlight %}

Then, if you try to create a user with no department, you'll see something like this:

![Validation](images/validation.png)

Sweet! This will break our tests, though, so what we're going to do is update our test so it states a department. Open up app/tests/cases/StaffControllerTest.php and add a 'department' to $_records, e.g.:

{% highlight php %}
<?php
// ...
	public function testAdd() {
		$this->assertEqual(2, count(Staff::all()));
		$request = new Request();
		$request->data = array(
			'name' => 'Brand new user',
			'department' => 'Scamping and nonsense'
		);
		// ...
{% endhighlight %}

Let's check that the model won't save without a department. We open up app/tests/cases/models/StaffTest.php and we're adding a setUp method, as well as testDepartmentIsMandatory. We're also no longer using our MockStaff because we can connect to our test database for extra realism:

{% highlight php %}
<?php
namespace app\tests\cases\models;

use app\models\Staff;
use li3_fixtures\test\Fixtures;

class StaffTest extends \lithium\test\Unit {

	public function setUp() {
        Fixtures::config(array(
            'db' => array(
                'adapter' => 'Connection',
                'connection' => 'default',
                'fixtures' => array(
                    'staff' => 'app\tests\fixture\StaffFixture',
                )
            )
        ));
        Fixtures::save('db');
	}

	public function testStaffHaveNames() {
		$this->assertEqual('string', Staff::hasField('name')['type']);
	}

	public function testDepartmentIsMandatory() {
		$staff = Staff::create(array(
			'name' => 'No department'
		));
		$staff->save();

		$errors = $staff->errors();
		$this->assertEqual('Please let us know what department this person works in.', $errors['department'][0]);
	}
}
?>
{% endhighlight %}


Now we're going to add a new test to StaffControllerTest:

{% highlight php %}
<?php
// ...
	public function testDepartmentIsMandatory() {
		$this->assertEqual(2, count(Staff::all()));
		$request = new Request();
		$request->data = array('name' => 'Departmentless user');
		$controller = new StaffController(array('request' => $request));
		$response = $controller->add();
		$this->assertEqual('Please let us know what department this person works in.', $response['errors']['department'][0]);
		$this->assertEqual(2, count(Staff::all()));
	}
{% endhighlight %}

This test ensures that the user cannot save a member of staff to the database without specifying a department.

> For more on validation, read the [Lithium documentation](http://lithify.me/docs/manual/working-with-data/validation.wiki)