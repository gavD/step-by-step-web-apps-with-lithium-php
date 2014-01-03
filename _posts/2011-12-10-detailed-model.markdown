---
layout: post
title:  Detailed model
---

Right, let's zoom in on this here model `app/models/Employees.php`:

{% highlight php %}
<?php

namespace app\models;

class Employees extends \lithium\data\Model {

	public $validates = array();
}

?>
{% endhighlight %}

Not a whole lot going on here... OR IS THERE???

Well, namespacing (`namespace app\models;`) is a pretty important concept - loosely analogous to Java packages. If you're not up on that, please take a look at the [PHP documentation](http://php.net/namespaces).

Next up, the class Employees extends [\lithium\data\Model](https://github.com/UnionOfRAD/lithium/blob/master/data/Model.php). It's worth taking a look at that class. It's pretty big but don't let that put you off - it's not like you need to memorise it or anything, just have an awareness of what's in there :-). I'm going to assume you have some knowledge of object oriented programming (OOP) - if not, you might pick a bit up here but this book's about Lithium rather than OOP - I'm not sure what book I'd recommend to be honest, generally I recommend the [Head First books](http://www.headfirstlabs.com/) because they're not boring but they don't do a specific OOP one AFAIK.

Aaaanyway, by extending Model, Employees gets all the gubbins of Model without copying/pasting it. Nice.

The `$validates array` is about validation of the fields that Employees may have - rules we can apply to that. We'll cover that later; I'm sure we'd both prefer to keep it as simple as possible! I'm somewhat like Homer Simpson - every time I learn something I forget something. Not sure what I forgot when I learned Lithium - I can't remember!

We're going to manually define our fields. We don't have to, we can lazy load them from the database, but here we go defining three fields:

{% highlight php %}
<?php
namespace app\models;

class Employees extends \lithium\data\Model {
	public $validates = array();

    // define the schema for this Model - what fields it has
    protected $_schema = array(
        'id' => array(
			'type' => 'id',
			'length' => 10,
			'null' => false,
			'default' => null
		),
        'name' => array(
			'type' => 'string',
			'length' => 64,
			'null' => false,
			'default' => null
		),
        'notes' => array(
			'type' => 'string',
			'length' => 255,
			'null' => false,
			'default' => null
		),
        'department' => array(
			'type' => 'string',
			'length' => 64,
			'null' => false,
			'default' => null
		),
    );
}
?>
{% endhighlight %}

Cool, so we've described what the Employees model will look like. Now we need to test it!