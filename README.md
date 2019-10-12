# Homework 2: Building a Blog in Rails

Regular credit is due **11:59PM, Wednesday, Oct.23rd**.
Extra credit is due on **the last day of class**.

Late day policy: You can use the late days you earned in previous assignments on this homework.
Collaboration Policy: You can collaborate with anyone in any form on any part of the homework, but you must write your own code.

## 1. Introduction

One of the contributors to Rails' explosive growth was a talk given by the creator, David Heinemeier Hansson, who shows [how to create a blog in less than 15 minutes](https://www.youtube.com/watch?v=Gzj723LkRJY) (viewing is optional). This was completely novel at the time, and shows how powerful Rails is and how quickly you can get an application up and running.

With just the few lectures we have had together, we will build a blog of our own from scratch -- going from the starting a new rails project all the way to having your blog deployed to the world wide web!

## 2. Summary and Overview

You will create a beautiful and useful personal blog. The required tasks can be broken down into **4 parts**, and will be graded from **2 aspects**.

### 2.1 - Tasks: 4 Parts
**Homepage:** A personal blog is *first*, a personal website. Your first task is to use rails to create a home page for your blog. This page should contain your personal information. Take a look at the personal websites of your favorite professors' ([eg1](https://www.cis.upenn.edu/~ahae/), [eg2](https://www.cis.upenn.edu/~nenkova/), [eg3](https://www.cis.upenn.edu/~stevez/)...), this is the kind of homepage you should aim to build. Additionally, this page should also contain a link to the page that contain all your blog posts. 

**CRUD and RESTful Routes:** Blogs are the simplest type of CRUD applications where the model is a `post`. Your second task is to implement the routes, controller methods, and views for creating, reading, updating, and deleting blog posts. Recall that these routes and functionalities are defined by the 7 RESTful routes. In other words, your job is (literally..) just to implement these RESTful routes. When you are done with the functionalities, make sure you add links between pages to make your blog a coherent application interface. At the end of this part, your application should look like this: https://cis196-hw2-linked.herokuapp.com/

**Styling Your Blog:** You should aim to make your personal blog and homepage beautiful and reflective of your personality and taste. With the help of BootStrap, you can easily do the styling by tweaking your HTML and CSS. Here is a good example of a good-looking personal blog: http://www.adhamdannaway.com/. Yours need not to be this flashy, but you should at least build out something like this: https://cis196-hw2-styled.herokuapp.com/

**Deploy:** You are expected to deploy your blog to the open web. We suggest using heroku, but you can also try DigitalOcean or AWS.

**BONUS - Extra Credits:** The above are regular credit work. I listed a  bunch of extra-credit materials in the last part of this writeup.

### 2.2 - Grading: 2 Aspects

The TAs will grade your work from two perspectives: **functionality** and **aesthetics**:
**Functionality** will be 70% of the grade:
* The root path of your blog should be a homepage that displays information about yourself (15%)
* All 7 RESTful routes are correctly implemented, are the CRUD functionality for `posts` work as expected. (35%)
* All links are correctly implemented to make a coherent web application. (10%)
* Your blog is publicly accessible on the internet (10%)

**Aesthetics** will be 30% of the grade:
* All your pages have a navigation bar. (6%)
* The `posts` in the `index` and `show` pages are properly styled. (6%)
* Bootstrap `container` and grid system are used to layout content.  (6%)
* Custom CSS are used to style fonts, headers, etc... (6%)
* Overall the blog does not look crude. (6%)

### 2.3 - Please start early
Some parts of this project (RESTful routes, Deployment...) can be very time intensive to debug. 
Note that in we are not providing any starter code (except `_form.html.erb`) for this assignment. 
You will build everything using Rails from the ground up!

## 3. Starting up and Implementing Your Home Page

