---
layout: post
title:  What is Li3?
---

Li3 (formerly known as Lithium) is a PHP web framework.

A framework is a bunch of gubbins that allows you to make stuff.

OK, that wasn't very scientific, was it? I'll try again!

A web framework is some software that allows you to build your web applications in a structured, consistent manner. A framework generally has a "core" that you don't modify; rather you write extensions and plugins "on top of" the framework. It stacks up like this:


    |==============================|
    | Your application code        |
	|==============================|
    | Framework                    |
	|==============================|
    | Programming language runtime |
	|==============================|
    | Operating system             |
	|==============================|

Examples of frameworks include Java's Spring, Ruby's Rails, and Python's Django. In PHP, we have Symfony2, Silex, Slim, F3 and many others. Li3 is a framework that, once I tried it, I was hooked - it has a no-nonsense style, it's not super-heavyweight but neither is it Michael Mouse, esquire - it's well written, well tested and it just seems to largely MAKE SENSE to me! Here's Li3 in a "stack"

    |==============================|
    | Your application code        |
	|==============================|
    | Li3                          |
	|==============================|
    | PHP runtime (e.g. PHP/fpm)   |
	|==============================|
    | Linux, Unix, Windows, OSX    |
	|==============================|

Li3, like many frameworks, comes with a lot of stuff "out of the box" to allow you to get up and running quite quickly. We'll cover a lot of those features as we go. It's going to be fun!