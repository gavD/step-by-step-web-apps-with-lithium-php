---
layout: post
title:  Authentication
---

We have a working app that we can add users to. Thing is, we don't want just any old sausage coming along and mucking things up! Let's modify the app so that there are administrators (as per our user stories, waaaay back in the mists of time!)

We *could* use our existing Staff object as the Administrator - we could call it "User" and make it more generic. Instead, though, we're going to simpy have the model "Administrator".

We'll need to add:

* Administrator model
* Login/logout controller functionality
* Some way of adding administrator
* Tests that make sure non-authenticated users cannot administrate users
* Tests that make sure administrators can administrate users

That's quite a bit! Good news is it's not super difficult in Lithium :-)

> I've adapted some of this chapter from [Matt Vandel's tutorial](https://sites.google.com/a/mattvanandel.com/lithium-framework/home) - it's been around a few years but is still well worth a read


