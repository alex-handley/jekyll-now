---
layout: post
title: "Rails ActiveRecord dependent options"
date: 2015-01-31T09:38:00+00:00
tags:
- Rails
- Models
- Ruby
- Programming
---

This is a test line

One of the common issues I see when working on Rails app’s that were created by developers less experienced was the Rails framework is missing the dependent option from a has_many/has_one.
This causes this common error.

{% highlight bash %}
2.1.2 :013 > Comment.first.post.title
  Comment Load (0.6ms)  SELECT  "comments".* FROM "comments"   ORDER BY "comments"."id" ASC LIMIT 1
	  Post Load (0.3ms)  SELECT  "posts".* FROM "posts"  WHERE "posts"."id" = $1 LIMIT 1  [["id", 1]]
		NoMethodError: undefined method `title' for nil:NilClass
{% endhighlight %}

This is because there parent record no longer exists but the foreign key still references the parent object.

I think it is an oversight that this is not configured by default to be the most common option “dependent: :destroy”.
As Rails ethos is convention over configuration.

The available dependent options are as follows:

# Destroy

This is the option that most Rails app use as it covers the expected outcome, if you delete a post for example you probably want to delete the comments for example.

{% highlight ruby %}
class Post < ActiveRecord::Base
	has_many :comments, dependent: :destroy
end
{% endhighlight %}

Now when you delete the post the comment is also deleted. This the best outcome less you need the comment to be kept which is our next option.

{% highlight bash %}
2.1.2 :003 > p.destroy
   (0.2ms)  BEGIN
  Comment Load (0.5ms)  SELECT "comments".* FROM "comments"  WHERE "comments"."post_id" = $1  [["post_id", 3]]
  SQL (0.3ms)  DELETE FROM "comments" WHERE "comments"."id" = $1  [["id", 3]]
  SQL (0.2ms)  DELETE FROM "posts" WHERE "posts"."id" = $1  [["id", 3]]
   (1.7ms)  COMMIT
 => #<Post id: 3, title: "Post 101", body: nil, created_at: "2015-01-31 13:01:43", updated_at: "2015-01-31 13:01:43">
{% endhighlight %}

# Nullify

Alot of developers don't know this option exist.

{% highlight ruby %}
class Post < ActiveRecord::Base
	has_many :comments, dependent: :nullify
end
{% endhighlight %}

Now when you delete the post the comment is not deleted. Instead the foreign key is set to Null.

{% highlight bash %}
2.1.2 :003 > p.destroy
   (0.2ms)  BEGIN
  SQL (0.4ms)  UPDATE "comments" SET "post_id" = NULL WHERE "comments"."post_id" = $1  [["post_id", 4]]
  SQL (0.2ms)  DELETE FROM "posts" WHERE "posts"."id" = $1  [["id", 4]]
   (2.1ms)  COMMIT
 => #<Post id: 4, title: "Post 101", body: nil, created_at: "2015-01-31 13:07:21", updated_at: "2015-01-31 13:07:21">
2.1.2 :004 > Comment.find(4)
  Comment Load (0.3ms)  SELECT  "comments".* FROM "comments"  WHERE "comments"."id" = $1 LIMIT 1  [["id", 4]]
 => #<Comment id: 4, body: "My comment", post_id: nil, created_at: "2015-01-31 13:07:24", updated_at: "2015-01-31 13:07:24">
{% endhighlight %}

This is better then the default of leaving the foreign key set but this will still cause an error if you try and access the post.

{% highlight bash %}
2.1.2 :006 > Comment.find(4).post.title
  Comment Load (0.5ms)  SELECT  "comments".* FROM "comments"  WHERE "comments"."id" = $1 LIMIT 1  [["id", 4]]
NoMethodError: undefined method `title' for nil:NilClass
{% endhighlight %}

# Restrict with Exception

I have to admit I hadn't heard of this option but I can imagine it to be useful if you wanted to protect against posts with comments being deleted.

{% highlight ruby %}
class Post < ActiveRecord::Base
	has_many :comments, dependent: :restrict_with_exception
end
{% endhighlight %}


{% highlight bash %}
2.1.2 :003 > p.destroy
   (0.2ms)  BEGIN
   (2.3ms)  ROLLBACK
ActiveRecord::DeleteRestrictionError: Cannot delete record because of dependent comments
{% endhighlight %}

As you can see this raises an ActiveRecord::DeleteRestrictionError exception if a comment exists.

# Restrict with Error

The last option is to set an error on the post object, which is better then restrict_with_exception if you are plaaning to use this in a scenario when an admin is trying to delete a post.

{% highlight ruby %}
class Post < ActiveRecord::Base
	has_many :comments, dependent: :restrict_with_error
end
{% endhighlight %}


{% highlight bash %}
2.1.2 :003 > p.destroy
   (0.2ms)  BEGIN
   (0.2ms)  ROLLBACK
 => false
2.1.2 :004 > p.errors
 => #<ActiveModel::Errors:0x000001016a9828 @base=#<Post id: 6, title: "Post 101", body: nil, created_at: "2015-01-31 13:19:36", updated_at: "2015-01-31 13:19:36">, @messages={:base=>["Cannot delete record because dependent comments exist"]}>
{% endhighlight %}

ActiveRecord has added an error to the errors attribute in the same way as a validation error would be added, this set you up perfectly to display this error to the admin trying to delete the post.
I think restrict_with_error could be a replacement to doing a custom validation rule in less code but you would lose the if/unless functionality available in valiations.

I know that the dependent option is not the most exciting thing to read about but knowing the less know option in a framework can save you time when working on your next amazing feature.

Alex
