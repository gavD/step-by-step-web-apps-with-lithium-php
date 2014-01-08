---
layout: post
title:  Declutter
---

_"Two MC's can't occupy the same space at the same time, it's against the laws of Physics." - Lauryn Hill (Fugees), Zealots_

We now have a working homepage AND a menu, PLUS we've using the Li3 coding standards - that's pretty sweet! Next thing to do, though, is to remove the stuff that comes with the framework project that we're not going to use.

> It's all good stuff, I recommend you have a good look at it before you delete it. To be honest, it won't matter if you don't but for my sample project, I have removed it.

## Removing Pages

Pages demonstrates simple blog functionality. Remove:

* `app/controllers/PagesController.php`
* `app/views/pages`
* `app/views/hello_world` (deleted its controller in the last chapter)

## Updating our home screen

Notice that now there is no "Pages" controller to handle the default route:

![No pages controller to handle default route](images/no-pages-controller.png)

So, let's make our HomeController the default! Open up app/config/routes.php and remove this code:

{% highlight php %}
<?php
// ...

/**
 * Here, we are connecting `'/'` (the base path) to controller called `'Pages'`,
 * its action called `view()`, and we pass a param to select the view file
 * to use (in this case, `/views/pages/home.html.php`; see
 * `app\controllers\PagesController` for details).
 *
 * @see app\controllers\PagesController
 */
Router::connect('/', 'Pages::view');

/**
 * Connect the rest of `PagesController`'s URLs. This will route URLs like
 * `/pages/about` to `PagesController`, rendering `/views/pages/about.html.php`
 * as a static page.
 */
Router::connect('/pages/{:args}', 'Pages::view');
// ...
{% endhighlight %}

Now we're going to replace that section with:

{% highlight php %}
<?php
// ...
Router::connect('/', 'Home::index');
// ...
{% endhighlight %}

Now, when you hit [http://employee-rolodex.localhost/](http://employee-rolodex.localhost/), you should see your "This is a HOME PAGE!" screen. Result!

![Homepage controller now responding on root](images/home-default-route.png)

## About "empty"

You'll notice a bunch of files just named "`empty`" - that's to make sure that they stay in the Git repo. Thinks of them like a dressmaker's pins - they're there to hold the shape. You can delete them, but I recommend leave them alone - at least until you've put something else in each empty directory - so you can easily figure out where things need to go.