---
layout: post
title: Rails Dependent Associations
date: 2015-01-31T09:38:00+00:00
tags:
- Rails
- Models
- Ruby
- Programming
---
I often find that the dependent parameter gets forgotten on projects. This is easy to do if you're new to Rails. When dependent is not set, nothing terrible happens at first… but once you get further into a project you will start seeing this:

{% highlight bash %}
2.1.2 :013 > Comment.first.post.title
  Comment Load (0.6ms)  SELECT  "comments".* FROM "comments"   ORDER BY "comments"."id" ASC LIMIT 1
  Post Load (0.3ms)  SELECT  "posts".* FROM "posts"  WHERE "posts"."id" = $1 LIMIT 1  [["id", 1]]
  NoMethodError: undefined method `title' for nil:NilClass
{% endhighlight %}

The cause of the error is that the parent record no longer exists, but the foreign key still references the parent object.
To stop this from happening you can use the dependent option with `has_many` and `has_one`.

# Dependent Options

Dependent simply tells destroy what to do with child records. The available dependent options are as follows:

## Destroy

The destroy option is the most common in a Rails app, as it is the most likely scenario. For example, when you delete a post, you probably want to remove the comments as well.

{% highlight ruby %}
class Post < ActiveRecord::Base
  has_many :comments, dependent: :destroy
end
{% endhighlight %}

{% highlight ruby %}
class Comment < ActiveRecord::Base
  belongs_to :post
  after_destroy { puts "Callback Fired" }
end
{% endhighlight %}

Now when you delete the post the comments are also deleted. Any callback's that are defined will also be fired.

{% highlight bash %}
2.1.2 :004 > p.destroy
   (0.1ms)  BEGIN
  Comment Load (0.3ms)  SELECT "comments".* FROM "comments"  WHERE "comments"."post_id" = $1  [["post_id", 18]]
  SQL (0.2ms)  DELETE FROM "comments" WHERE "comments"."id" = $1  [["id", 26]]
Callback Fired
  SQL (0.2ms)  DELETE FROM "comments" WHERE "comments"."id" = $1  [["id", 27]]
Callback Fired
  SQL (0.2ms)  DELETE FROM "posts" WHERE "posts"."id" = $1  [["id", 18]]
   (1.6ms)  COMMIT
 => #<Post id: 18, title: "Post 101", body: nil, created_at: "2015-02-07 16:42:13", updated_at: "2015-02-07 16:42:13">
{% endhighlight %}

The destroy option should be a default in Rails because of the ethos of convention over configuration. Currently what happens is the child record doesn't have its foreign key nullified, which is the next option we will look at.

I don't think we could change this default now as it would cause unexpected behaviors when updating to a new Rails version.

## Nullify

Nullify is the opposite to destroy, it means the child record is not destroyed after Active Record removes the parent. Curiously, not that many developers seem to know that this option exist.

{% highlight ruby %}
class Post < ActiveRecord::Base
  has_many :comments, dependent: :nullify
end
{% endhighlight %}

Now when you delete the post the comment is not deleted. Instead the foreign key is set to Null.

{% highlight bash %}
.1.2 :003 > p.destroy
  (0.2ms)  BEGIN
 Comment Load (0.5ms)  SELECT "comments".* FROM "comments"  WHERE "comments"."post_id" = $1  [["post_id", 3]]
 SQL (0.3ms)  DELETE FROM "comments" WHERE "comments"."id" = $1  [["id", 3]]
 SQL (0.2ms)  DELETE FROM "posts" WHERE "posts"."id" = $1  [["id", 3]]
  (1.7ms)  COMMIT
=> #<Post id: 3, title: "Post 101", body: nil, created_at: "2015-01-31 13:01:43", updated_at: "2015-01-31 13:01:43">
{% endhighlight %}

This is better than leaving the foreign key set but will still cause an error if you try to access the post.

{% highlight bash %}
2.1.2 :006 > Comment.find(4).post.title
  Comment Load (0.5ms)  SELECT  "comments".* FROM "comments"  WHERE "comments"."id" = $1 LIMIT 1  [["id", 4]]
NoMethodError: undefined method `title' for nil:NilClass
{% endhighlight %}

## Delete All

The delete all option is the same as destroy but no callbacks will be executed.

{% highlight ruby %}
class Comment < ActiveRecord::Base
  belongs_to :post
  after_destroy { puts "Callback Fired" }
end
{% endhighlight %}

{% highlight ruby %}
class Post < ActiveRecord::Base
  has_many :comments, dependent: :delete_all
end
{% endhighlight %}

{% highlight bash %}
2.1.2 :011 > p.destroy
   (0.2ms)  BEGIN
  SQL (0.3ms)  DELETE FROM "comments" WHERE "comments"."post_id" = $1  [["post_id", 11]]
  SQL (0.3ms)  DELETE FROM "posts" WHERE "posts"."id" = $1  [["id", 11]]
   (1.4ms)  COMMIT
 => #<Post id: 11, title: "Post 101", body: nil, created_at: "2015-02-04 08:48:18", updated_at: "2015-02-04 08:48:18">
{% endhighlight %}

## Restrict with Exception

This is an option that I have to admit I’ve never seen or used before writing this article. Restrict with exception raises an exception when destroy is called on a parent record with children. The parent will only be deleted if the parent doesn't have associated children.

I can imagine it to be useful if you wanted to protect against posts with comments being deleted.

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

## Restrict with Error

The last option is restrict with error which sets an error on the parent record if the parent has child records associated.

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

Active Record has added an error to the errors attribute in the same way as a validation error would be added.
I think this would work well with validations as you can  display this error to the admin trying to delete the post.
Restrict with error could be a replacement to doing a custom validation rule in less code. The downside you would lose the if/unless functionality available in validation.

I know that the dependent parameter is not the most exciting thing to read about but knowing the less known features in a framework can save you time when working on your next amazing feature.

Alex
