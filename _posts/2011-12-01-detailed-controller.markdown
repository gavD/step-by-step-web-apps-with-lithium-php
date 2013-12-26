---
layout: post
title:  Detailed controller
---

Remember a couple of things we've covered - MVC (Model View Controller) and the "scaffolding" that created our model? As we mentioned back in the chapter "A Model Citizen", that scaffolding also generated a controller - and that's what we're going to focus on here: now that our model is sitting pretty, now we want to hook into it and do actual stuff with it! First things first, let's actually set up a database. Now, a full treatment of how to set up your database is outside the scope of this book - but here's what I've done:

{% highlight bash %}
    mysql -uroot -p -e'CREATE DATABASE staff';
{% endhighlight %}

That will create a database "staff" (assuming you have mysql installed, you don't already have such a db, and dozens of other things!). Great, we have a DB now!

Let's go to our controller at [http://staff-rolodex.localhost/staff](http://staff-rolodex.localhost/staff)!

![Uncaught exception 'lithium\core\ConfigException' with message 'The data connection `default` is not configured.'](images/crashy-controller.png)

_Ruh roh!_

So this business:

> "Uncaught exception 'lithium\core\ConfigException' with message 'The data connection `default` is not configured.'"

Let's get that sorted now! The app/config folder contains a file "bootstrap.php", which includes a bunch of gubbins from the "bootstrap" folder. This is where the config lives, broken up nicely into PHP files. Nice. NICE!

Open up app/config/bootstrap/connections.php. Uncomment the MySQL section, so the file will look a bit like:

{% highlight php %}
<?php
use lithium\data\Connections;
 Connections::add('default', array(
 	'type' => 'database',
 	'adapter' => 'MySql',
 	'host' => 'localhost',
 	'login' => 'root', // <== Never use root on production! Feel free to create a user!
 	'password' => '', // <== Set this to your user's password.
 	'database' => 'staff', // <=== notice this line!
 	'encoding' => 'UTF-8'
 ));
?>
{% endhighlight %}

Total nonsense aside:

> I've chopped out all the comments and stuff from the default connections.php file, you don't have to do that! I'm trying to save trees - or pixels or bytes or whatever is inside computers - I hear there's lots of stuff inside computers these days. There used to be 8 "bits", now there are 64 or more "bits"! OK I stole that joke from Mr Biffo. Anyone remember Digitiser? No? Moc-moc-a-moc!

Right, back to [http://staff-rolodex.localhost/staff](http://staff-rolodex.localhost/staff)! Will it work? Will it not work? Will it catapult your HDD into space? Only several ways to find out!

![ Uncaught exception 'lithium\data\model\QueryException' with message 'SELECT * FROM `staffs` AS `Staff`;: Table 'staff.staffs' doesn't exist' i](images/crashy-db.png)

Right so the table doesn't exist. That makes sense, right? After all, we created our database but there's nothing in it, and we told the Staff model it had some fields... So, open up MySQL and run:

{% highlight mysql %}
CREATE TABLE staffs (
    id int AUTO_INCREMENT PRIMARY KEY,
    name varchar(64) NOT NULL,
    notes varchar(255) NOT NULL,
    department varchar(64) NOT NULL
);
{% endhighlight %}

You should get a message like:

	Query OK, 0 rows affected (0.02 sec)

Right! Let's hit this controller again! Back to [http://staff-rolodex.localhost/staff](http://staff-rolodex.localhost/staff)!

![Template not found at path `/var/www/staff-rolodex/app/views/staff/index.html.php`'](images/crashy-no-template.png)

So, do you remember way back in the mists of time when we built our own controller? It were alllll fields back then... Ah, happy times. Anyway, we need to build some basic templates now.

Create app/views/staff/index.html.php and give it the following contents:

{% highlight php %}
<h2>Staff</h2>
<?php
foreach($staffs as $staff) {
    echo $h($staff->name);
    echo "<br/>";
}
?>
<p><a class="btn btn-large" href="<?= $this->url(array('Staff::add')); ?>">Add</a></p>
{% endhighlight %}

_Please overlook the clumsy pluralisation of Staff to Staffs! Allow it, fam!_

You should now see:

![Staff list](images/staff-list.png)

YESSSSSSssssss..... It might not look like it, but we have created our first bit of properly dynamic code! Notice the "foreach" loop - it goes through each staff member and lists hir name. There are no staff right now, so let's add a function to add them!

Create app/views/staff/add.html.php and give it the contents:

{% highlight php %}
<h2>Add new member of staff</h2>

<?= $this->form->create($staff); ?>
<label>Name</label>
<?= $this->form->text('name'); ?>
<label>Notes</label>
<?= $this->form->text('notes'); ?>
<label>Department</label>
<?= $this->form->text('department'); ?>
<br/>
<?= $this->form->submit('Save staff'); ?>
<?= $this->form->end(); ?>

<p><a class="btn btn-large" href="<?= $this->url(array('Staff::index')); ?>">Back to staff index</a></p>
{% endhighlight %}

This should render something like the following:

![Add form](images/add-form.png)

> _Don't worry, we're going to go back and explain all that we've done in this section, I just want to 'close the loop' and make sure we can add a member of staff first!_

Just one more step, then we've got a working system for adding users!

Create app/views/staff/view.html.php and give it the following contents:

{% highlight php %}
<h2>Staff '<?= $staff->name ?>'</h2>
<p> Notes: <?= $staff->notes ?></p>
<p> Department: <?= $staff->department ?></p>

<p><a class="btn btn-large" href="<?= $this->url(array('Staff::index')); ?>">Back to staff index</a></p>
{% endhighlight %}

Only one more thing! Please edit app/controllers/StaffController.php and change the view method to:

{% highlight php %}
<?php

// ...
	public function view($id) {
        $staff = Staff::first($id);
		return compact('staff');
	}
// ...
?>
{% endhighlight %}

For some reason, the generated version uses $this->request->id, which for me, didn't work. Your Mileage May Vary! Do the same for the edit method. You can now use the web application to add and list staff! When you add a user you should get a screen like this:

![Staff](images/staff.png)

Now let's update app/views/staff/index.html.php:

{% highlight php %}
<h2>Staff</h2>
<ul>
<?php
foreach($staffs as $staff) {
    ?><li><a href="<?=$this->url(array('Staff::view', 'args' => array($staff->id)));?>">
            <?=$staff->name?>
        </a></li><?php
}
?>
</ul>
<p><a class="btn btn-large" href="<?= $this->url(array('Staff::add')); ?>">Add</a></p>
{% endhighlight %}

You should now have a nice looking index of users that you can click through to view their details - cool!

Well, that was a lot of DOING. Now it's time for some THINKING!