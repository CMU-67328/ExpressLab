# ExpressLab
#Express Lab

## Step 0 - Make this file easier to read
This file is written in [markdown](http://daringfireball.net/projects/markdown/).  You can read it in a text editor, but to see the formatting more clearly, try:

* The [Markdown Preview Plus ](https://chrome.google.com/webstore/detail/markdown-preview-plus/febilkbfcbhebfnokafefeacimjdckgl?hl=en-US) Chrome extension
    * Make sure to follow the directions to allow access to file URLs
* Copy this file's contents and paste them into the online markdown editor/previewer at [Markable](http://markable.in/editor/)

## Step 1 - Hello World

1. In a terminal window, cd to a directory to work in.
2. Create the package.json file:
    - Run `npm init`
    - Use defaults except for entry point type `app.js`
3. View the package.json that is created in the current directory
    - I.e. with cat, vim, or in another editor
4. Run `npm install express --save`
    - This will download Express
    - *--save* will add the dependency to package.json
    - Look at the contents of the current directory, there is now a *node_modules* folder
    - View the package.json file and see the added Express dependency.  
5. Create and edit a new file `app.js` and insert the following code:

```
var express = require("express");
var app = express();
/*
 * Upon receiving a GET request of the path /hi,
 * do the callback that sends as the response "hello world"
 */
app.get("/hi", function (request, response) {
  response.end("hello world");
  });
  app.listen(50000);
console.log("Server listening at http://localhost:50000/");
```
6. Start your app running with `node app`.
7. Browse to your app and verify that it works.
    - What is the path to have it reply with "hello world"?
    - What is the response if you give no path with the URL?

## Step 2 - Add logging middleware
It would be useful to have some logging messages to show what URLs that are visiting your site. The NPM package [Morgan](https://www.npmjs.org/package/morgan) is middleware to provide formatted logging messages to the node console.

Middleware is a function that handles requests and is part of the controller function of MVC.  Middleware is used for many purposes; most basically it is used to take the request and call the right route-handling functions that will perform the rest of the controller role.  The app.get in Step 1 is serving that role.  Middleware can also be used to do authentication, authorization, manage cookies and sessions, and many more functions.  

Sencha Labs provides a [list of middleware modules that work well with Express](https://github.com/senchalabs/connect?_ga=1.75415314.1436979566.1385393094#middleware).  You can use several middleware modules, each doing its own processing of the request.  For example, you can use body-parser to process the body of post and put requests, and express-sessions to manage sessions.  This is known as *stacking* middleware.

For now, we are going to add the logging module, Morgan, to our middleware stack.  The stack is processed in order, so we should put Morgan *before* the app.get that processes the request.

1. Run `npm install morgan --save` to install Morgan and include it in package.json
2. View the package.json to see the added Morgan dependency
3. Go to [Morgan package at npmjs.org and scroll down to the Express/Connect examples](https://www.npmjs.org/package/morgan#examples)
4. Using their example, integrate Morgan into your app.js
5. Run your node app and visit it with a few different paths, including /hi.
6. Back at the Morgan npm documentation, scroll up to the [Predefined Formats](https://www.npmjs.org/package/morgan#predefined-formats) section and review the options available for formatting the logging messages.  Experiment with a couple options.  For example, I personally like the *tiny*  format.

## Step 3 - Add an additional GET path
Express' route pattern matching makes implementing routes very easy. For example, add the following path to your app:

```
app.get("/bye", function (request, response) {
  response.end("Toodles!");
  });
```

Middleware is executed in order, but will only be called if the path pattern is matched.  Therefore it does not matter if you put this before or after the /hi route.  It should be after the Morgan logger, however, so that the request is first logged before it is handled.

Re-run your node app and browse to the /bye and /hi paths.

## Step 4 - Nodemon

It can be a nuisance to have to quit and restart node each time you edit your app.  Nodemon is an npm package that will monitor your files and restart node automagically whenever you save a change.

Nodemon is not a module, but a program.  But it is available via npm.  To install it from the terminal prompt, run: `npm install -g nodemon`.  The -g means to install it *globally* and not in this particular directory.  You might have to run this as administrator or sudo depending on the permissions on your laptop.

Once you have it installed, instead of running your app directly with node, run it with `nodemon app`.  

Give it a try by editing your app file and seeing node being restarted upon save.

## Step 5 - Other HTTP Methods (PUT, POST, DELETE)

As you might have inferred, app.get is middleware for GET requests.  Similarly app.put will match PUT requests, and there are similar functions for POST and DELETE.

1. Add a new route to handle PUT requests to the /hi route:
```
app.put("/hi", function (request, response) {
  response.end("Hi route from PUT request");
  });
```
2. Visiting this route is not as easy as typing in the URL in a browser, for all requests from the browser web address bar are GET requests.  One way to test is to create a web page to make the requests.  For testing purposes, we can also use a Chrome plugin called Rest Console. This was mentioned back in the Networking classes.  If you have not already done so, install the [Rest Console plugin into Chrome](https://chrome.google.com/webstore/detail/rest-console/cokgbflfommojglbmbpenpphppikmonn).
3. From the Rest Console, in the Target Request URI section, put the URL `http://localhost:50000/hi` then click the **PUT** button at the bottom of the page.  The page will then automatically scroll to the bottom where you should see the response "Hi route from PUT request".
4. Also experiment with GET to /hi and /bye.

## Step 5 - Query parameters

GET requests often have query parameters that we need to access.  Express includes a default query parser that is an extended version of the one your used with the HTTP module.  

It is based on [qs](https://www.npmjs.org/package/qs) which is _**very**_ powerful.  You probably won't need to use most of the power but you might browse the documentation to be familiar with it.  

Most important for your use is that the query parameters are automatically parsed from the request URL and added as an object on the request object.

The Express API is the best source of information on Express.  The latest version is 4.x which we will use in this class, and the base URL for its API is http://expressjs.com/4x/api.html.

1. Review the Express examples for [req.query](http://expressjs.com/4x/api.html#req.query).  Notice that it is very simple to refer to a query attribute (such as *q*) by its name on req.query (i.e. req.query.q).
2. Add a new GET route to your app for a path of the form `/say?greeting=Alii&to=Edwel` that returns "Alii Edwel".
3. Test your new route.  Experiment whether the query string order matters: `/say?to=Pua&greeting=Kia%20Orana` (This sounds like a simple exam question I could use.)

## Step 6 - Route parameters

Express also makes it easy to parse the individual components of a URL path as parameters.  Documentation for this can be found [here](http://expressjs.com/4x/api.html#req.params).

For example, consider the set of paths:
* /user/FredF/firstname
* /user/FredF/lastname
* /user/FredF/occupation

The /user is a fixed part of the path, indicating a route.  FredF is a username, and firstname, lastname, and occupation indicate some aspect of the user's profile information.

To route this set of paths in Express, we can use the pattern `/user/:username/:userinfo`
* :username is a parameter, and can be accessed with request.params.username
* :userinfo is a parameter, and can be accessed with request.params.userinfo

Add a route to handle the /user path to your app:

```
app.get("/user/:username/:userinfo", function (request, response) {
  response.end("The user is "+request.params.username+" and the requested info is "+request.params.userinfo);
  });
```

Test the new path.

## Step 7 - Route modules

Of course we will want to do more in our app than just return simple strings.  Therefore we typically put the rest of the MVC controller logic into separate modules.  Lets expand what we can do with the /user path.

1. Create a new local module to handle the user route.  For good separation of concerns and organization, lets put it in a new directory called `routes`.  Therefore create a new file called `routes/user.js`.
2. In user.js, put the code:
```
exports.getUser = function(request, response) {
  response.end("The user is "+request.params.username+" and the requested info is "+request.params.userinfo);
  }
```
3. Change the route for /user in app.js to be:
```
app.get("/user/:username/:userinfo", userRoutes.getUser);
```
4. And add the require at the top of app.js:
```
var userRoutes = require('./routes/user.js');
```
5. Test the updated route.  It should return the same response as before.

## Step 8 - Add another route to the route module

1. Add a new PUT route for /user that will call a second function in the user.js module:
```
exports.putUser = function(request, response) {
  response.end("Adding the user "+request.params.username+" and info "+request.params.userinfo+" with value "+request.params.uservalue);
  }
```
2. Visit your app with the path `/user/WilmaF/lastname/Flintstone` using the Chrome REST Console.

## Step 9 - The beginning of a model

1. In the putUser function, store the information about WimaF in a JavaScript object variable (global to the user.js file, not within get User).
2. Change the getUser function so that it will retrieve and return the information for the user stored in the variable.

