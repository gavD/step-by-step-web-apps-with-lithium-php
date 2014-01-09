---
layout: post
title:  More on helpers
---

This book's not really about delving into the guts of Li3 too much but I think this bears explaining. When we call this line:

{% highlight php %}
<?php
// ...
        return $this->_render(__METHOD__, 'link', compact('url', 'title'));
// ...
{% endhighlight %}

li3's method `_render` gets kicked off. There is a bit of code in there that finds some strings by going through all the handlers it "knows about":

{% highlight php %}
<?php
// ...
		if ($this->_context) {
			foreach ($params as $key => $value) {
				$handler = isset($options['handlers'][$key]) ? $options['handlers'][$key] : $key;
				$params[$key] = $this->_context->applyHandler(
					$this, $method, $handler, $value, $options
				);
			}
			$strings = $this->_context->strings();
		}
{% endhighlight %}

It finds the "link" string from the class `lithium\template\helper\Html` and uses that.

We can change it:

{% highlight php %}
<?php
namespace app\extensions\helper;

use lithium\security\Auth;

class LoginLogoutControl extends \lithium\template\Helper {
    
    protected $_strings = array(
        'loginControl' => '<li><a href="{:url}">{:title}</a></li>',
    );
    
    public function link($loginUrl, $logoutUrl) {
        if (Auth::check('admin')){
            $url = $logoutUrl;
            $title = 'Log out';
        } else {
            $url = $loginUrl;
            $title = 'Admin log in';
		}
        return $this->_render(__METHOD__, 'loginControl', compact('url', 'title'));
    }
}
?>
{% endhighlight %}

Now, we are no longer using the **build in** string "link", we've defined our own "loginControl". "loginControl" is not super fancy or anything, it simply has an additional `<li />` tag wrapping it and loses the `{:options}` placeholder. Hopefully this demonstrates the concept!

