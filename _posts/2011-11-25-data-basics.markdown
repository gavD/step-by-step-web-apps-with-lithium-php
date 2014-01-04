---
layout: post
title:  Data basics
---

Remember a couple of things we've covered - MVC (Model View Controller) and the "scaffolding" that created our model? As we mentioned back in the chapter "A Model Citizen", that scaffolding also generated a controller - and that's what we're going to focus on here: now that our model is sitting pretty, now we want to hook into it and do actual stuff with it! First things first, let's actually set up a database. Now, a full treatment of how to set up your database is outside the scope of this book - but here's what I've done:

{% highlight bash %}
    mysql -uroot -p -e'CREATE DATABASE staff';
{% endhighlight %}

That will create a database "staff" (assuming you have mysql installed, you don't already have such a db, and dozens of other things!). Great, we have a DB now I've called it "staff" to avoid confusion with the employees table.

Let's go to our controller at [http://employee-rolodex.localhost/employees](http://employee-rolodex.localhost/employees)!

![Uncaught exception 'lithium\core\ConfigException' with message 'The data connection `default` is not configured.'](images/crashy-controller.png)

_Ruh roh!_

So this business:

    "Uncaught exception 'lithium\core\ConfigException' with message 'The data connection `default` is not configured.'"

Let's get that sorted now! The `app/config` folder contains a file `bootstrap.php`, which includes a bunch of gubbins from the `app/config/bootstrap` folder. This is where the config lives, broken up nicely into PHP files. Nice. NICE!

Open up `app/config/bootstrap/connections.php`. Uncomment the MySQL section, so the file will look a bit like:

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

I've chopped out all the comments and stuff from the default connections.php file, you don't have to do that! I'm trying to save trees - or pixels or bytes or whatever is inside computers - I hear there's lots of stuff inside computers these days. There used to be 8 "bits", now there are 64 or more "bits"! OK I stole that joke from Mr Biffo. Anyone remember Digitiser? Moc-moc-a-moc!

Right, back to [http://employee-rolodex.localhost/employees](http://employee-rolodex.localhost/employees)! Will it work? Will it not work? Will it catapult your HDD into space? Only several ways to find out!

On to the next chapter, where we'll actually set up some tables and start working with REAL data!
