---
layout: post
title:  Authentication - filters
---

We mustn't go around sticking users straight into the database. It's really important that the passwords are **hashed**.

> Hashing is a one-way encryption. When we come to check a password, we never compare directly the actual password directly, but we compare hashes. Also, a good hashing algorithm should be quite slow so that it's hard for an attacker to brute force.

Lithium is pretty smart in that it will pick the best encryption available on your system. We will add a filter, [as per the Lithium docs on the matter](http://li3.me/docs/manual/auth/simple-auth-user.wiki), that will automatically encrypt passwords. I've added this code the the Administrators model:

{% highlight php %}
<?php
namespace app\models;

use lithium\security\Password;

class Administrators extends \lithium\data\Model {
	public $validates = array(
		'username' => array(
			array('notEmpty', 'message'=>'Please enter a username')
		),
		'password' => array(
			array('notEmpty', 'message'=>'Please enter a password')
		),
	);
}

Administrators::applyFilter('save', function($self, $params, $chain) {
	if ($params['data']) {
		$params['entity']->set($params['data']);
		$params['data'] = array();
	}
	if (!$params['entity']->exists()) {
		$params['entity']->password = Password::hash($params['entity']->password);
	}
	return $chain->next($self, $params, $chain);
});
?>
{% endhighlight %}

Right, so we've got Administrators now that, if they get saved, automatically have the password set... Just one problem here. How do we create our FIRST user? It all gets a bit "chicken or the egg"! Find out, in the next THRILLING installment!