@page creating Creating Cookbook
@parent getstarted 0

We're going to create a basic cookbook application that
lets us create, and delete recipes. It will look like:

@image tutorials/getstarted/Cookbook.png

JavaScriptMVC uses [steal.generate generator scripts] to
assist you in setting up your application's
files and folders. They make everything you need to fall
into the pit of success!

## Generating an Application

To create your application, open a console window and 
navigate to your public directory. Run:

    > js jmvc\generate\app cookbook

This script creates an application folder and
files. Here's what each file does:


    cookbook/                // folder for your app
      cookbook.css           // css for your app
      cookbook.html          // a page for your app
      cookbook.js            // app file, loads other files
      docs/                  // documentation
      fixtures/              // simulated ajax responses
      funcunit.html          // functional test page
      models/                // model & data layers
      qunit.html             // unit test page
      scripts/               // command line scripts
        build.html           // html for build script
        build.js             // build script
        clean.js             // code cleaning / linting
        crawl.js             // generate search content
        docs.js              // create documentation
      test/                    
        funcunit             // functional tests
          cookbook_test.js   // functional test
          funcunit.js        // loads functional tests
        qunit/               // unit tests
          cookbook_test.js   // unit test
          qunit.js           // loads unit tests



We'll use cookbook.html for our application. If 
you need to make another page for your app you 
can generate it:

@codestart text
> js jmvc\generate\page cookbook index.html
Generating ... index.html
@codeend

Or you add the steal script to an existing page 
page followed by `?cookbook` like:

    <script type='text/javascript'
            src='../path/to/steal/steal.js?cookbook'>
    </script>

If you open cookbook/cookbook.html, you'll see a
JavaScriptMVC welcome screen.  

@image tutorials/getstarted/Welcome.png

Open `cookbook/cookbook.html` and you will find:

    <script type='text/javascript'
          src='../steal/steal.js?cookbook'>
    </script>

This line loads [steal] and tells steal to 
load `cookbook/cookbook.js`. `cookbook/cookbook.js` is
your application file.  Open it and you will find:

    steal(
	    './cookbook.css', 			// application CSS file
	    './models/models.js',		// steals all your models
	    './fixtures/fixtures.js',	// sets up fixtures for your models
	    function(){					// configure your application
		
	})

The application file loads and configures your applications resources.  Currently,
it's loading the app's css file, models and fixtures (there are no fixtures or models yet).

Now it's time to make some widgets, models, and fixtures that allow us to create and delete
recipes!  

## Scaffolding Recipes

We'll use the scaffold generator to quickly create:

  - A Recipe model for CRUDing recipes on the server
  - A Fixture for simulating a recipe service
  - A widget for creating recipes
  - A widget for listing and deleting recipes

To scaffold recipes run the following in the command-line console:

    > js jmvc\generate\scaffold Cookbook.Models.Recipe

Here's what each part does:

__recipe.js__

Creates a recipe [can.Model model] that is used
to create, retrieve, updated, and delete 
recipes on the server.

__recipe_test.js__

Tests the recipe model.

__fixtures.js__

The generator added code to simulate the Recipe Model's Ajax
requests (You might not have a Recipe service).

__recipe/create__

This folder contains the code, demo page, and tests for a
widget that creates Recipes.

__recipe/list__

This folder contains the code, demo page, and tests for a
widget that lists recipes.

__(steal added)__

The generator will also list files that 
say "(steal added)". For example:

@codestart text
cookbook/models/models.js (steal added)
@codeend    

The "(steal added)" means the generator is 
adding a steal call to load
a generated file for you.  For example, 
`cookbook/models/models.js` now steals your
`recipe.js` model like:

    steal('./recipe.js')

## Page Setup

After the generator runs, your application file (`cookbook.js`)
looks like:

    steal(
      './cookbook.css', // application CSS file
      './models/models.js',	// steals all your models
      './fixtures/fixtures.js',	// sets up fixtures for your models
      'cookbook/recipe/create',
      'cookbook/recipe/list',
    function(){
      // set your application up
      new Cookbook.Recipe.List('#recipes');
      new Cookbook.Recipe.Create('#create');
    })

You'll notice that it now loads `cookbook/create`
and `cookbook/list` and then tries to add these widgets to the
`#recipes` and `#create` elements.  

