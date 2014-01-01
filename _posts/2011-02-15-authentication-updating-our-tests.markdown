---
layout: post
title:  Authentication - updating our tests
---

A quick test run shows a problem:

	$ ./libraries/lithium/console/li3 test app/tests 
	----
	Test
	----

	........F

	FAIL

	8 / 9 passes
	1 fail and 0 exceptions

	Failed assertion assertEqual.
	 File    : /var/www/staff-rolodex/app/tests/cases/controllers/StaffControllerTest.php
	 Class   : app\tests\cases\controllers\StaffControllerTest
	 Method  : testAdd()
	 Line    : 61
	 ________
	expected: 3
	result: 2

	 ________

app/tests/cases/controllers/StaffControllerTest.php will need to be updated so that it tests firstly that non-authenticated users cannot add staff, and secondly that authenticated users can add staff.
