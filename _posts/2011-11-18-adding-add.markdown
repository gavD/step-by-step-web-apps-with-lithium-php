---
layout: post
title:  Adding add functionality
---

There are no employees in the database right now, so we can't see them in our list, so how do we KNOW we've actually built anything that works? Right then, let's add functionality to add them!

Create `app/views/employees/add.html.php` and give it the contents:

{% highlight php %}
<h2>Add new employee</h2>

<?= $this->form->create($employee); ?>
<label>Name</label>
<?= $this->form->text('name'); ?>
<label>Notes</label>
<?= $this->form->text('notes'); ?>
<label>Department</label>
<?= $this->form->text('department'); ?>
<br/>
<?= $this->form->submit('Save Employee'); ?>
<?= $this->form->end(); ?>

<p><a class="btn btn-large"
		href="<?= $this->url(array('Employees::index')); ?>">
	Back to employee index
</a></p>
{% endhighlight %}

This should render something like the following:

![Add form](images/add-form.png)

> _Don't worry, we're going to go back and explain all that we've done in this section, I just want to 'close the loop' and make sure we can add a member of Employee first!_

Just one more step, then we've got a working system for adding users!

Create `app/views/employees/view.html.php` and give it the following contents:

{% highlight php %}
<h2><?= $employee->name ?></h2>
<p> Notes: <?= $employee->notes ?></p>
<p> Department: <?= $employee->department ?></p>

<p><a class="btn btn-large"
		href="<?= $this->url(array('Employees::index')); ?>">
	Back to employee index
</a></p>
{% endhighlight %}

You can now use the web application to add and list Employee! When you add a user you should get a screen like this:

![Employee](images/employee-view.png)


If you go back to the employee index, you should now have a nice looking index of users that you can click through to view their details - cool!

![Employee](images/employee-list.png)

Well, that was a lot of DOING! We've covered:

* More templating
* Models
* Saving models
* Database setup

Now it's time for some THINKING!