### 3.1 - Starting a new Project 
Start off your project by creating a new Rails application named `Blog` with `rails new Blog --db=postgresql`.
After all installations are done, you can `cd` into the project and run `rails s -b 0.0.0.0`.
Since your local machine forwards all requests at port 3000 to the virtual machine, if you visit `localhost:3000` in your browser, you will be greeted with the Rails welcome page. 
![Screen Shot 2019-10-11 at 6.32.36 PM](https://i.imgur.com/i8Ns5nL.png)


Well done!... but this isn't a blog or a website. 
So next up, let's first create a home page by defining a route, a controller method, and a view.

#### 3.2 - Your Home Page

Recall from lecture, that in order to create a dynamic page in Rails, you need to define 3 things: 
1. Define a `GET` request route in `config/routes.rb` that points to a controller method.
    * The definition of a route should be just one line, which consists of the HTTP method, the path, and the controller method it points to.
    * If you are not familiar with this. Either review the lecture slides / recordings, or [read this section in the rails guide](https://guides.rubyonrails.org/routing.html#connecting-urls-to-code).
    * We suggest you point the route to `"pages#home"`, as in `PagesController`, `home` method.
    
1. Create the corresponding controller file in `app/controllers/` for the route you just defined.
    * Remember the naming conventions: 
        * The controller file should be in snake case, 
        * the class should be in camel case, 
        * the method should be in lower case.
    * The controller class should extend `ApplicationController`
    * Recall that the purpose of route method is to prepare data for views later. If you don't want to provide data for your homepage view, you can keep the route method empty.
    
1. Finally create the view folder for the controller, and the `.html.erb` file for your method. 
    * The folder should be inside app/views.
    * Remember the naming conventions:
        * For a controller named `PagesController`, the view folder name should be `pages`.
        * The view file name should be the same as the method name.
    * Be sure to properly write the HTML, which should be the introduction of yourself.
    * Don't worry about the link to your blog posts yet! 
        * We'll do that later once we have that CRUD figured out.

When you are done, reload your app by restarting `rails s -b 0.0.0.0`. Then go to the path you defined in your route in your browser, you should see the page you wrote for the view, which contains your introduction. 

Before we leave this section, let's make this homepage the root page of your application, so that next time you visit `localhost:3000`, you will be greated by this page, instead of the rails default welcome page. 

In your `route.rb` file, simply change the `get '/somepath'` to `root`.

> **Sanity Check 1:**
> At this point, your blog should look something like this:
> ![Screen Shot 2019-10-11 at 10.20.27 PM](https://i.imgur.com/nHsB8mt.png)


## 4. RESTful Routes and CRUD Functionalities
The last section should have prepared you for defining routes, and hooking the controller and controller methods to the view file. However, the dynamic page we created doesn't have a database part. 

Recall that a database is a program that stores relational data in tables, and provides interface for **c**reating, **r**eading, **u**pdating, and **d**eleting records in these tables. In a CRUD application, we store objects in databases as records in the tables. Each table represents the class (or model) of these objects, and each column of the table is an attribute of those objects.

To build a CRUD application in Rails, we first create the Model class, then initialize the database with `rails db:migrate`. After that, we just need to define the routes that represent the CRUD opertions and provide the implementation in the controller file. 

Recall that the CRUD operations is represented by 7 RESTful routes. Your task here is to 
1. create the model, 
2. define the 7 RESTful routes in `routes.rb`
3. provide the implementation of the route methods in the controller class.
4. For the `GET` routes, provide the view templates.

Keep in mind the naming convention. For a model named `Model`, the controller file is `models_controller`, and the class name of that controller is `ModelsController`; the view files will thus be in the folder `app/views/models/`.

### 4.1 - Creating the `Post` Model

We'll first use the rails generator commands to create the `Post` model.
Our Post will have the following properties:
 
 * `title`, of type `string`, will be the title of each blog post. 
 * `content`, of type `text`, will contain the content (the text) of each post.

(`string` and `text` both store strings of characters, but `string` is more appropriate for things that will be shorter).

If you don't remember the command we run to generate models, review the lecture notes, it should be of form:
```ruby
rails g model ModelName prop_name:type, prop_name:type ...
```
After you hit enter, you should see these following files being created:
![Screen Shot 2019-10-11 at 10.23.35 PM](https://i.imgur.com/BpJr8ua.png)

Now run `rails db:migrate` to initialize the database with the schema defined by this model's migration files.

At this point, we don't need to worry about the models any more. Yet just for sanity check, you can run `rails c` to boot up your application   in an IRB environment. 

Then you can try running these operations to check if the CRUD functionality are all defined for your `Post` model:
![Screen Shot 2019-10-11 at 10.28.53 PM](https://i.imgur.com/Mm768Tg.png)


### 4.2 - Implement CRUD and RESTFul Routes
CRUD applications need to fulfill 4 actions from the user: Create, Read,  Update, and Delete. In web application terms, this expands to 7 RESTful routes listed here:

| HTTP Method | Path | Controller Method | Functionality / Expected Behavior  | Has a View?
| --- | --- | --- | --- | --- |
|  GET | /`model`s | `model`s#index | display a list of all `model`s | Yes |
|  GET | /`model`s/new | `model`s#new | return an HTML form for creating a new `model` | Yes |
|  POST | /`model`s | `model`s#create | create a new `model` | No, redirects to path "/`model/`:id"
|  GET | /`model`s/:id | `model`s#show | display a specific `model` whose id is `params[id]` | Yes |
|  GET | /`model`s/:id/edit | `model`s#edit | return an HTML form for editing the specific `model` whose id is `params[:id]` | Yes |
|  PATCH/PUT | /`model`s/:id | `model`s#update | updates a specific `model` whose id is `params[id]` | No, redirects to path "/`model`/:id"  |
|  DELETE | /`model`s/:id | `model`s#destroy | delete a specific `model` whose id is `params['id']` | No, redirects to path "/`model`s" |

Note that the `model` in this chart refers to a Model class named "model". For a model class named `photo`, all the literal "model"s here in this chart should be replaced by the literal "photo".

#### 4.2.1 Setting Up the Pipelines
Since we don't need to worry about the models any more at this point, the task now is just like what we did for our homepage: 1) define the routes, 2) create the controller and the methods, 3) establishing the view files. This time, the routes are described in the chart
##### Routes
Define all the 7 RESTful routes in your `routes.rb` **exactly as described** by the chart above. For example: 
the first route should be:
```ruby
get '/models', to: 'models#index'
```
and the last route should be:
```ruby
delete '/models/:id', to: 'models#destroy'
```
With a slight exception of the `GET /models/:id` route: 
instead of defining it as just:
```ruby
get '/models/:id', to: 'models#show'
```
you should define it like this:
```ruby
get '/models/:id', to: 'models#show', as: 'model'
```
Again, note that all "model" literals here should be replaced by your model name, which is `post`.

##### Controller
Given these routes, it is only natural that you create a `posts_controller.rb`, and define a `PostsController` class inside of it. 
Since, youhave 7 routes, make sure you create 7 methods with the corresponding name.

##### View
As shown in the chart, you only need 4 view files, which corresponds to 4 controller methods that have views: `index`, `show`, `new`, and `edit`.
Be sure to follow the naming conventions when you are creating the view folder and the view files. 

Additionally, drag the provided `_form.html.erb` into your view folder. This will be useful later.

#### 4.2.2 Implementing the Routes
Although we have correctly wired things up in the previous section, we haven't implemented the CRUD functionalities yet! You need to implement the 7 methods in the controller so that they behave exactly as the chart describes. 

Moreover, your view files are currently empty. Recall that the controller methods only prepare data for the views. You need to write the HTML and embedded Ruby in the View file to display the desired content. 

##### `index`
In order for the `/posts` page to display *all* the `post`s in the database, your `index` method should prepare a variable `@posts` to be an array of all the posts. 
Then in your `index.html.erb`, use embedded ruby to loop over `@posts`, and render (using `<%= ... %>`) the title and content for each post object in the array.
When you are done, boot up `rails s -b 0.0.0.0`. You should see something like this on http://localhost:3000/posts:
![Screen Shot 2019-10-11 at 10.43.57 PM](https://i.imgur.com/mwQwBCp.png)


##### `new`
Visit to `/posts/new` should see a form to make a post.
We haven't talked about `<form>`s yet so just do the following:
1. In the `new` method, write `@post = Post.new`, and that's it.
2. In the `new.html.erb`, write two lines:
    * The first line is a `<h1>` title that says 'Write a new Post'.
    * The second line is `<%= render 'form', post: @post %>`. 
    This renders the `_form.html.erb` as part of the page, and passes the empty new `@post` to the form.

When you are done, boot up `rails s -b 0.0.0.0`. You should see something like this on http://localhost:3000/posts/new:
![Screen Shot 2019-10-11 at 10.51.23 PM](https://i.imgur.com/RqFYFFB.png)

    
##### `create`
This route is different from all the routes you have seen thus far, as it is associated with the `POST` HTTP method. This means that this is not triggered by any "page visits" you do through the browser. So when is it triggered? -- when you hit the 'submit' button in the form you see just now!

What happens is that on submit of the form, the browser constructs a POST request to your Rails application. That HTTP request has all the data you filled in the form. Since rails knows that it is a form for a `post`, rails parses the form data into a hash and put this hash inside the params hash as a value with key `"post"`. You can access the hash with `params['post']` inside your controller.

Using this hash, you can instantiate a new `post` object. However, since rails need to defend against [Mass Assignment Attacks](https://rubyplus.com/articles/3281-Mass-Assignment-in-Rails-5), you need to manually permit the usage of that hash by creating a private method:
```ruby
def post_params
    ... # manually permit 
end
```
So that you can now create the new post object by:
```ruby
@post = Post.new(post_params)
```
Now you need to `save` it to the database! The `save` method returns a boolean value indicating the success of database transaction. 

Now notice that according to the chart, the `create` method has no view template. What you should enable is that:
* on successfully saving to the database, use `redirect_to` to load the `/post/:id` path that shows the page of the newly created post (see next section). 
    * Hmm, what should this `id` be?
* on failure, use `render` to rerender the `/posts/new` page.

If you are not sure how to use `render` or `redirect`, check out [this post](https://gist.github.com/jcasimir/1210155)

##### `show`
In order for the `/posts/:id` page to display the `post` with the associated `:id`, your `show` method should prepare a variable `@post` to be the post object with  with id equal to the `/:id` in the path.
Then in your `show.html.erb`, use embedded ruby to render (using `<%= ... %>`) the title and content of that post object.
When you are done, boot up `rails s -b 0.0.0.0`. You should see something like this on http://localhost:3000/posts:
![Screen Shot 2019-10-11 at 10.57.24 PM](https://i.imgur.com/BwDsleL.png)


Moreover, this time when you create fill in the form in `/posts/new` and hit submit, you should be redirected to the "show" page of that new post!

##### `edit`
Visit to `/posts/:id/new` should see a form to make edits to the post with id `/:id`.
We haven't talked about `<form>`s yet so just do the following:
1. In the `new` method, assign `@post` to the post with id equal to the `/:id` in the path, just like what you did in `show`.
2. In the `new.html.erb`, write two lines:
    * The first line is a `<h1>` title that says 'Edit Post #...'.
    * The second line is `<%= render 'form', post: @post %>`. 
    This renders the `_form.html.erb` as part of the page, and passes the empty new `@post` to the form.
    
When you are done, boot up `rails s -b 0.0.0.0`. You should see something like this on http://localhost:3000/posts/2/edit:
![Screen Shot 2019-10-11 at 11.00.16 PM](https://i.imgur.com/q4Eduy1.png)


##### `update`
This method is much like the `create` method in that it is triggered not through a "page view", but through the submission of the form in `/posts/:id/edit`. 

On form submit, the form data is again parsed into a hash and made available in `params` hash through `params['post']`. Still, in order to avoid the [mass assignment problem](https://rubyplus.com/articles/3281-Mass-Assignment-in-Rails-5), you need to access the hash through the `post_params` method you created for `create`.

You first find the the post object like you did in `show` and `edit`, then you update its information in database by:
```
@post.update(post_params)
```
This method, like `save` returns a boolean type, so for the view:
* on success, use `redirect_to` to load the `/post/:id` path that shows the page of the post.
* on failure, use `render` to rerender the `/posts/:id/edit` page.

##### `destroy`
The `destroy` method is easier to implement: you first find the post object just like you did for `show`, `edit`, and `update`, then simply call:
```ruby
@post.destroy
``` 

Then no matter if the deletion is successful, always redirect the page to `/posts` path.

But how do you test it? what triggers the browser to send a "DELETE" request? 
Recall that we can use the `<%= link_to ... >` helper to create `<a>` elements on pages that can trigger specific kinds of requests, which takes the general form:
```ruby
<%= link_to "DISPLAY_NAME", "/path/to/somewhere", method: :HTTP_METHOD %>
```
In our case, you should put the link_to tags at least in the `edit.html.erb`. But you are welcomed to put it in other places too.



> **Sanity Check 2:**
> At this point, your blog should look something like this:
> https://cis196-hw2-unstyled.herokuapp.com
> Note that since you did not add links, as you will in the next section, you need to manually type in the address of the RESTful routes. 
> For example, to see all posts, you need to visit https://cis196-hw2-unstyled.herokuapp.com/posts, 
> and to make a new post, you need to visit https://cis196-hw2-unstyled.herokuapp.com/posts/new

### 4.3 Adding Links between Pages
Although your blog has all the necessary functionalities ready, it is still not a coherent application because its pages are not wired properly. For example, right now when you are in the `/posts` page, although you see the content of each post, you don't have a link to their respective `show` page, and you don't have a link to `/posts/new` where you can create a new post. This is problematic.

Therefore, to ensure usability:
* The `/posts` page should have:
    * links to each post's detail page (`/posts/:id`)
    * links to each post's edit page (`/posts/:id/edit`)
    * a link to create new posts (`/posts/new`)
* The `/posts/new` page should have:
    * a link back to all the posts(`/posts/:id`)
* Each `/posts/:id` page should have:
    * a link back to all the posts (`/posts/:id`)
    * a link to this post's edit page (`/posts/:id/edit`)
* Each `/posts/:id/edit` page should have:
    * a link back to the post (`/posts/:id`)
    * a link to delete this post
* All pages should have a link to the homepage you created in part 3.
* The homepage should have a link to `/posts`.

> **Sanity Check 3:**
> At this point, your blog should look something like this:
> https://cis196-hw2-linked.herokuapp.com/

        
## 5. Making the Blog Look Good

Great, our application should be working now! You should be able to read your posts, create new posts, edit them, and delete! With our functionality done, let's go on to the fun part -- making our application look good! 

This is where you will utilize HTML, CSS, and BootStrap. If you need references for them:
* For HTML, see [W3School's documentation for HTML](https://www.w3schools.com/html/default.asp)
* For CSS, see [W3School's documentation for CSS](https://www.w3schools.com/css/default.asp)
* For Bootstrap, either use [Bootstrap's documentation](https://getbootstrap.com/docs/4.3/getting-started/introduction/) or [W3School's documentation for BootStrap](https://www.w3schools.com/bootstrap4/default.asp).

If you need video tutorials for any of these topics: visit [Scrimba](https://scrimba.com/). It has tutorials for all of them.

You should installing Bootstrap through the [BootStrap 4 gem for Rails](https://github.com/twbs/bootstrap-rubygem).
Following its steps, you will need to:
1. Modify your Gemfile, and run `bundle install`
2. Rename `application.css` to `application.scss` in `apps/assets/styles/`, and call the `@import` keyword.

To properly style your blog, the minimum requirement is:
1. to wrap the page content inside a `container` on every page.
2. to have a `navbar` for every page.
    * on which there is a "Home" link that links to your homepage.
    * and a "Blog" link that links to `/posts`.
3. Use the Bootstrap grid system to organize the content on your homepage, and/or style your `/posts` page.
4. Add paddings, borders, margins, or modify color, font-size, background colors as you see fit.


> **Sanity Check 4:**
> At this point, your blog should look something like this:
> https://cis196-hw2-styled.herokuapp.com/

## 6. Deployment

We will use Heroku in this class for deployment. You can use any deployment service you prefer, but the 196 team strongly recommends Heroku and can only provide technical support for this. 

To deploy with Heroku, follow the guide [here](https://devcenter.heroku.com/articles/getting-started-with-rails5#deploy-your-application-to-heroku). Basically, you need to do the following steps.
1. Sign up an account at heroku.com
2. Install the heroku CLI on your virtual machine with
```
sudo snap install --classic heroku
```
1. Inside your project folder, run command `heroku login --interactive` and provide your heroku account email and password to authenticate. You should see something like this:
![Screen Shot 2019-10-12 at 12.23.00 AM](https://i.imgur.com/KH7H0q8.png)
1. In your heroku dashboard, create a new application and hit "Create app"
![Screen Shot 2019-10-12 at 12.23.37 AM](https://i.imgur.com/67ivNFC.png)
1. Follow the steps in the next page all the way till you finish running `git push heroku master`
2. After the installation is done, run `heroku run rails db:migrate` to initialize your database at heroku.

Then you are all set!



## 7. Extra Credits
Even with the few tools we've learned, we've made a very powerful application and showcased some of the things Rails can offer. Of course, there's many things we can improve on this blog. For example: having users sign in and comment, making sure that only you can make blog posts (as it stands, anyone can make a blog post). You will be able to do this and much more in the coming weeks. 

Here are a few extra credit ideas you can try out.
* Multiple Pages (Easy, covered)
* User Authentication / Admin Privilege (Medium, to be discussed in lectures)
* Add Read/Like/Dislike Counts (Medium, to be discussed in lectures)
* Form Validation (Easy, to be discussed in lectures)
* Rich Text Editing (Easy, will not be discussed in lectures)
* Markdown Support (Hard, will not be discussed in lecture)
* Adding a Domain (Medium, will not be discussed in lecture)
* Add Categories to Posts (Medium, to be discussed in lecture)
* Enable Comments to Posts (Medium, to be discussed in lecture)
* Enable Nested Comments to Posts (Hard, to be discussed in lecture)
* Add JS/CSS (Animation/User Interaction) to Pages
* Use React/Vue to Replace ERB (Hard, to be partially discussed)
* ... (submit your own ideas)

Each "Easy" is worth 5% more points. Each "Easy" feature not listed here is worth 6% more points.
Each "Medium" is worth 10% more points. Each "Medium" feature not listed here is worth 12% more points.
Each "Hard" is worth 15% more points. Each "Hard" feature not listed here is worth 18% more points.

You can ask about the implementation of all extra credit features in office hours or on CampusWire public channels / public questions.

## 8. Submission

1. Commit your changes and push your changes to Gitlab.
2. Make a zip for this folder, and upload it on Canvas.
3. In your canvas submission, make sure you put the **link** of your deployed blog in submission comment.

