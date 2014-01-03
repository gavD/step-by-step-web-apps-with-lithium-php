---
layout: post
title:  Improved logging
---

Back so soon? Great, well done!

In a production system, you might want to log to a database rather than a file - for example, if you have multiple front ends, you might not want to have to pick through each box's logs separately. Both have their advantages, of course - sometimes, it might be a single node that is exhibiting a problem. So, it's good to log locally, and also to have a shared log - perhaps to something like MongoDB or another database.

It's a good idea to make use of the full spectrum of logging levels. In development and debugging, you might want debug messages turned on, but in a production environment, it could well be overkill.

## What's wrong with this picture?

Well, if you run your tests again (which, by the way, you should do if you make any changes. Sorry, I probably should have said that earlier! It helps to prevent bugs from creeping in!), you'll notice that, because we have a test that covers EmployeesController::index, that writes to the log file now! I don't particularly want my unit tests writing to the logs.

Once again, Lithium to the rescue! Lithium has the concept of *environments* - if you've done and Rails or Symfony2 or similar you'll probably be familiar with the concept. Generally, environments take the form of:

* **test** - running unit and integration tests.
* **development** - your workspace on your machine, as you're building and testing your app. Often kind of like a motorbike stripped down and strewn over the living room floor.
* **production** - the app in "running fully optimised" mode.

We're going to tell Lithium "in test mode, don't bother with that logging gubbins".

Open up app/config/bootstrap/logging.php and change it to:

{% highlight php %}
<?php
use lithium\analysis\Logger;
use lithium\core\Environment;

if (!Environment::is('test')) {
	Logger::config(array(
		'default' => array('adapter' => 'Syslog'),
		'problems' => array(
			'adapter' => 'File',
			'priority' => array('emergency', 'alert', 'critical', 'error')
		)
	));
}
?>
{% endhighlight %}

TODO why doesn't this work?