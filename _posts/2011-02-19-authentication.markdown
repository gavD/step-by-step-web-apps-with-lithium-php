---
layout: post
title:  Authentication
---

We have a working app that we can add users to. Thing is, we don't want just any old sausage coming along and mucking things up! Let's modify the app so that there are administrators (as per our user stories, waaaay back in the mists of time!)

We *could* use our existing Employees object as the Administrator - we could call it "User" and make it more generic. Instead, though, we're going to simply have the model "Administrator".

We'll need to add:

* Administrator model
* Login/logout controller functionality
* Some way of adding administrator
* Tests that make sure non-authenticated users cannot administrate users
* Tests that make sure administrators can administrate users

That's quite a bit! Good news is it's not super difficult in Lithium :-)

> I've adapted some of this chapter from [Matt Vandel's tutorial](https://sites.google.com/a/mattvanandel.com/lithium-framework/home) - it's been around a few years but is still well worth a read

## Create authentication configuration

Now open `app/config/bootstrap.php` - you should have something like:

{% highlight php %}
<?php

// ...

/**
 * This file contains configuration for session (and/or cookie) storage, and user or web service
 * authentication.
 */
// require __DIR__ . '/bootstrap/session.php';
{% endhighlight %}

Uncomment that line so that session.php is getting included.

Open up `app/config/bootstrap/session.php`. Here's mine - don't just blindly copy it (not that I think you would! I have every confidence in you!), but have a read through it first and take note of the comments in the default version. Might be a good idea to keep those comments in there rather than dump them :-)

{% highlight php %}
<?php
use lithium\security\Auth;
use lithium\storage\Session;

$name = basename(LITHIUM_APP_PATH);
Session::config(array(
	'default' => array('adapter' => 'Php', 'session.name' => $name)
));

Auth::config(array(
    'admin' => array(
        'adapter' => 'Form',
        'model'   => 'Administrators',
        'fields'  => array('username', 'password')
    )
));
?>
{% endhighlight %}

What this is saying is "configure sessions to use PHP sessions with a name based on the LITHIUM_APP_PATH - so, if you have multiple Lithium instances on a single node, they won't conflict. Then, we're configuring Auth saying "we have a model called **Administrators** that we're going to log in with using the model's fields **username** and **password**. All good?

Right, but this isn't enough codes - we're talking about a model that doesn't even exist yet! Have we lost our minds?

Far from it! Let's create the Administrators model in `app/models/Administrators.php`:

{% highlight php %}
<?php
namespace app\models;

class Administrators extends \lithium\data\Model {
    public $validates = array(
        'username' => array(
            array('notEmpty', 'message' => 'Please enter a username')
        ),
        'password' => array(
            array('notEmpty', 'message' => 'Please enter a password')
        ),
    );
}
?>
{% endhighlight %}

> ** Note on fields** Although we've used validation, we haven't defined fields for this class. In Lithium, we don't need to, but it'll perform a tiny bit better if we do as it skips a DESCRIBE operation (or equivalent).

## Creating our controller

Create `app/controllers/AdministratorsController.php`:

{% highlight php %}
<?php
namespace app\controllers;

use lithium\security\Auth;

class AdministratorsController extends \lithium\action\Controller {

	public function login() {
		if (Auth::check('admin', $this->request)) {
			return $this->redirect(array('Employees::index'));
		}
		
		$loginFailed = false;
		if ($this->request->data){
			$loginFailed = true;
		}
		return compact('loginFailed');
	}

	public function logout() {
        Auth::clear('admin');
        return $this->redirect('/');
    }
}
?>
{% endhighlight %}

One more thing! You might be overloading, but as Terry Wogan said on his Secret Pirate Radio Podcast, stay with me, stay with me! We're nearly there!

Create `app/views/administrators/login.html.php`:

{% highlight php %}
<?php if ($loginFailed): ?>
	<div class="text-error">Login failed - please check your credentials</div>
<?php endif; ?>
<?=$this->form->create(); ?>
<?=$this->form->field('username');?>
<?=$this->form->field('password', array('type'=>'password'));?>
<?=$this->form->submit('Log in');?>
<?=$this->form->end();?>
{% endhighlight %}

This creates a small, simple form, much like the add user form. Now, head on over to [http://employee-rolodex.localhost/administrators/login](http://employee-rolodex.localhost/administrators/login) and you should get an error like:

    Fatal error: Uncaught exception 'lithium\data\model\QueryException' with message 'DESCRIBE `administrators`: Table 'Employee.administrators' doesn't exist'

Right, I guess we'd better create one! Open your database in MySQL and enter:

	CREATE TABLE `administrators`( 
	   `id` int UNSIGNED NOT NULL AUTO_INCREMENT , 
	   `username` varchar(255) NOT NULL, 
	   `password` varchar(255) NOT NULL, 
	   PRIMARY KEY (`id`)
	);

Now, when we go to [http://employee-rolodex.localhost/administrators/login](http://employee-rolodex.localhost/administrators/login) we should see:

![Login screen](images/loginscreen.png)

Now, before we log in, let's actually secure something. Open app/controllers/EmployeesController.php and stick a bit of code at the top of the add method:

{% highlight php %}
<?php
// ...
use lithium\security\Auth;
// ...

	public function add() {
		if ( !Auth::check('admin', $this->request) ){
			return $this->redirect(array('Administrators::login'));
		}
{% endhighlight %}

Now, still not logged in, let's try adding a user by clicking "Employees" at the top and then "Add" - and we get taken to the administrator login form - great!

Right, now let's log in using the credentials username "brucewayne", password "gothamknight" (of course, he wouldn't be so stupid as to use those credentials, but I can't guess what the great detective would choose!) - but obviously there are no users so we get an error message:

![Login screen](images/loginscreen.png)

> You might want to add a flash message at this point. [Here's a tutorial on doing just that](http://www.jblotus.com/2011/08/17/adding-a-session-flash-message-to-your-site-in-lithium-php/).

That's the groundwork laid, what's next?