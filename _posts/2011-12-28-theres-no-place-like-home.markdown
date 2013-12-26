---
layout: post
title:  There's no place like home
---

"There's no place like home, there's no place like home" I used to mutter to myself as rain lashed down at scout camp... Well actually that's not strictly true, I loved camping until I went to Glastonbury in 2003 and got mild heat stroke... Still, most web applications need a homepage!

With that in mind, we'll leave the default homepage alone for the time being and make our own responding on /home. Let's try browsing to [/home](http://staff-rolodex.localhost/home) right away!

You should, if you have error reporting turned on in PHP (and for development, you should have ALL THE ERRORS switched on!):

![Homeless website!](assets/homeless.png)

Oh dear! Hmm. Let's take a look at this error message in detail. The pertinent bit is:

> Fatal error: Uncaught exception 'lithium\core\ClassNotFoundException' with message 'Class `Home` of type `controllers` not found.'

So, it's looking for a **controller** named **home**... Right, just to shut it up, let's make a [controller](http://lithify.me/docs/manual/handling-http-requests/controllers.wiki)! We'll create this file in app/controller/HomeController.php - on my computer, that's /var/www/staff-rolodex/app/controllers/HomeController.php

Here's my file:

{% highlight php %}
<?php
namespace app\controllers;

class HomeController extends \lithium\action\Controller {
	public function index() {

	}
}
?>
{% endhighlight %}

Right, back on over to our homepage! Now I see:

![No template](assets/no-template.png)

So that's a _different_ error message! They say that a change is as good as a rest, and in 15 years of software development, getting a _different_ error message is almost as encouraging as actually fixing something - at least it's progress! Let's zoom in on this message:

> Fatal error: Uncaught exception 'lithium\template\TemplateException' with message 'Template not found at path `/var/www/staff-rolodex/app/views/home/index.html.php`

Well, it's been super duper helpful this time - we know what file we have to create! Remember how we have the "app/views" folder? That's where we put this stuff - and the folder "app/views/home" is called "home" because it corresponds to the "home" controller. You see, Lithium does a lot by default, including saying "well, if the controller is called foo, I'll look for the views in foo". A sensible enough assumption!

So here's what to put in app/views/home/index.html.php:

{% highlight html %}
This is a HOME PAGE!
{% endhighlight %}

Yep! Just that! Just a bit of text.

![There's no place like home](assets/no-place-like-home.png)

RESULT!

Now, we still have a lot of the Union of RAD's sample code in here - we can see it in the footer, for example - but for now we're going to leave that alone. We wouldn't go to market with their code still there though - we'd want to build our own theme! It's great for getting us up and running though, which is what it's there for :-) Notice that your code is sat inside the template - if you do a "view source" or "inspect" you can see what's going on there. Have a look at app/views/layouts/default.html.php - that's the "wrapper" template that is being applied, by, well, by default to any view. YOUR bit is shown here:

{% highlight html %}
    <!-- ... -->
		<div class="content">
			<?php echo $this->content(); ?>
		</div>
    <!-- ... -->
{% endhighlight %}

So, your code "This is a HOME PAGE!" in app/views/home/index.html.php, that is pulled out by the line <?php echo $this->content(); ?>.

Feel free to tinker around with these templates! Here's one I've made as an example! Because Lithium's framework distribution comes with Twitter Bootstrap, it's easy to make a theme that renders decently cross-device straight out of the box. (Again, there's no literal box. If there were, I'm certain a cat would have sat in it by now, this being the Internet and all...)

{% highlight php %}
<!doctype html>
<html>
<head>
    <?php echo $this->html->charset(); ?>
    <title>Staff Rolodex <?php echo $this->title(); ?></title>
    <?php echo $this->html->style(array('bootstrap.min', 'lithified')); ?>
    <?php echo $this->scripts(); ?>
    <?php echo $this->styles(); ?>
    <?php echo $this->html->link('Icon', null, array('type' => 'icon')); ?>
    <style type="text/css">
        /* This should really be in the css files but I've put it here for
           simplicity's sake! */
        body {
            padding-top: 60px;
        }
    </style>
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="navbar-inner">
            <div class="container">
                <ul class="nav nav-pills pull-right">
                    <li><a href="<?= $this->url(array('Home::index')); ?>">Homepage</a></li>
                </ul>
            </div>
        </div>
    </div>
    <div class="container">
        <h1>Staff Rolodex</h1>
        <hr>

        <div class="content">
            <?php echo $this->content(); ?>
        </div>

        <hr>
        <div class="footer">
            <p>Staff Rolodex</p>
        </div>
    </div>
</body>
</html>
{% endhighlight %}

This should render something like:

![Here's one I made earlier](assets/layout.png)

## Tell me again why this is cool?

Well, we've set up a route, very quickly, and we have a working site, and we didn't have to edit 1023 YAML files (or, worse still, XML!) to accomplish this! Now, of course, up until this point we could have achieved the same with static HTML, or even Post-It notes and crayons! Lithium offers us so much more though... It will become clear how Lithium can get us building real, interactive applications lickety-split!

We have a homepage, we have Bootstrap, now we can start building interactivity... On to the next!