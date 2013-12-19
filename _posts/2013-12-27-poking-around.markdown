---
layout: post
title:  "A Home Screen"
---

# Poking around

Open the project in your favourite editor or IDE. I tend to use Netbeans - which is free and pretty good - although lots of people prefer the commercial PHPStorm. I'm not going to tell you what to use, I'm only mentioning a couple that I know are good in case you don't have one :-).

So, in your project you can see a few files:

* web.config - this is for IIS on Windows servers. I'm going to assume you're not using Windows, if you are, well all the best to you but it's not my bag for PHP development! Delete it if you're not on Windows.
* .htaccess - this is for the Apache web server and sets up URL rewriting, assuming you have mod_rewrite. If you used Nginx or set up rewriting elsewhere, ignore or even delete this.
* app - this is where **YOUR CODE** lives.
* libraries - this is where **LITHIUM** lives. The Git submodule is hooked up to this folder.
* index.php - this includes the index.php from the web root. I'd personally delete this, I feel it's a bit misleading - I wonder if it's there for some purpose like supporting command line access or something? If anyone can enlighten me, feel free!

## Commitment issues

At this point, I suggest committing to Git. You could push up to your personal Github or Bitbucket as you go - commit often!

I really don't recommend doing ANY software development without version control. That's probably all I'll say about version control as that's a whole separate topic, but don't leave /home without it! ;-)

## Going a bit deeper

So far so good, all makes sense... Now let's delve into ./app

# A Home Screen

"There's no place like home, there's no place like home" I used to mutter to myself as rain lashed down at scout camp... Well actually that's not strictly true, I loved camping until I went to Glastonbury in 2003 and got mild heat stroke. Still, most web applications need a homepage!

