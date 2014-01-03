---
layout: post
title:  Unscientific benchmarking
---

What we need to do now is figure out roughly how much computar machines we need to make our application run! It depends on a lot of factors - most notably, how many users we are likely to need to support.

If we assume we have 1000 Employees, and that 10% of them want to use this application at once, what we can do is benchmark the application and work out roughly how many servers we'd need.

> It's a really bad idea to GUESS at how much our application can handle. If you want to know more about this, there's a chapter on it in my book, [Deal With It: Attitude for Coders](https://leanpub.com/dealwithit).

The utility we're going to use is called "ApacheBench", which is the "ab" command. You will need to install it if you don't have it already.

There are plenty of other utilities that do a similar job. "Siege" is another one. There are dedicated utilities that can simulate user behaviour and all sorts of stuff and if you're a pro level operation, it's a good idea to have some load testing that's really representative of what your users are likely to be doing. Tools like ApacheBench get you started though so you're not TOTALLY in the dark!

Be sure to load test against a server that you might be hosting on - if you use Linode, for example, set up a Linode with your application on. If you are testing against your own laptop, that's only useful for comparison over time - it's not the same as testing against something out in the Interwebs at large.

Do be careful not to get into trouble load testing, though! If you go crazy with it you might look like you're trying to DOS a site, and your network administrator might be breathing down your neck! If you're on a corporate network, make sure your SysAdmin knows what you're up to!