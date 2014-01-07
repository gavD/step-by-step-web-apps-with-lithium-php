---
layout: post
title:  Flash messages
---

_"Flash! Ahhh-ahhhh!" - Queen, Flash Gordon soundtrack_

Flash messages is a way of putting a message in the session for display on the next page. It's really useful when you have some stuff to tell the user but you don't want to interrupt processing. It's a nice "set it and forget it" way of putting message out to the user. It works really well with pages that do a redirect. Once the flash message has been shown, it's removed from the system - i.e. it's "flashed up" once - so it's best used for one-off pieces of information, like "setting changed", "file saved", "message sent" - things like that.

> Because we're using [Twitter Bootstrap](http://getbootstrap.com) in our project, we already have some nice looking themes we can use for rendering flash messages. Bootstrap is a fantastic springboard for producing decent looking sites quickly - I use it for pretty much anything I'm building! (although, that being said, competing framework [Foundation](http://foundation.zurb.com/) actually got me a date with an amazing girl, Bootstrap can't say that!)

At this point we're going to install [Michael Hue's li3_flash_message plugin](https://github.com/michaelhue/li3_flash_message). I've done it as a submodule by entering the following command in the root of my app through my console:

    $ git submodule add -f https://github.com/michaelhue/li3_flash_message.git libraries/li3_flash_message

> A lot of people don't like submodules. I don't either - they give me the jibblies. There's one now! (jibbly, jibbly). Later on we'll look at alternative strategies for dealing with dependencies :-)

then in `app\config\bootstrap\libraries.php` we should end up with something like:

{% highlight php %}
// ...
/**
 * Add some plugins:
 */
// Libraries::add('li3_docs');

Libraries::add('li3_quality');
Libraries::add('li3_fixtures');
Libraries::add('li3_flash_message');
?>
{% endhighlight %}

Give your homepage a check to make sure you've got it installed correctly - you'll see a big, ugly error message if you haven't!

## y u do dis?!

I'm adding it as a core dependency in `libraries` alongside "lithium" rather than in `app\libraries`. The distinction in our case is that we simply put the `li3_quality` and `li3_fixtures` in `app\libraries` rather than making them submodules. This is because they are **test/dev dependencies**, not **production dependencies**. We're going to be using the flash messages library regardless of what environment we're in - ergo, core dependency right up there with the framework itself. You don't have to use this convention, but it makes sense to me!

## Adding Flash message

Let's let the admins know when they've been logged in or out - open up `app/controllers/AdministratorsController.php`:

{% highlight php %}
<?php
namespace app\controllers;

use lithium\security\Auth;
use li3_flash_message\extensions\storage\FlashMessage;

class AdministratorsController extends \lithium\action\Controller {
	public function login() {
		if (Auth::check('admin', $this->request)) {
            FlashMessage::write('Logged you in!');
			return $this->redirect(array('Employees::index'));
		}

		$loginFailed = false;
		if ($this->request->data){
			$loginFailed = true;
		}
		return compact('loginFailed');
	}

	public function logout() {
        FlashMessage::write('Logged you out!');
		Auth::clear('admin');
		return $this->redirect('/');
	}
}
?>
{% endhighlight %}

Three things to note here:

1. `use li3_flash_message\extensions\storage\FlashMessage;` - include the storage object
2. `FlashMessage::write('Logged you in!');` - write a message
3. `FlashMessage::write('Logged you out!');` - write a message on logout

Final thing to do is to display these messages. Open up `app/views/elements/header.html.php` and modify the container div:

{% highlight php %}
<?php
<!-- ... -->
    <div class="container">
        
        <?= $this->flashMessage->show() ?>
        
        <h1>Rolodex</h1>
        <hr>

        <div class="content">
{% endhighlight %}

Cool, now go and log in and have a look!

![Basic flash message](images/flash-plain.png)

Do you see it? It's a bit difficult to see! Let's give it some styling. Create a file `app/views/elements/flash_message.html.php` and put the following in it:

    <span class="label label-success"><?=$message; ?></span>

![Styled](images/flash-styled.png)

This is very basic styling - we can, of course, give it a different class based on what TYPE of message it is (e.g. red for errors and so forth) but this will do us for now :-) See `libraries/li3_flash_message/app/views/elements/flash_message.html.php` for an example of how to do that.

> For more information on writing Helpers in Lithium, see [this post from Michael Nitschinger](http://nitschinger.at/Write-your-own-Helper-with-Lithium)

This is just one way of doing Flash messages with a small library I found browsing the Internet. Feel free to experiment, and it's always worth reading the source of anything you use! I wrote [a blog post about trusting the libraries you use](http://www.boxuk.com/blog/trusting-the-libraries-you-use/) a few years back, I'd recommend scanning through it!

Now we've got a nice way of relaying messages to our users, let's take a look at more sophisticated ways of querying our data!