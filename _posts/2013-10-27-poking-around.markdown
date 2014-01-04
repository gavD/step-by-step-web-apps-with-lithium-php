---
layout: post
title:  Poking around
---

Open the project in your favourite editor or IDE. I tend to use [Netbeans](https://netbeans.org/features/php/) - which is free and pretty good - although lots of people prefer the commercial PHPStorm. I'm not going to tell you what to use, I'm only mentioning a couple that I know are good in case you don't have one :-).

## What are we working with here?

The Framework project that we cloned is a sample Lithium application with all the proper directories set up. It's a nice structure that we are using as a starting point.

## What's what?

So, in your project root (/var/www/employee-rolodex for me) you can see a few files:

* `web.config` - this is for IIS on Windows servers. I'm going to assume you're not using Windows, if you are, well all the best to you but it's not my bag for PHP development! Delete it if you're not on Windows.
* `.htaccess` - this is for the Apache web server and sets up URL rewriting, assuming you have mod_rewrite. If you used Nginx (as I did in the last chapter), or set up rewriting elsewhere, ignore or even delete this.
* `app` - this is where **YOUR CODE** lives.
* `libraries` - this is where **LITHIUM** lives. The Git submodule is hooked up to this folder.
* `index.php` - this includes the index.php from the web root. I delete this, I feel it's a bit misleading - I wonder if it's there for some purpose? If anyone can enlighten me, feel free!

## Commitment issues

At this point, I suggest committing to Git. You could push up to your personal Github or Bitbucket as you go - commit often!

I really don't recommend doing ANY software development without version control. That's probably all I'll say about version control as that's a whole separate topic, but don't leave /home without it! ;-)


## Going a bit deeper

So far so good, all makes sense... Now let's delve into the `app` directory. Again there are `.htaccess`, `index.php` and `web.config` files and the same advice applies and the same question of what they're doing there! Forgive my ignorance on the purpose of these files! If you check "webroot" that's where they "really" belong as far as I can tell...

With that said, there are a bunch of folders in here where you put your 1337 c0d3z:

* `app/config` - application configuration lives in here
* `controllers` - we'll talk about MVC (Model/View/Controller) - the controllers are the C of this!
* `extensions` - you can create custom commands and extend Lithium in here
* `libraries`
* `models` - The M of MVC!
* `resources` - holds, well, resources for your application - things like translations and so forth
* `tests` - very important! Store your automated tests in here
* `views` - the bits of HTML and so forth that render your application. You guessed it - this is the V of MVC! (unless you didn't guess it, don't feel bad about that, you're probably just tired. I'm pretty tired. I just had a drum lesson!)
* `webroot` - this is where you point your web server. The rest of the application should never be directly exposed to the public (hence my questioning the wisdom of putting index.php files at the two tiers above)

## A note about MVC

Feel free to skip this bit if you know this bit! There aren't any good jokes in this section, anyway.

MVC is a way of organising an application (we call it a "design pattern" because it's a common pattern in software design to organise code this way). It separates code into Model (the data gubbins and all the thinky-worky bits), View (the pretty-looky HTML and so forth that displays the model) and Controller (the thing that sends information to and from models and plonks said information into views for display).

MVC is very common in web applications and Lithium works well with this pattern right out of the box (not a real box).

![My crummy MVC diagram](images/mvc.png)
_My crummy effort at an MVC diagram_
