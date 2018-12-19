# NodeJS POC Project
A NodeJS project designed to learn the ins and outs of NodeJS.

This project will be used to learn NodeJS.  Target audience is developers who have used other Web Development frameworks, namely .NET MVC / API v2.  

This project will grow in complexity as new techniques are incorporated.  The idea is to start with the most trivial examples using as few dependencies as possible and expand from there.  As a result, many instructions for installing dependencies will happen later in the development process than they would for a more seasoned Node developer.  

This project will also serve to document each step, no matter how trivial, during the setup process.

# Section 1: Hello Node (The most basic of basic web applications) 

__Note__ most of this "Hello Node" portion of this project is based on dumbed-down version of of _Ben Gourley's_ tutorial.  
[A Simple Website in Node.js – 2016 Edition](http://www.clock.co.uk/blog/a-simple-website-in-node-js-2016-edition).  

Much of __Section 1__ is simply over explaining steps that are already outlined in his tutorial.

## Project Initialization
The first step is to create a root directory, we'll call it 'node_poc' for now, and to cd into that directory.

```
mkdir node_poc
cd node_poc
```

Next we have to initialize node 

```
npm init
```

This command will promt you to fill out same basic info for your project, feel free to hit enter for each prompt to add default values.
Since this is not a production application these details can be ommitted for now (You can always modify them later).

This command will also generate the file _package.json_.  The package.json will contain the details of your app that were
generated by the promts from the `npm init` command.  It will also act as a package manager, storing any dependencies you install.

## Adding Express Dependency
Express is a web application framework that provides HTTP Utility methods, routing, and other features essential to a basic web application.  There are several alternatives to Express and it is by no means the only solution, however it is a framework that fits the needs of a first time node application as well as more robust production level applications, making it suitable for the scope of this project.  More information on express can be found on the [projects website](http://expressjs.com).

Add the express dependencies by entering the following command from the projects root folder:

```
npm i --save express@4
```

__A note on the paramaters__
The `i` paramater installs the target package to a newly created folder _node_modules_ which will contain all of your future dependencies.  

The `--save` paramater adds this dependency to your packages.json file's dependencies section.  This will allow you to use the command `nmp install` to install in any dependencies you have previously saved, without having to commit the files in `node_modules` to your repository. 

##views directory
Create a directory from the root of your application.

```
mkdir views
```

The views directory will contain all of the web pages that will be available from your server.

## index.html

Create file index.html in the root of your views directory. 

```
touch views/index.html
```

index.html will be the default webpage that is shown when you run your webserver.

Add the following HTML markup to index.html

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello NodeJS</title>
</head>
<body>
    <p>Hello NodeJS</p>
</body>
```

## Server.js
Create a file named server.js using the following command
```
touch server.js
```

This file will be the entry point for you web server. Adding the following code to create a simple web server to host your content.

```
/*load the express dependencies */ 
var express = require("express");
var app = express();
var router = express.Router();

/*set the root of your html files*/
var path = __dirname + '/views/';

/*create a basic router configuration*/
router.use(function (req,res,next) {
  console.log("/" + req.method);
  next();
});

/*Set the defaul webpage*/ 
router.get("/",function(req,res){
  res.sendFile(path + "index.html");
});

/*Add instructions to serve up your webpage*/
app.use("/",router);

/*Start webserver*/
app.listen(3000,function(){
  console.log("Live at Port 3000");
});

```

## Run Application

Launch your webserver by entering the following command from your projects root.

```
npm start
```

If you succesfully followed these steps your console should ouput the following.

```
Live at Port 300
```

Open a web browser to the to `http://localhost:3000`

Your browser should display the following.

![Index Screenshot](https://github.com/palburtus/node_poc/blob/master/assets/hello_node_js.png)

# Section 2: Templating

For even the most basic web applications you will want to use some version of templating in order to avoid adding the same HTML that will appear on multiple pages.  For those farmilar with MVC master pages, this is the same concept.  You create a "master" containing common components, such as nav, footers, etc, and have other pages inherit this template.  


We will be changing some of the file names that were created in Section 1, so just to recap as of right now your directory structure
should look something like this.

```
node_poc
│   server.js    
│   package.json
└───node_modules
│   │   express
└───views
    │   index.html
```

## Install Express-Handlebars
While there are several frameworks available for node developers, Jade being an example of a popular one, that acomplish templating, for beginners, Express Handlerbars is a good starting point and easy to tackle.

The github page for the express-handlebars project can be found [here](https://github.com/ericf/express-handlebars)

From the root folder of your application run the following command:

```
npm install express-handlebars
```
## Create Main Template
Our first step is to create a directory that will contain all of our layouts (for now this will just contain our main layout).

Create this file by running the following commands from the root folder of your project

```
cd views
mkdir layouts
cd layouts
touch main.handlebars
```
This 'main.handlebars' file will become the template from which other files will inherit markup form.  

Enter the following HTML into main.handlebars

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Example App</title>
</head>
<body>
    <h1>Sample App</h1>
    {{{body}}}

</body>
</html>
```

The `{{{body}}` will contain the markup in your inherited files.  

Change your index.html to just contain the markup you want displayed in the `{{{body}}}` tag:

```
<p>Hello NodeJS</p>
```

## Configure Server.js to use Express-Handlebars
We need to modify the server.js file to use the handlebars engine instead of the express router.  Replace the server.js code with the following.

```
/*load the express dependency */ 
var express = require('express');
/*load the express handlebar dependency */ 
var exphbs  = require('express-handlebars');

/*configure app to use handlebars template */ 
var app = express();
app.engine('handlebars', exphbs({defaultLayout: 'main'}));
app.set('view engine', 'handlebars');

/*create a basic router configuration*/
var router = express.Router();
router.use(function (req,res,next) {
  console.log("/" + req.method);
  next();
});

/*Set the defaul webpage now using handlebars*/ 
router.get('/', function (req, res) {
    res.render('index');
});

/*Add instructions to serve up your webpage*/
app.use("/",router);

/*Start webserver*/
app.listen(3000, function () {
    console.log('express-handlebars example server listening on: 3000');
});
```

Finally rename `index.html` to `index.handlebars`

Your project directory should now look like the following.

```
node_poc
│   server.js    
│   package.json
└───node_modules
│   │   express
|   |   express-handlebars
└───views
    │   index.handlebars
    └───layouts
        |   main.handlebars
    
```
Launch your webserver by entering the following command from your projects root.

```
npm start
```

Open a web browser to the to `http://localhost:3000`

Your browser should now display the following.

![Index with Template Screenshot](https://github.com/palburtus/node_poc/blob/master/assets/hellohandlebars.png?raw=true)

# Section 3: Basic Dependencies 
We mentioned at the beginning of this document that we would try to install as few dependcies as possible in the begining.  The main reason for this is that, especially for developers new to Node.js, the sheer number of npm modules can be overwhelming.  In addition, there are many popular modules that do the same things.  As a result, when trying to use node.js for the first time it can be difficult to know what node modules you should use vs what node modules you could use (i.e. we are using handlebars for scafolding and routing.  There other popular node modules that do what handlebars does that you can use).  This can become particularly difficult when following other developer's guides and tutorials as they may use different dependecies and assume the reader understands what each of them do.   

With that in mind, from this point on we will start adding some additional dependencies.  Whenever one is added, we will try to explain exactly what we are using it for and (where applicable) make note of other popular alternatives to the module we decide to us.  The decision to install one module vs another when both modules offer the same functionality is often as simple as personal preference or developer familiarity, we are not suggesting that one 3rd party module is better or more popular than any alternatives.  

### Installing a File Watcher (nodemon)

At this point, any time you make a change to a file you need to restart the webserver in order to view it.  This is a real pain when you are developing.  Node provides an API that allows you server to watch for file changes, however rolling your own file watcher implementation can be a bit cumbersome.  Luckly there are a number of node modules out there that remedy this problem.  We will be using [nodemon](https://github.com/remy/nodemon).  

From the root of your project run the following command.

```
npm install --save-dev nodemon
```

This will add [nodemon](https://nodemon.io/) to your node_modules directory.  Also note that we used the `--save-dev` paramater instead of just `--save`.  This is because nodemon is a tool used for development and should not be included in your production configuration.  We will cover configuration management in a later section. 

Some popular alternatives to nodemon include but are not limited to forever, node-supervisor, and node-dev.  

### Install jQuery

Run the following command from your projects root directory

```
npm install jquery
```
This command will save jquery in your node_modules folder.  However, jQuery will need to be deployed and servered by the webserver in order to be used and we don't want to serve it directly from node_modules.  Instead we will define where it the webserver should delpoy it in our server.js.

Add the following code to your server.js

```
app.use('/js', express.static(__dirname + '/node_modules/jquery/dist')); 
```

Let's take a moment to disect this code to better understand what its purpose is.
- The /js indicates that we want to server this file in a folder named 'js' from the web server root. 
- The variable &#95;&#95;dirname is a static variable that points to the root of your project, you do not need to declare it.
- '/node_modules/jquery/dist' is the location of your jQuery file on your file system.

So on your file system your jquery file structure looks something like this...

```
node_poc
│   server.js    
│   package.json
└───node_modules
│   └───jquery
│       └───dist
│           └───jquery.js
```

...and your app.use statement will cause your jquery file structure on the webserver to look like this

```
<web_server_root>
└───js
│   └───jquery.js
```

You can now reference jquery in the <head> section of your "main.handlebars" file like so
    
```
<script src="/js/jquery.js"></script>
```

### Install Bootstrap

__Note__ jquery is a dependency of bootstrap thus needs to be installed in order to use bootstrap.

```
npm install bootstrap
```

This will install the lastest version of Bootstrap (as of this last update of this document version 4.1.3).  Bootstrap is a popular, open-soure library maintaned by Twitter that provides some CSS and Javascript styles and components.  We will drop in a sample navigation bare the utilizes bootstrap into this application in a bit.  

You can now reference bootstrap dependencies much like we just did for jQuery, however bootstrap requires a .css file and a .js file and has a slightly different node_modules directory setup than jquery so we will have to make a few minor adjustments.  

To further illustrate here is the current node_modules directory structure for jquery and bootstrap.

```
node_poc
│   server.js    
│   package.json
└───node_modules
│   └───jquery
│       └───dist
│           └───jquery.js
└───bootstrap
│       └───dist
│           └───js
│               └───bootstrap.js
│           └───css
│               └───bootstrap.css
```

First lets include the bootstrap javascript file.  We will serve this file in the same directory as the jquery.js file.  Include the following code in your server.js file right under hte "app.use" code for query

```
app.use('/js', express.static(__dirname + '/node_modules/bootstrap/dist/js'));
```

Notice the first paramater 'js' is the same since we want to server this file in the same folder as jquery, however the second param ponits to a different folder and file -with a different file structure- than jquery.

Next lets include the bootstrap css import right below the bootstrap js import in our server.js file.  

```
app.use('/css', express.static(__dirname + '/node_modules/bootstrap/dist/css'));
```

Notice the first paramater 'css' is different in this app.use statement.  This will cause the bootstrap file to be served under a 'css' folder from the webserver root.  The resulting file structure on the webserver should look like the following.  

```
<web_server_root>
└───js
│   └───jquery.js
│   └───bootstrap.js
└───css
│   └───bootstrap.css
```

Finally we need to reference these two files that we are now serving up.  To do this, reference the files in the 'head' section of the HTML in your main.handlebars file.

```
<link rel="stylesheet" href="/css/bootstrap.min.css">
<script src="/js/bootstrap.min.js"></script>
```

Now we can add a sample navigation bar provided by the bootstrap 4 documentation.  We will add only two links "Home" and "About" (we will use this links later to demonstrate setting up simple reqeust routing).  This should be added to your "main.handlebars" file right below the "body" tag.

```
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
			<a class="navbar-brand" href="#">WebMD Mobile CMS</a>
			<button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
				<span class="navbar-toggler-icon"></span>
			</button>

			<div class="collapse navbar-collapse" id="navbarSupportedContent">
				<ul class="navbar-nav mr-auto">
					<li class="nav-item active">
						<a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
					</li>
					<li class="nav-item">
						<a class="nav-link" href="/About">About</a>
					</li>
				</ul>
			</div>
		</nav>
```

You can serve any number files to the web server using app.use code the first paramater is the name of the folder you want the file to be served in the webserver, and the second paramter is the location of the the file in your node_modules directory.  

# Section 4: Basic Routing

