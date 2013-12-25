---
layout: post
title:  "Things to look into"
---

Well, hopefully we've done enough to get you up and running with Lithium development, but that's never the whole story! I'd recommend you do some research into:

* Source control (e.g. Git)
* PHP performance and configuration
* Scaling
* Hosting
* OOP
* AOP
* ORM (Object-Relational Mapper)
* FP

Specific things to read:

* [http://unassumingphp.com/lithium-getting-stuff-from-your-database/](http://unassumingphp.com/lithium-getting-stuff-from-your-database/)
* http://www.komposta.net/article/lithium-model-schema-caching - cache schema
* https://github.com/heptat/li3_ruckusing_migrations?
* http://www.slideshare.net/nateabele/lithium-the-framework-for-people-who-hate-frameworks - presentation from Nate Abele on Lithium. A few years old but still worth a browse.

Apply this to the confusing bit about routes:

<pre>
15:10 <gavin__> oops wrong button - anyway this works:
15:10 <gavin__> 	public function view($id) {
15:10 <gavin__>         $staff = Staff::first($id);
15:10 <gavin__> 		return compact('staff');
15:10 <gavin__> 	}
15:10 <gavin__> am I missing something? Should $this->request->id get set magically somewhere?
15:10 <gavin__> $this->request->args[0] works as well
15:21 <wms> gavin__ it's determined by which route is being used
15:21 <wms> if a route like `Router::connect('/{:controller}/{:action}/{:id:\d+}');
15:21 <wms> is used, then a property named `id` will be set on $request
15:22 <wms> if a route like Router::connect('/{:controller}/{:action}/{:args}');
15:22 <wms> is used, then your controller action will be called with `args`
15:23 <wms> you dig?
15:35 <gavin__> yeah I mean I get it but I'm not sure if the code the generator is writing is broken
15:39 <gavin__> might be something funky in my model
15:48 <wms> gavin__ the generated code is making the assumption that the action will be called from the route given in the first example
15:48 <wms> i believe such a route is present but commented out in the 'framework' distribution
15:49 <wms> gavin__ see https://github.com/UnionOfRAD/framework/blob/master/app/config/routes.php#L81
15:49 <gavin__> seen, thanks
15:49 <wms> otherwise, the default route on L95 will catch it, which will call view($id) rather than setting request->id
15:51 <gavin__> aha!
15:51 <gavin__> Yeah I'm with you

</pre>


TODO:
Running the quality tests
Running tests from CLI