---
-layout: post
-title: 'Rails ProTip: Stop generating empty files you're not gonna use!'
-date: '2014-01-20T09:38:00+00:00'
-tags:
  - rails protip
---

I regularly work on projects where there are default pending specs and helpers/assets that are empty. This is bad for two reasons: firstly RSpec and the Asset pipeline still check/load these files which slows things down, and secondly they make the project harder to navigate.

I recommend adding this too your application.rb today!

{% highlight ruby %}config.generators do |g|
  g.test_framework :rspec, views: false, routes: false
  g.helper_specs false
  g.stylesheets = false
  g.javascripts = false
  g.helper = false
end
{% endhighlight %}
