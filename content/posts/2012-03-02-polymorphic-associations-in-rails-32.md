---
title: "Polymorphic Associations in Rails 3.2"
date: 2012-03-02
draft: false
toc: false
images:
tags:
  - programming
  - ruby-on-rails
---

I have recently transferred to a new job. I am now working as a Ruby on Rails developer for TwitMusic. Before I go on about promoting my new employer (which I won't do for the sake of this article), I would like to point out that I am being trained for the position before I get involved with the production code. For the first part of my training, my employer gave me a set of problems about Ruby on Rails that I have to solve on my own. One of the most challenging problems I encountered was about polymorphic associations.

In Rails, Polymorphic Associations allow an ActiveRecord object to be associated with multiple ActiveRecord objects. A perfect example for that would be comments in a social network like Facebook. You can comment on anything on Facebook like photos, videos, links, and status updates. It would be impractical if you were to create a comment model (photos_comments, videos_comments, links_comments) for every other model in the application. Rails eliminates that problem and makes things easier for us by allowing polymorphic associations.

For this article, I have 2 independent models: Foo and Bar:

```ruby
class CreateFoos < ActiveRecord::Migration
  def change
    create_table :foos do |t|
      t.string :title
      t.text :content

      t.timestamps
    end
  end
end

class CreateBars < ActiveRecord::Migration
  def change
    create_table :bars do |t|
      t.string :name
      t.text :content

      t.timestamps
    end
  end
end
```

First of all, let's create the Comment model. Normally, if we were just create a comments model for a single model like Post, we would have an integer field called post_id to store the foreign key. Since we have multiple models, we need to have it reference to something more abstract. As I understand right now, Rails allows us to make an interface for polymorphic associations. Also, all of the models we're adding comments to have one thing in common: they allow comments. With that in mind, we'll create a foreign key field called `commentable_id`. The comment also needs to know which model it's associated with, so we'll create another field called `commentable_type`.

```shell
rails generate model Comment content:text commentable_id:integer commentable_type:string
```

Once that's done, we'll need to associate the comment model with the other models.

```ruby
class Comment < ActiveRecord::Base
  belongs_to :commentable, :polymorphic => true
end
```

Instead of making it belong to a specific model, we made it belong to `commentable`, which will be the interface for the other models to associate with.

We establish the association by making it in the other models as well:

```ruby
class Foo < ActiveRecord::Base
  has_many :comments, :as => :commentable
end

class Bar < ActiveRecord::Base
  has_many :comments, :as => :commentable
end
```

Now that the relationships are set up, it's time to create the controller for our Comment model:

```shell
rails generate controller Comments
```

Afterwards, we need to modify `routes.rb` to reflect the association.

```ruby
# routes.rb

resources :foos do
  resources :comments
end

resources :bars do
  resources :comments
end
```

What we want to do is to have all the comments displayed if we go to [/foos/1/comments](http://localhost:3000/foos/1/comments). Here's where it gets tricky: we can't do the usual with the index action for the comments controller. If we do our usual:

```ruby
def index
  @comments = Comment.all
end
```

All the comments for every model will be displayed. We need to find a way to only display the comments under the model that we are referring to.

As seen in the [RailsCasts episode about Polymorphic Associations](http://railscasts.com/episodes/154-polymorphic-association), we have to create a method to solve our problem:

```ruby
def find_commentable
  params.each do |name, value|
    if name =~ /(.+)_id$/
      return $1.classify.constantize.find(value)
    end
  end
  nil
end
```

The RailsCasts episode explains this method pretty well, so I won't go over this. Now we can use this method in the index action to get the corresponding comments.

```ruby
def index
  @commentable = find_commentable
  @comments = @commentable.comments
end
```

Of course, we'll want to make a form to make use of our new functionality, so users can add their own comments.

```ruby
# /app/views/comments/index.html.erb

<h1>Comments</h1>

<ul id="comments">
  <% @comments.each do |comment| %>
    <li><%= comment.content %></li>
  <% end %>
</ul>

<h2>New Comment</h2>
<%= form_for [@commentable, Comment.new] do |f| %>
  <div class="field">
    <%= f.label :content %><br />
    <%= f.text_area :content, :rows => 5 %>
  </div>
  <div class="actions">
    <%= submit_tag "Add comment" %>
  </div>
<% end %>
```

When the form is submitted, it will call the create action of the CommentsController, so let's create that.

```ruby
def create
  @commentable = find_commentable
  @comment = @commentable.comments.build(params[:comment])
  if @comment.save
    flash[:notice] = "Successfully saved comment."
    redirect_to :id => nil
  else
    render :action => 'new'
  end
end
```

We call `find_commentable` again to get the corresponding model, so we can make sure that the comment saved will be pointing to the right model. Another thing to note is that we are redirecting to `:id => nil`, to make it redirect back to the current page.
