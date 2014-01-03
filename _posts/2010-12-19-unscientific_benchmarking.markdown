---
layout: post
title:  Unscientific benchmarking
---

What we need to do now is figure out roughly how much computar machines we need to make our application run! It depends on a lot of factors - most notably, how many users we are likely to need to support.

If we assume we have 1000 Employees, and that 10% of them want to use this application at once, what we can do is benchmark the application and work out roughly how many servers we'd need.

The utility we're going to use is called "ApacheBench", which is the "ab" command. You will need to install it if you don't have it already.

There are plenty of other utilities that do a similar job. "Siege" is another one. There are dedicated utilities that can simulate user behaviour and all sorts of stuff and if you're a pro level operation, it's a good idea to have some load testing that's really representative of what your users are likely to be doing. Tools like ApacheBench get you started though so you're not TOTALLY in the dark!

Be sure to load test against a server that you might be hosting on - if you use Linode, for example, set up a Linode with your application on. If you are testing against your own laptop, that's only useful for comparison over time - it's not the same as testing against something out in the Interwebs at large.

Do be careful not to get into trouble load testing, though! If you go crazy with it you might look like you're trying to DOS a site, and your network administrator might be breathing down your neck! If you're on a corporate network, make sure your SysAdmin knows what you're up to!

> I'm calling this "Unscientific" because we're only testing locally. Test on representative hardware when you can! Testing locally is useful for comparative tests though - "oh, this is quicker now" - but don't be deceived - sometimes, one page running quicker might be consuming more memory or similar, so could actually perform WORSE in the field...

Here, I check that with a concurrency level of 100 (`-c 100`) I can service 1000 requests (`-n1000`):

	$ ab -c100 -n1000 "http://employee-rolodex.localhost/employees"  
	This is ApacheBench, Version 2.3 <$Revision: 655654 $>
	Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
	Licensed to The Apache Software Foundation, http://www.apache.org/

	Benchmarking employee-rolodex.localhost (be patient)
	Completed 100 requests
	Completed 200 requests
	Completed 300 requests
	Completed 400 requests
	Completed 500 requests
	Completed 600 requests
	Completed 700 requests
	Completed 800 requests
	Completed 900 requests
	Completed 1000 requests
	Finished 1000 requests


	Server Software:        nginx/1.2.6
	Server Hostname:        employee-rolodex.localhost
	Server Port:            80

	Document Path:          /employees
	Document Length:        1808 bytes

	Concurrency Level:      100
	Time taken for tests:   3.301 seconds
	Complete requests:      1000
	Failed requests:        0
	Write errors:           0
	Total transferred:      1989000 bytes
	HTML transferred:       1808000 bytes
	Requests per second:    302.92 [#/sec] (mean)
	Time per request:       330.122 [ms] (mean)
	Time per request:       3.301 [ms] (mean, across all concurrent requests)
	Transfer rate:          588.38 [Kbytes/sec] received

	Connection Times (ms)
				  min  mean[+/-sd] median   max
	Connect:        0    0   1.3      0       6
	Processing:    18  314  53.9    326     361
	Waiting:       18  314  53.9    326     361
	Total:         24  315  52.7    326     363

	Percentage of the requests served within a certain time (ms)
	  50%    326
	  66%    331
	  75%    335
	  80%    338
	  90%    343
	  95%    346
	  98%    349
	  99%    352
	 100%    363 (longest request)

Yep, that's all good - on my machine, at least! YMMV.

I won't go into any more detail here. The point is - never guess at your figures, in Lithium, or in any other framework! It's a really bad idea to GUESS at how much our application can handle. If you want to know more about this, there's a chapter on it in my book, [Deal With It: Attitude for Coders](https://leanpub.com/dealwithit).

How can we perform better? Let's take a look at caching...