However, `#recipes` and `#create` elements do not 
exist.  All we have to do now is add them.  Open __cookbook/cookbook.html__
and add a `#recipes` __ul__ and a `#create` __form__
so it looks like:

    <!DOCTYPE HTML>
    <html lang="en">
      <head>
        <title>cookbook</title>
      </head>
      <body>
          <h1>Welcome to JavaScriptMVC!</h1>
          <ul id='recipes'></ul>
          <form id='create' action=''></form>
        <script type='text/javascript' 
                  src='../steal/steal.js?cookbook'>	 
            </script>
      </body>
    </html>

## Run Cookbook


That's it. You've created a simple Cookbook 
application. Open cookbook/cookbook.html in a browser. 

@image tutorials/getstarted/Cookbook.png

<div style='background-color: #dddddd;  margin: 20px 0px;padding: 20px'>
<p>
<b style='color:red'>NOTICE:</b> 
If you are having problems and 
using Chrome from the filesystem, it's because Chrome has an
[http://code.google.com/p/chromium/issues/detail?id=47416 insanely restrictive AJAX policies on the filesystem].
</p>
<p>
Essentially, Chrome does not allow AJAX requests to
files outside the html page's folder.  JavaScriptMVC
organizes your files into separate folders.
</p>
<p>
To fix this, just run JavaScriptMVC from a web server.
Or, you can use another browser.  Or you can add
`--allow-file-access-from-files` to Chrome's start script.
</p>
<p>
If you're annoyed like we are, 
[http://code.google.com/p/chromium/issues/detail?id=47416 star the issue]
and let 
google know you'd like Chrome to work on the filesystem!
</p>
</div>


Continue to [testing Testing Cookbook] or continue to read how
this code works.

## How it Works

The Cookbook application can be broken into 5 parts:

  - The Recipe Model
  - The Recipe Fixture
  - The Recipe Create control
  - The Recipe List control
  - The Cookbook application that puts it all together

### The Recipe Model and Fixture

`cookbook/models/recipe.js` looks like:

    steal('can/model', function(){
      /**
       * @class Cookbook.Models.Recipe
       * @parent index
       * @inherits can.Model
       * Wraps backend recipe services.
       */
      can.Model('Cookbook.Models.Recipe',
      /* @Static */
      {
        findAll: "GET /recipes",
          findOne : "GET /recipes/{id}",
          create : "POST /recipes",
          update : "PUT /recipes/{id}",
          destroy : "DELETE /recipes/{id}"
      },
      /* @Prototype */
      {});
    })

This loads [jQuery.Model can.Model] and uses it to create a 
`Cookbook.Models.Recipe` class.  This class lets us
create, retrieve, update, and delete models programmatically like:

__create__

    // create a recipe instance
    var recipe = new Cookbook.Models.Recipe({
      name: 'Hot Dog',
      description: 'nuke dog, put in bun'
     })
  
    // call save to create on the server
    recipe.save()

__retrieve__

    // get recipes from the server
    Cookbook.Models.Recipe.findAll({}).done(function(recipes){
      // do something with recipes
    })

__update__

    // update the properties of a created recipe
    recipe.attrs({
      name: 'Bratwurst',
      description: 'nuke bratwurst, put in bun'
    });
  
    // call save to send updates to the server
    recipe.save()

__delete__

    // call destroy
    recipe.destroy()

Of course, we don't have a server to make requests to. This is
where fixtures come in.

### The Recipe Fixture

[can.fixture Fixtures] intercept Ajax requests and
simulate the response. They are a great tool that enables
you to start work on the front end without a ready server.

Open `cookbook/fixtures/fixtures.js` and you will this:

    steal("can/util/fixture", function(){
      var store = can.fixture.make(5, function(i, recipe){
        return {
          name: "recipe "+i,
          description: "Model " + i
        }
      });

      can.fixture('GET /recipes', store.findAll);
      can.fixture('GET /recipes/{id}', store.findOne);
      can.fixture('POST /recipes', store.create);
      can.fixture('PUT /recipes/{id}', store.update);
      can.fixture('DELETE /recipes/{id}', store.destroy);
    })

The scaffold generator added this to simulate a server 
with 5 recipes.  Read more about how this works on
[can.fixture.make make's documentation page].

### The Recipe Create Control

Open `cookbook/recipe/create/create.html` in your 
browser.  This page demos the Cookbook.Recipe.Create control and
lets you create recipes.  It lets us work on Cookbook.Recipe.Create
independent of the rest of the application.

Open `cookbook/recipe/create/create.js` to
see the Cookbook.Recipe.Create control's code:

    steal('can', 'can/control/view', 'cookbook/models', './views/init.ejs', function() {
      /**
       * @class Cookbook.Recipe.Create
       * @parent index
       * @inherits jQuery.Controller
       * Creates recipes
       */
      can.Control('Cookbook.Recipe.Create',
      /** @Prototype */
      {
        init : function(){
          this.element.html(this.view());
        },
        submit : function(el, ev){
          ev.preventDefault();
          var el = this.element;
          el.find('[type=submit]').val('Creating...')
          new Cookbook.Models.Recipe(el.formParams()).save().done(function() {
            el.find('[type=submit]').val('Create');
            el[0].reset()
          });
        }
      })
    });

This code uses [steal] to load dependencies and then creates a 
`Cookbook.Recipe.Create` controller.  This creates
a `cookbook_recipe_create` jQuery helper function that
can be called on a form element like:

    new Cookbook.Recipe.Create('form#create')

When the jQuery plugin is called, the controller's `init`
method is called and runs 

    this.element.html(this.view());
    
This code renders the template at `cookbook/recipe/create/views/init.ejs`
into the controller's [can.Control.prototype.element element].

When the jQuery plugin is called controller also binds event handlers on the
controller's element.  In this case, it listens for "submit" events on the element.

When a submit event happens, it updates the submit button's text, then creates
a new recipe.

### The Recipe List Control

Open `cookbook/recipe/create/create.html` in your 
browser.  This page demos the Cookbook.Recipe.List control. It loads
Recipes from the server, lets you delete recipes, and it also 
listens for recipes being created and adds them to the list.

Open `cookbook/recipe/list/list.js` to
see the Cookbook.Recipe.Create control's code:

    steal( 'can/control/view', 'can/view/ejs', 'can/model/elements',
      'cookbook/models', './views/init.ejs', function(){

      /**
       * @class Cookbook.Recipe.List
       * @parent index
       * @inherits can.Control
       * Lists recipes and lets you destroy them.
       */
      can.Control('Cookbook.Recipe.List',
      /** @Static */
      {
        defaults : {}
      },
      /** @Prototype */
      {
        init : function(){
          Cookbook.Models.Recipe.findAll().done(can.proxy(this.list, this));
        },

        list : function(list) {
          this.list = list;
          this.element.html(this.view('init', list));
        },

        '.destroy click': function( el ){
          if(confirm("Are you sure you want to destroy?")){
           el.closest('.recipe').model().destroy();
          }
        },

        "{Cookbook.Models.Recipe} created" : function(Recipe, ev, recipe){
         this.list.push(recipe);
        }
      });

    });

When the List control is added to the page, `init` is called:

     Cookbook.Models.Recipe.findAll().done(can.proxy(this.list, this));

This does the following:

Makes a findAll request to the `Cookbook.Models.Recipe` model and when it returns executes the `list` method
of the control. There we store the returned list and render `cookbook/recipe/list/views/init.ejs` with the list data
using `this.view('init', list)`.

`init.ejs` looks like:

    <% this.each(function(current) { %>
      <li <%= current %>>
        <h3>
          <%= current.attr('name') %>
          <a href='javascript://' class='destroy'>X</a>
        </h3>
        <p><%= current.attr('description') %></p>
      </li>
    <% }) %>

This iterates through the recipes retrieved from the server.  For each
recipe, it creates an LI element and renders `name` and `description` using
live binding.

Notice that the view 'adds' each recipe instance to its LI element with:

    <%= current %>

This adds the model to jQuery.data and sets a 'recipe' className on the 
LI element.  We'll make use of this in a moment.

__Destroying Recipes__

Each recipe has a destroy link.  When it is clicked on the list's
`'.destroy click'` method is called:

    if(confirm("Are you sure you want to destroy?")){
      el.closest('.recipe').model().destroy();
    }

This method checks if you want to destroy the method.  If you do,
it finds the parent 'recipe' element and gets back the model instance (that's
in jQuery.data). It then calls [can.Model.prototype.destroy model's destroy] method.

When a model is destroyed, all occurrences will be removed from any list. Due to live binding
the displayed list will update automatically.

__Creating Recipes__

When a recipe is created, a "created" event is triggered.  The List control listens for this
with:

    "{Cookbook.Models.Recipe} created" : function(Recipe, ev, recipe){
     this.list.push(recipe);
    }

So, when a recipe is created, we just add it to the list we are currently displaying.
Again, thanks to live binding the list will just update itself without having anything else to do.

### Putting it all Together

The cookbook application loads both of these widgets and adds them to the page.
When Cookbook.Recipe.Create creates a Recipe, it creates a 'created' event which
Cookbook.Recipe.List listens for and adds that newly created recipe to its list
of recipes.

Continue to [testing Testing Cookbook].
