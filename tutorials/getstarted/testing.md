@page testing Testing Cookbook
@parent getstarted 1

JavaScriptMVC puts a tremendous emphasis on 
testing. It uses [FuncUnit] to provide easy to write functional and
unit tests that can be run in the browser or automated!

When you scaffolded recipe, it created tests for you.  This guide will show you how to:

- Run unit tests.
- Understand the unit tests.
- Run functional tests.
- Understand the functional tests.
- Test isTasty functionality.

## Run Unit Tests

FuncUnit uses QUnit unit test things like models and basic plugins.  You can run these
tests in the browser or Envjs (and now PhantomJS with a little extra work).

### Run Unit Tests in the Browser

Open `cookbook/qunit.html`.  You should see something like:

@image site/images/test_cookbook_example.png

We'll see how this works in a second.  First, lets run the unit tests
in Envjs.

### Run Unit Tests with Selenium

Selenium automates browsers. FuncUnit can run your tests in this environment.

In a command window type:

    > js funcunit/open/selenium cookbook/qunit.html

This runs qunit.html in a simulated 
browser environment. The output should look like:

<img src='http://wiki.javascriptmvc.com/wiki/images/2/24/Qunit-envjs.png' width='500px'>

## Understanding the Unit Tests

FuncUnit uses [http://docs.jquery.com/QUnit QUnit] for assertions and
organizing them into tests and modules.  The best place to start understanding the
unit tests is to read through [http://docs.jquery.com/QUnit QUnit's documentation].

__cookbook/qunit.html__ loads [steal] and tells it to load
`cookbook/test/qunit/qunit.js` which loads your unit tests:

 - `cookbook/test/qunit/cookbook_test.js`
 - `cookbook/test/qunit/recipe_test.js`
 
These files steal `funcunit/qunit` then 
declare tests that get run once 
all scripts have loaded.

Open __cookbook/test/qunit/recipe_test.js__, 
and lets see how the __findAll__ test works:


    //creates a test
    test("findAll", function(){
      //prevents the next test from running
      stop(2000);
      
      //requests recipes
      Cookbook.Models.Recipe.findAll({}, function(recipes){
    
        //makes sure we have something
        ok(recipes)
    
        //makes sure we have at least 1 recipe
        ok(recipes.length)
    
        //makes sure a recipe looks right
        ok(recipes[0].name)
        ok(recipes[0].description)
    
        //allows the next test to start
        start()
      });
    })

## Run Functional Tests

Functional tests are used to test things 
that require user interaction like widgets that listen for 
clicks and key events.

Cookbook's functional tests test the recipe create and recipe lists widgets.

### Run Functional Tests in the Browser

Open `cookbook/funcunit.html`.  You should see something like:

<img src='http://wiki.javascriptmvc.com/wiki/images/b/b6/Funcunit.png'/>

### Run Functional Tests in Selenium

[http://seleniumhq.org/ Selenium] can automate launching browsers and is packaged
with FuncUnit.

In a command window type:

    > js funcunit/open/selenium cookbook/funcunit.html

This should open Firefox and IE if you are using Windows.  The results of the
test should look like:

<img src='http://wiki.javascriptmvc.com/wiki/images/a/a7/Funcunit-envjs.png' width='500px'>
<div class='whisper'>
	If Selenium is unable to open your browsers, it's likely you have them in an
	unusual location.  Read [FuncUnit.static.browsers] for information on how to configure browsers
	so selenium can find them.
</div>

If you are having trouble running the tests in Internet Explorer, you need to change a 
few settings in the browser.  Please see the [FuncUnit FuncUnit documentation] for troubleshooting help.

## Understanding FuncUnit Tests

FuncUnit adds to QUnit the ability to open another page, in this case
`cookbook/cookbook.html`, perform actions on it, and
get information from it.

The `cookbook/funcunit.html`  page
works just like `qunit.html` except the 'funcunit' plugin is loaded which 
provides [FuncUnit]. FuncUnit is aliased to `S` to highlight the similarity between its API
and jQuery's API.

Let take a quick look at a FuncUnit test:

    test("create recipes", function(){

      //type Ice in the name field
      S("[name=name]").type("Ice")

      //type Cold Water in the description field
      S("[name=description]").type("Cold Water")

      //click the submit button
      S("[type=submit]").click()

      //wait until the 2nd recipe exists
      S('.recipe:nth-child(2)').exists()

      //Gets the text of the first td
      S('.recipe:nth-child(2) td:first').text(function(text){

        //checks taht it has ice
        ok(text.match(/Ice/), "Typed Ice");
      });

    })

Functional tests are largely many asynchronous actions 
(clicks and keypresses)
with relatively few checks/assertions.  
FuncUnit's goal is to provide as readable and linear syntax as possible.
FuncUnit statements are actually stored and then run asynchronously.  This requires that
getting a value from the page happens in a callback function.

For more information on FuncUnit, read its [FuncUnit documentation]
