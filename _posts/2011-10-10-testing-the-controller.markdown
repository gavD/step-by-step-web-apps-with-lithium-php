---
layout: post
title:  "Testing the controller"
---

Remember a couple chapters back, we looked at unit testing our models? Here's how I approach testing:

![Testing Pyramid]({{ site.url }}/assets/testing-pyramid.png)

I think I copped this off Kent Beck or Bob Martin, but here's my take on it. **Unit tests** are for the developer. For us, we're testing the low level logic in our models behaves _as we understand it_, so we're verifying our comprehension of the system (as well as looking out for regressions).

**Integration tests** check that the subsystems play nice. For our example, this is testing our controllers - a lot of people don't test controllers so much but for me it's a point I like to test at because it's simulating user input and the output that comes back to the user.

**System tests** I like to tie directly into **user stories** using **functional testing**, we'll look at that later. How exciting!

So, we're going to test our controllers, to make sure our systems integrate well. This 