---
layout: post
title:  Improved logging
---

Back so soon? Great, well done!

In a production system, you might want to log to a database rather than a file - for example, if you have multiple front ends, you might not want to have to pick through each box's logs separately. Both have their advantages, of course - sometimes, it might be a single node that is exhibiting a problem. So, it's good to log locally, and also to have a shared log - perhaps to something like MongoDB or another database.

It's a good idea to make use of the full spectrum of logging levels. In development and debugging, you might want debug messages turned on, but in a production environment, it could well be overkill.

## What's wrong with this picture?

Well, if you run your tests again (which, by the way, you should do if you make any changes. Sorry, I probably should have said that earlier! It helps to prevent bugs from creeping in!), you'll notice that, because we have a test that covers EmployeesController::index, that writes to the log file now! I don't particularly want my unit tests writing to the logs.

Once again, Li3 to the rescue! Li3 has the concept of *environments* - if you've done and Rails or Symfony2 or similar you'll probably be familiar with the concept. Generally, environments take the form of:

* `test` - running unit and integration tests.
* `development` - your workspace on your machine, as you're building and testing your app. Often kind of like a motorbike stripped down and strewn over the living room floor.
* `production` - the app in "running fully optimised" mode.

Logger is an **adapter**, and in Li3 all **adapters** accept the environment name as a key, we're going to tell Li3 "in test mode, only log emergencies" but to log more types of error in dev/production. Open up `app/config/bootstrap/logging.php` and change it to:

{% highlight php %}
<?php
use lithium\analysis\Logger;

Logger::config(
    array ('default' =>
        array(
            'production' => array(
                'adapter' => 'File',
                'priority' => array('emergency', 'alert', 'critical', 'error')
            ),
            'development' => array(
                'adapter' => 'File',
                'priority' => array('emergency', 'alert', 'critical', 'error', 'warning')
            ),
            'test' => array(
                'adapter' => 'File',
                'priority' => array('emergency')
            )
    )
));
?>
{% endhighlight %}

This means that running tests won't spam our logs. Cool!

> Do make sure that `app/config/bootstrap/logging.php` is included from `app/config/bootstrap.php`

Remember that file based logs are output to `app/resources/tmp/logs`. Right that's it for logging for now - let's do some authentication!