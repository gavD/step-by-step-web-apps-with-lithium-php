---
layout: post
title:  "A model citizen"
---

_"I wish I looked like Cindy Crawford" - Cindy Crawford_

So, your models, they're generally the things that model the real world - the things that represent your data and the things you can do to or with your data.

> The `Model` class is the starting point for the domain logic of your application.
> Models are tasked with providing meaning to otherwise raw and unprocessed data (e.g.
> user profile).

_From the [Lithium source code](https://github.com/UnionOfRAD/lithium/blob/master/data/Model.php) (which is marvellously well documented! It's a good idea to have a look through the source when you're trying to work out a problem. I certainly had to when I was writing this book!)_

# Schemas

You might have used Symfony2 or Zend where you generally define model fields in PHP. In Lithium, you can have the fields automatically loaded from the database, which is pretty interesting. In MySQL, for example, Lithium uses DESCRIBE on a corresponding table to work out what fields a data type has.

The schema (TODO DEFINE) is:

> Lazy-initialize the schema for this Model object, if it is not already manually set in the object. You can declare `protected $_schema = array(...)` to define the schema manually.

So this means you can either say up front what fields something has, or you can let Lithium inspect the database to work it out. Either approach has pros and cons - managing changes to a database can be quite a headache.

If we have the fields in the database only, it means we have less code, but it's a bit harder to write automated tests for because our code doesn't "know" about those fields.

# Enough with the schemas already!

I ended a sentence with "already". I'm not American but I am "from the Internet" so my sentence structure is pretty globalised! More on "G11n" later...

Right let's create a model. No, not like in Weird Science (even Lithium can't build people).

From the command line, go to the root of your project and type:

{% highlight bash %}
./libraries/lithium/console/li3
{% endhighlight %}

You should see something along the lines of:

<pre>
Lithium console started in the development environment. Use the --env=environment key to alter this.
COMMANDS via lithium
    create
    The `create` command allows you to rapidly develop your models, views, controllers, and tests
    by generating the minimum code necessary to test and run your application.

    g11n
    The `G11n` set of commands deals with the extraction and merging of message templates.

    help
    Get information about a particular class including methods, properties,
    and descriptions.

    library
    The Library command is used to archive and extract Phar::GZ archives. Requires zlib extension.
    In addition, communicate with the a given server to add plugins and extensions to the
    current application. Push archived plugins to the server.

    route
    The route command lets you inspect your routes and issue requests against the router.

    test
    Runs a given set of tests and outputs the results.

See `li3 help COMMAND` for more information on a specific command.
</pre>

So what have we just done? Well, this is the Lithium console - it's a command line utility that can do helpful jobs for you! It's nothing to be scared of, and it can help you out quite a bit! We're going to use the "create" command. I suggest you try "./libraries/lithium/console/li3 help create" to find out a bit more about this!

> A lot of people alias this command so they can type "li3" rather than banging in the full path to the command. That's probably a good idea but I'll stick with the full path!

Righty-ho, let's create our "Staff" model. Generally the convention in Lithium is that you create the model in the plural, although "Staff" is plural already, if I stuck an "S" on the end it would sound like a breed of dog or an English Cricket club, and I don't know anything about either of those things, so "Staff" it is!

{% highlight bash %}
./libraries/lithium/console/li3 create Staff
{% endhighlight %}

Here's the output from my terminal (side note - I use [Terminator](http://gnometerminator.blogspot.co.uk/p/introduction.html) and [Zsh](http://www.zsh.org/) with [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh) for maximum terminal sickness!)

<pre>
➜  staff-rolodex git:(master) ./libraries/lithium/console/li3 create Staff
Staff created in models/Staff.php.
StaffController created in controllers/StaffController.php.
StaffTest created in tests/cases/models/StaffTest.php.
StaffControllerTest created in tests/cases/controllers/StaffControllerTest.php.
</pre>

Wow, so this has created four files! Right, I know I said I wouldn't do this, and I don't mean to nag, but now is a really good time to do a "git status" to see what's changed, and then make a "git commit". Safety is number 1 priority!

# Whaddayagaaht?!

Have you seen The Trip? Coogan and Brydon? If not, that chapter title will make no sense to you ;-)

OK, file by file, here's what we've got:

## models/Staff.php

The Staff model. This is the bit that will represent and manipulate staff in the system.

## controllers/StaffController.php

A controller for interacting with staff. Very similar to the controller we made manually in the last chapter! I often think it's good to do stuff manually to learn it and then use a tool to abstract away the grunt work - otherwise, when the tool breaks, so do you!

## tests/cases/models/StaffTest.php

A unit test for the Staff model. I've mentioned testing but essentially, if you're not familiar with the concept, it's automated tests that you write that test your code for you. Then, when you make a change, you have some confidence that you haven't broken anything. Remember, it's not WRITING CODE that takes the time (at least, not if you're a pro-level typist like me on my [Das Keyboard](http://shop.daskeyboard.com/)!); it's thinking, learning, refactoring, maintaining and explaining that take the lion's share of time! Having tests, even though it's MORE code, makes me faster. It works for me and it's my book so if you disagree, tough!

_For a less facetious treatment of testing, please see my book [Deal With It: Attitude for Coders](https://leanpub.com/dealwithit/). You'll like it!_

In my opinion, you should NEVER code without tests. This is like doing a tightrope walk over a raging river whilst drunk. With no safety net. In the rain. With pirates. And a monkey is chewing on that rope. And it’s on fire. And everyone is shouting at you. You get the picture.

Soooo, this test is for the model. There's one more test:

## tests/cases/controllers/StaffControllerTest.php

This is an automated test for the controller. It's bare bones, there's no actual implementation, but it serves as a nice reminder to write the tests!

Wow, we covered a LOT there! Let's take a break and play Pong for a bit.