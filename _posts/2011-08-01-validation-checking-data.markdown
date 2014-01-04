---
layout: post
title:  Validation - checking data
---

It's a common requirement to have some checks on data. For example, we might not want to save a member of Employee unless the department has been entered. Thankfully, in Lithium, this is quite straightforward!

## Tweak the model

Open up `app/models/Employees.php` and replace the validation array:

{% highlight php %}
<?php
namespace app\models;

class Employees extends \lithium\data\Model {
	public $validates = array(
		'department' => array(
			array(
				'notEmpty',
				'required' => true,
				'message' => 'Please let us know what department this person works in.'
			)
		)
	);
// ...
{% endhighlight %}

## Tweak the controller

Next, edit `app/controllers/EmployeesController.php` and add the error checking:

{% highlight php %}
<?php
// ...
	public function add() {
		$employee = Employees::create();
		if (($this->request->data) && $employee->save($this->request->data)) {
			return $this->redirect(array('Employees::view', 'args' => array($employee->id)));
		}

		$errors = $employee->errors();
		return compact('employee', 'errors');
	}
// ...
{% endhighlight %}

Notice that the `errors()` method is automatically populated with any validation errors, should the `save` operation fail.

## Tweak the view

Now, we're going to modify `app/views/employees/add.html.php` and add some error output:

{% highlight php %}
<h2>Add new member of Employees</h2>

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

Sweet! This will break our tests, though, so what we're going to do is update our test so it states a department.
