---
layout: post
title: The bloated Ruby block
date: 2015-04-01T09:38:00+00:00
tags:
- Ruby
- Programming
---

I am currently spending a month in Spain climbing before I start looking for a new position after closing Serious Fox and in
the process decided to setup up my old MacBook Pro for the journey, as it is resilient to most damage. While I was moving my files
over I found a draft of this blog post.

I remember blocks being confusing when I first started learning Ruby, they seemed alien when compared to my PHP code.
After a while I got the hang of them and soon my code was littered with these nuggets:

{% highlight ruby %}
Rack::Rewrite do
  r301 "/wiki/alex", "/alex"
  rewrite "/docs/go", "/documentation/go"
end
{% endhighlight %}

After a while thou they started looking like some of my university coursework, lots of code which was hard to read and make changes too.

{% highlight ruby %}
Rack::Rewrite do
  r301      "/wiki/alex",   "/alex"
  r301      "/wiki/steve",   "/steve"
  r301      "/wiki/billy",   "/billy"
  r301      "/wiki/jake",   "/jake"

  rewrite %r{(.*)}, lambda do |match, rack_env|
    if match[0] == "/docs/hipster-js"
      "https://www.destroyallsoftware.com/talks/wat"
    else
      return match[1]
    end
  end

   # This would go on and on...
end
{% endhighlight %}

The solution is to use another of Ruby's more confussing features.. Lambda's.

Lambda's enable separating the different pieces of logic which make up your block.

{% highlight ruby %}
Rack::Rewrite do |t|
  # The "*" symbol simply flattens the array into the
  # separate parameters the method expects.
  wiki_permanant_redirects.each { |args| r301 *args }

  rewrite %r{(.*)}, hipster_redirect
end

def hipster_redirect
  lambda do |match, rack_env|
    if match[0] == "/docs/hipster-js"
      "https://www.destroyallsoftware.com/talks/wat"
    else
      return match[1]
    end
  end
end

def wiki_permanant_redirects
  wiki_users = ["alex", "steve", "billy", "jake"]

  wiki_users.collect do |user|
    ["/wiki/#{user}", "/#{user}"]
  end
end
{% endhighlight %}

Show Tyre example of using lambdas

