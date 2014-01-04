---
layout: post
title:  Getting started
---

OK, so we know what Lithium is and what we're going to build with it! Roughly. Well, we know what we want to get out of it, anyway.

I guess to get started we'd better get Lithium installed... Before that, though, make sure you have PHP installed and a web server - I generally use Nginx but Lithium has instructions for plenty of servers.

I won't duplicate the Lithium docs here - see [the requirements page for Lithium](http://li3.me/docs/manual/getting-started/requirements.wiki) and [the Lithium installation guide](http://li3.me/docs/manual/getting-started/installation.wiki) to get up and running.

We'll start with the sample app and go from there! Check out [Union of Rad's Framework from Github](https://github.com/UnionOfRAD/framework) and set it up as per the Lithium docs. What you want to get to is seeing this screen:

![Default Lithium framework screen](images/default-lithium.png)

## Here's how I did it

This section is just how I set it up. This is not "documentation", this is just what I did, it's better to read [li3.me/docs/manual](http://li3.me/docs/manual) and follow their guide, but I'm including my gubbins for reference.

Open a terminal and run:

{% highlight bash %}
cd /var/www
git clone https://github.com/UnionOfRAD/framework.git employee-rolodex
cd employee-rolodex
{% endhighlight %}

We can haz a checkout! Now let's set up a hosts file entry for development so that requests to "employee-rolodex.localhost" get routed back to your computron on local address 127.0.0.1. You don't have to do it this way - PHP has a built in web server etc but here's what works for me!

{% highlight bash %}
sudo sh -c 'echo "127.0.0.1 employee-rolodex.localhost" >> /etc/hosts'
{% endhighlight %}

Right, next job - set up a web server to listen for requests to employee-rolodex.localhost - here's my config:

{% highlight bash %}
cd /etc/nginx/sites-enabled
sudo vim employee-rolodex.localhost
{% endhighlight %}

Now your mileage REALLY may vary here, so follow the docs rather than me, but what worked for me is:

{% highlight nginx %}
server {
        server_name employee-rolodex.localhost;
        root /var/www/employee-rolodex/app/webroot;

        index index.php;

        location / {
                try_files $uri $uri/ /index.php?$args;
        }

        include global/restrictions.conf;
        # Pass all .php files onto a php-fpm/php-fcgi server.
        location ~ \.php$ {
            try_files $uri =404;

            fastcgi_split_path_info ^(.+\.php)(/.+)$;

            include fastcgi_params;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass php;
        }
}
{% endhighlight %}

> The file global/restrictions.conf is something I "borrowed" from the [Wordpress Nginx setup page](http://codex.wordpress.org/Nginx)!

Then I restarted nginx:

    sudo /etc/init.d/nginx restart

(Again, I'm on Ubuntu, so your specific instructions may vary)

OK now if you browse to [http://employee-rolodex.localhost/](http://employee-rolodex.localhost/) - well, initially Chrome will probably try to search for that, but if you glare at it for a bit and make sure to include the http:// at the start and / at the end, it generally gets the message eventually! You should have a lovely encouraging error message now. It means that Lithium hasn't been installed. The "framework" repo includes Lithium as a submodule, so run:

{% highlight bash %}
cd /var/www/employee-rolodex
git submodule init && git submodule update
{% endhighlight %}

Now, if you browse to [http://employee-rolodex.localhost/](http://employee-rolodex.localhost/) you should see a nice screen!

![Default Lithium framework screen](images/default-lithium.png)

OK that's pretty sweet right now, we have a page up! Let's do some coding!