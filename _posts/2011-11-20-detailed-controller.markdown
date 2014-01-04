---
layout: post
title:  Detailed controller
---

![ Uncaught exception 'lithium\data\model\QueryException' with message 'SELECT * FROM `Employees` AS `Employee`;: Table 'Employee.Employees' doesn't exist' i](images/crashy-db.png)

Right so the database is present, but the table doesn't exist. That makes sense, right? After all, we created our database but there's nothing in it, and we told the Employees model it had some fields... So, open up MySQL and run:

{% highlight mysql %}
CREATE TABLE employees (
    id int AUTO_INCREMENT PRIMARY KEY,
    name varchar(64) NOT NULL,
    notes varchar(255) NOT NULL,
    department varchar(64) NOT NULL
);
{% endhighlight %}

You should get a message like:

	Query OK, 0 rows affected (0.02 sec)

> By default, Lithium looks for a table with the same name as the controller, all in lowercase, so model Foos will look for a table foos

Right! Let's hit this controller again! Back to [http://employee-rolodex.localhost/employees](http://employee-rolodex.localhost/employees)!

![Template not found at path `/var/www/employee-rolodex/app/views/employees/index.html.php`'](images/crashy-no-template.png)

So, do you remember way back in the mists of time when we built our own controller? It were alllll fields back then... Ah, happy times. Anyway, we need to build some basic templates now.

Create `app/views/employees/index.html.php` (which is the file the error message reported as missing) and give it the following contents to allow it to show a nice list of employees using the `<ul>` tag and also to show an "Add" link:

{% highlight php %}
<h2>Employees</h2>
<ul>
<?php
foreach($employees as $employee) {
    ?><li><a href="<?=$this->url(array('Employees::view', 'args' => array($employee->id)));?>">
            <?=$employee->name?>
        </a></li><?php
}
?>
</ul>
<p><a class="btn btn-large" href="<?= $this->url(array('Employees::add')); ?>">Add</a></p>
{% endhighlight %}

You should now see:

![Employee list](images/employees-list.png)

YESSSSSSssssss..... It might not look like it, but we have created our first bit of properly dynamic code! Notice the "foreach" loop - it goes through each Employee member and lists hir name.

Next we need to be able to actually ADD employees...
