---
layout: post
title: The bloated Ruby block
date: 2015-04-01T09:38:00+00:00
tags:
- Ruby
- Programming
---

I am currently spending a month climbing in Spain before I start looking for a new job and in
the process decided to setup up my old MacBook Pro for the journey.
While I was moving my files over I found a draft of this blog post.

I remember blocks being confusing when I first started learning Ruby, they seemed alien when compared to my PHP code.
After a while I got the hang of them and soon my code was littered with these nuggets:

{% highlight ruby %}
Rack::Rewrite do
  r301 "/wiki/alex", "/alex"
  rewrite "/docs/go", "/documentation/go"
end
{% endhighlight %}

After a while they started looking like some of my university coursework, lots of code which was hard to read and make changes too.

{% highlight ruby %}
Rack::Rewrite do
  r301 "/wiki/alex", "/alex"
  r301 "/wiki/steve", "/steve"
  r301 "/wiki/billy", "/billy"
  r301 "/wiki/jake", "/jake"

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

One way to improve this is to pull out the logic into functions which helps to give context to the code.
i.e what is happening in each part, this is helpful for someone reading the code for the first time.

{% highlight ruby %}
Rack::Rewrite do |t|
  # The "*" symbol simply flattens the array into the
  # separate parameters the method expects.
  wiki_permanant_redirects.each { |args| r301 *args }

  rewrite %r{(.*)}, redirect_hipster_js_to_wat_talk
end

def redirect_hipster_js_to_wat_talk
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

The functions could be implemented as classes which will enable you to easily Unit test.

Another solution using a loop is to collect the parts of the block as Lambda's and then pass them to the block.
This solution works well when there are lots of pieces of logic such as partial Tire queries when using ElasticSearch.

{% highlight ruby %}
tags = ["ruby", "java", "python"]
partial_queries = tags.collect do |tag|
  lambda do |boolean|
    boolean "tags:#{tag}"
  end
end

Tire.search("articles") do
  query do
    boolean do
      partial_queries.each do |partial_query|
        boolean &partial_query
      end
    end
  end
end
{% endhighlight %}

** I am not able to test the above code as the Internet here isn't good enough to download ElasticSearch.

I like this way of structuring blocks when I know it is going to grow quickly. e.g. when building a complex search.

Because you can continue to grow out the number of query parameters and isolate complex logic to enable easy testing.
As with the first example, classes can be used to group similar logic such as tags and word searches within articles in this example.

At the same time if the search is basic this would be massively overkill and would make the code hard to read.

Hope this helps someone with a bloated block problem :)

![View from Siurana Campsite](/images/post_images/blocks_office.png)

My office view.
