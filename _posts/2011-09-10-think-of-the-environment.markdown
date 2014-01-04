---
layout: post
title:  Think of the environment
---

We made some progress with testing, but really, we need to be able to write to and read from a database. We don't want to use the database that we're using for our development, though, otherwise every time we run tests, the data could be in any old state! Imagine your test does something like "test we can delete all users" - when you ran the test, it would shred your data! Thankfully, Lithium supports the concept of multiple environments, so we're going to set up a separate **environment** for testing.

An environment is a setup of how your system runs. Similar to Rails or Symfony2, there are development (usually your local machine), test (automated tests), and production (live) environments. For example, you might want caching switched off on your development environment, but in production you want to cache aggressively. We're going to use it to say "in unit tests, don't write to our MySQL database, write to an in-memory SQLite database instead". If you have't got [SQLite](http://www.sqlite.org/) installed, do it now! SQLite is a great tool for testing because it works pretty much like any other relational database, but you can store it in memory so it's nice and clean and it's all gone after the test run - the perfect crime!

In `app/config/bootstrap/connections.php`, change the contents to the following:

{% highlight php %}
<?php
use lithium\data\Connections;

Connections::add('default', array(
    'development' => array(
		'type' => 'database',
		'adapter' => 'MySql',
		'host' => 'localhost',
		'login' => 'root',
		'password' => '',
		'database' => 'staff'
	), 
    'test' => array(
		'type' => 'database',
		'adapter' => 'Sqlite3',
		'database' => ':memory:'
	),
    'production' => array(
		'type' => 'database',
		'adapter' => 'MySql',
		'host' => 'localhost',
		'login' => 'root',
		'password' => '********',
		'database' => 'staff-production'
    )
));
?>
{% endhighlight %}

> **Note on configuration** - generally I don't version configuration for a generic application. I'm certainly reluctant to commit things like passwords in source code! You might want to add `connections.php` to your `.gitignore` (or equivalent for other VCSs) for a real-world system.

So now you have THREE database connections. Lithium will automatically work out which database to use using the [_detector function](https://github.com/UnionOfRAD/lithium/blob/master/core/Environment.php#L329) - if you're running unit tests, it should automatically grab the "test" environment and thus the SQLite database. If you're running locally (i.e. on IP `127.0.0.1`), it will try to connect to `staff`. If you're on any other IP and being browsed through the web, it'll go for `staff-production`.

Notice they're all under "default". I guess we could call this "relational" instead of "default" - Lithium also supporting things like MongoDB. We could have separate connections for "mongo" and "relational". The notion of a **connection** in Lithium is pretty abstract, [see the docs for more details](http://li3.me/docs/manual/working-with-data/using-data-sources.wiki). Models will use the connection named "default" by default ([see the `Model::$_meta` documentation](http://li3.me/docs/lithium/data/Model::$_meta)), so I'm leaving it called "default".

> **The means of production...** it's really important to test in production mode with full caching, "real world" etc. For example, if you're load testing (which we'll talk about later), you really want to load test against production so that it's a realistic simulation of your live system.

## Debugging tests with SQLite3

If you wanted to use a physical, on-disk sqlite database rather than an in-memory one, simply provide a path rather than using `:memory:`. This is really useful for debugging tests.

{% highlight php %}
<?php
// ...
    'database' => 'app/resources/tmp/test.db'
// ...
{% endhighlight %}

This allows me to put an "exit" command in my code and then do something like:

	$ sqlite3 app/resources/tmp/test.db "SELECT * FROM employees;"
	-- Loading resources from /home/gavin/.sqliterc

	id          name        notes        department           
	----------  ----------  -----------  ---------------------
	1           Foobar      some stuff2  Scamping and nonsense
	2           Bazbip      some other   Music and fun        
	3           Brand new                Scamping and nonsense

In-memory is a lot faster, though, so prefer that!

> For more on environments in Lithium, see [Michael Nitschinger's post on "Using Environments in Lithium"](http://nitschinger.at/Using-Environments-in-Lithium) - it's perhaps a bit dated but generally still seems to work! Michael makes some good points about how the /test route is only included in non-production environments.

## Moving on...

Right, we've now got some nice, clean separation between production, development and test. Let's go on to fixtures!