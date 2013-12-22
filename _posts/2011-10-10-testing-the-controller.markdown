---
layout: post
title:  "Testing the controller"
---

Well it's not BROKEN exactly, but our template has a "Homepage" link but NOT a "Rolodex" link, so if we  click on "Homepage" we can come unstuck!

Also, after the last few chapters have covered a lot of stuff, I thought it'd be good to have a quick, easy and simple chapter!

Anyway, open up app/views/layouts/default.html.php and find the navbar HTML:

{% highlight php %}
...
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="navbar-inner">
            <div class="container">
                <ul class="nav nav-pills pull-right">
                    <li><a href="<?= $this->url(array('Home::index')); ?>">Homepage</a></li>
                    <li><a href="<?= $this->url(array('Staff::index')); ?>">Staff</a></li>
                </ul>
            </div>
        </div>
    </div>
...

{% endhighlight %}

What we've done here is added a link to the "Staff" index route. Notice that the $this object has a method url which renders a path to a route. This means you can move things around without breaking stuff - if you use absolute URLs you're going to have a bad time!

aaaaand... That's it for this chapter! Pretty easy, right?