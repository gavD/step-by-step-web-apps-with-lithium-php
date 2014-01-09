---
layout: post
title:  Elegant links with helpers
---

Remember we have the "Admin log in" and "Log out" links? Well, it would be nice to only show one at a time, depending if the user is logged in or out.

Although Lithium's templates are essentially PHP, it's bad form to put code in there - it muddies the water and makes testability a bit harder. Instead, we're going to use a **helper**. [Helpers are documented in the Li3 manual](http://li3.me/docs/manual/handling-http-requests/helpers.wiki), but we'll go through them here for our specific use.

First thing to do is add a new file `app\extensions\helper\LoginLogoutControl`:

{% highlight php %}
<?php
namespace app\extensions\helper;

use lithium\security\Auth;

class LoginLogoutControl extends \lithium\template\Helper {
    public function link($loginUrl, $logoutUrl) {
        if (Auth::check('admin')){
            $url = $logoutUrl;
            $title = 'Log out';
        } else {
            $url = $loginUrl;
            $title = 'Admin log in';
		}
        return $this->_render(__METHOD__, 'link', compact('url', 'title'));
    }
}
?>
{% endhighlight %}

This is extending the core `Helper` class, which has a `_render_` method. We're saying "Hey `_render_` this is a link we are rendering, here's the URL and title to use".

You can do a lot more with Helper classes, as we'll see later, but this suffices for the time being. Here's what I've done to the `app/views/elements/header.html.php`:

{% highlight php %}
<!-- ... -->
<?php
    <li><a href="<?= $this->url(array('Home::index')); ?>">
        Homepage</a></li>
    <li><a href="<?= $this->url(array('Employees::index')); ?>">
        Employees</a></li>
    <li><?=$this->loginLogoutControl->link(
            $this->url(array('Administrators::login')),
            $this->url(array('Administrators::logout'))
        ) ?></li>
</ul>
<!-- ... -->
{% endhighlight %}

Great - now we should have a little control that intelligently shows the login or logout link depending on whether we are logged in or not... Decent!

Notice that we're passing in the URLs. We could tie the URLs in to the `LoginLogoutControl` - if we do that, though, the `LoginLogoutControl` is dependent on `AdministratorsController`. With the way we've done it, we could pass any two links into it. Whether you'd want to do that or not is entirely up to you but I've done it this way to show the flexibility.

> Take a look at [the Li3 Html helper](https://github.com/UnionOfRAD/lithium/blob/master/template/helper/Html.php) - it's got all sorts of nice methods for rendering bits of HTML. There is a `link` method in there that's worth looking at!

Helpers are great because:

1. They have all sorts of escaping functionality
1. You can test them separately to your views
1. You don't have to throw a load of code into views
1. They are another way of achieving code reuse

Hopefully you can see the benefits! The next chapter unpacks how helpers work a bit more.