---
layout: post
title:  Authentication - creating a user
---

We went through all that business, and we can't even log in! There's not point trying to add the user in MySQL because the password would not be hashed, and as Tempa T might say, that would be a STRONG PAR.

Symfony2 has a bundle called FOSUserBundle which has functionality to add users via the command line. We're going to build something similar, but more lightweight. In the process, we'll learn about writing command line tasks in Lithium - it's more than "just" a web framework!

The [Lithium console documentation](http://lithify.me/docs/lithium/console) shows us how to create a console command, and we're going to do just that :-)

Create app/extensions/command/AdministratorCreate.php and add:

{% highlight php %}
<?php
namespace app\extensions\command;

use app\models\Administrators;

class AdministratorCreate extends \lithium\console\Command {

    public function run() {
        $this->header('Administrator Creator');
        $this->out('Creating an admin...');
		
		$admin = Administrators::create();
		$admin->username = "brucewayne";
		$admin->password = "gothamknight";
		$admin->save();
		
		$this->out('Created user ' . $admin->username);
    }
}
?>
{% endhighlight %}

Now, from the command line, run the li3 command:

    $ ./libraries/lithium/console/li3             
	Lithium console started in the development environment. Use the --env=environment key to alter this.
	...
	COMMANDS via app
		administrator-create
    
    ...

Notice "COMMANDS via app" now has a new task "administrator-create" - let's run that:

    $ ./libraries/lithium/console/li3 administrator-create
	---------------------
	Administrator Creator
	---------------------
	Creating an admin...
	Created user brucewayne

So now we have a user!

> You'd probably want to parameterise this command so that you can pass in the username and password as command line arguments, rather than hard code it like this

Let's log in as username "brucewayne", password "gothamknight". You should be logged in now, and you should be able to add users! Also, "regular" users can't add users, only the administrator.