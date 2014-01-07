---
layout: post
title:  Querying models
---

We've built a complete application, but that's far from the whole story! We've glossed over a lot of topics, so let's delve into them a bit further!

Lithium's models have a fair amount of reasonably powerful methods that use a bit of magic. Let's try some out!

We're going to create a new action on the user controller to allow us to look at a specific department. Open up `app/controllers/EmployeesController.php` and add the following method:

// TODO

{% highlight php %}
<?php
// ...
    
    public function donk() {
        $employees = Employees::find('all', array(
            'conditions' => array('department' => 'donk')
        ));

        return $this->render(array(
            'template' => 'index',
            'data' => array(
                'employees' => $employees
            )
        ));
    }
// ...
{% endhighlight %}

We've sorta hacked that in there so it dumps out all the employees who work in department "donk". If you don't have any, then create some! Now, if you go to /donk, you'll see some employees there.

> Notice what we've done with the return array - we're specifying "use the same template as the index action" - or `app\views\employees\index.html.php`. This is great because we don't have to duplicate our template!

## Simplifying

Try this now:

{% highlight php %}
<?php
// ...
    public function donk() {
        $employees = Employees::findAllByDepartment('donk');
        
// ...
{% endhighlight %}

Notice that this code is now more **semantic** - that is, it communicates its meaning more explicitly. This takes advantage of the fact that Li3 models offer "find*" **magic methods**.

## Removing data

Let's say we wanted to sack everyone:

{% highlight php %}
<?php
// ...
    public function sackall() {
        Employees::remove();
        return $this->redirect('Employees::index');
    }
        
// ...
{% endhighlight %}

> TODO flash message here

That will remove _all_ the Employees completely whenever you hit [/employees/sackall](http://employee-rolodex.localhost/employees/sackall) - so be careful with the `remove` method!

If you just wanted to remove employees with department donk you could have:

{% highlight php %}
<?php
// ...
    public function sackdonk() {
        Employees::remove(array('department' => 'donk');
        return $this->redirect('Employees::index');
    }
        
// ...
{% endhighlight %}

> Please note that remove* "magic" methods don't seem to be implemented in the same way as find* are

There is plenty more we can do with models!

> More information can, of course, be found in the [Li3 manual section on using models](http://li3.me/docs/manual/working-with-data/using-models.wiki).