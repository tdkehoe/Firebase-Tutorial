# Firebase Project: Make the CRUDiest Movies Database

This tutorial is divided into several files because the Atom editor can be overwhelmed by long files. The files are:

1. Firebase Principles (this file)
2. Setting Up Firebase

5. Firebase Auth

## Firebase Principles Table of Contents

* [A Short History of Websites](## A Short History of Websites)
* [What Is Firebase?](## What Is Firebase?)
* [Firebase Arrays vs. Objects](## Firebase Arrays vs. Objects)
* [Firebase Methods](## Firebase Methods)



## A Short History of Websites

Firebase is a NoSQL cloud database service. Storing your data in the cloud has become popular for a variety of reasons. To explain the advantages of Firebase, allow me to walk through my twenty years of building websites. In the 1990s we built static websites by hand-coding HTML. The World Wide Web (WWW) enabled users to look up information but websites weren't interactive. The browser sent an _HTTP request_ to the server, which served an HTML file to the browser, and the browser rendered the HTML code for the user to see. Then the user clicked a link, which sent another HTTP request to the server for another webpage, and so on.

In 1999 I wrote the first documentation for using a new language called Personal Home Page (PHP) with a new database called MySQL. My documentation became the most popular and highest-rated download from the website DevShed.com. Being able to (relatively) easily connect a database to your HTML enabled customers to do things such as make dating website profiles. Soon the WWW was dominated by websites where users stored stuff in databases, e.g., MySpace.com and Facebook.com. We wrote static HTML webpages but inserted PHP code to get data to fill in spaces on the webpage, such as a user's name or picture. The browser sent an HTTP request to the server, which ran PHP, Java, or another programming language to translate the HTTP requests into SQL queries. The data filled in the spaces of a static webpage with dynamic content, then served it to the browser.

The next step was _dynamic websites_, in which a database retrieved parts of a website and assembled the parts into webpages. No longer did webmasters have to write HTML. Instead we used _content management systems_ (CMS) such as WordPress. A blogger could set up a complex website by plugging in modules, with little or no coding knowledge. With Ruby on Rails (RoR) developers could spin up a website in hours instead of weeks, plugging in modules for almost anything. The browser sent an HTTP request to the server, which hit the database perhaps hundreds of times to assemble the webpage, then rendered the webpage and served it to the browser. The user clicked on a button or a link, another HTTP request went to the server, the server hits the database perhaps hundreds more times, etc.

Dynamic websites are slow. Users click and wait while a lot of stuff happens on the server, before their page loads. Webmasters have to design websites to minimize clicks or users will bounce, and then pay top dollar for the fastest hosting services, and their websites are still slow.

Vast numbers of software engineers work at companies with SQL back ends, coding in PHP, C, Java, or Python. With SQL a database administrator must set up the _schema_ or structure of the database. The arrays and objects that software developers create must be translated into tables of columns and rows. Like most professions, learning the work isn't easy, but once you learn it you can expect to do the same stuff every day for the next twenty years.

The next step was JavaScript. JavaScript is the only language that runs in the browser. Initially JavaScript did only minor tasks on websites but then frameworks such as Angular were developed. Initially the browser sends an HTTP request to the server, but the server sends not a rendered webpage but an application that runs on the user's computer, in the browser. The user clicks on buttons and the app does stuff, such as opening a calendar, without going back to the server. The user interface or user experience (UI/UX) is fast, practically instantaneous.

JavaScript web apps still need data. An app can open a calendar, but to open a calendar of your AirBnB reservations that app has to send an HTTP request to the server to access the database and send back data. This is faster than a client-server website because only small amounts of data are requested (instead of entire webpages), and the webpages (now called _views_) don't have to be rendered each time they change, as only the parts that change are updated.

With JavaScript web apps the _back end_ servers do little. The servers just translate HTTP requests into SQL queries and send data in HTTP responses. _NoSQL databases_ eliminate the translation into SQL queries. NoSQL databases store arrays of objects, connecting seamlessly to programming languages, especially JavaScript and JavaScript Object Notation (JSON). A NoSQL server can be spun up in a few hours, and after that there's little or nothing to do on the back end.

## What Is Firebase?

Firebase is a NoSQL cloud database. You write your front-end app in Angular, React, or another framework. You use the ```$firebaseArray``` and ```$firebaseObject``` _services_ in your controllers to _bind_ your local arrays and objects to arrays and objects in the remote database. Your local arrays and objects stay synchronized to the remote database without HTTP requests or database queries.

When data changes in Firebase the app updates, and vice versa. In contrast, with a client-server structure the app only updates when the browser sends an HTTP request to the server, usually when the user clicks something. If you order a pizza and the driver's GPS is constantly sending updates, you don't want to have to click every five seconds to get new data. You want the app to show the driver getting closer to you without you having to click anything.

A third service, ```$firebaseAuth```, creates an _auth object_ to log in a user, authorize which views the user can access, what data the user can read or write, etc.

With Firebase, front-end developers can focus on the UI/UX without thinking about the back end. Compare the [CRUDiest Movies Database](https://crudiest-firebase.firebaseapp.com/#/movies) to the [Internet Movies Database](http://www.imdb.com/) (IMDb). The IMDb feels like it was written a long time ago in a galaxy far, far away. In contrast, the Firebase app feels like it's a native app, i.e., as if the movies are on your computer.

To reiterate the reasons to use Firebase:

* You don't need to run a server. Servers take time to set up, and then can crash or are shut down by your host for maintenance. A Node/Express/MongoDB server adds no value to your website, i.e., it doesn't do anything that you can't do in a front-end framework such as AngularJS (e.g., routing).
* Data binding synchronizes your local arrays and objects to the remote arrays and objects. You don't have to write HTTP requests or database queries. Your users don't have to click to see updated data.
* Firebase handles authorization and authentication for you, including OAuth2, e-mail & password login, and routing authorization.







## Firebase Arrays vs. Objects, and Firebase Methods

Firebase is a NoSQL database. It stores an arrays and objects.



Firebase uses its own methods. You can use JavaScript methods with Firebase but mixing JavaScript methods and Firebase methods is a bad idea. For example, to add an object to array in JavaScript you use ```push()```. In Firebase you use





Firebase methods do different things depending on whether the method is chained to a ```$firebaseArray``` or to a ```$firebaseObject```. When you work with nested arrays and objects you may chain a Firebase method


Our database will be an array of movie objects.

Our ```INDEX/NEW``` view (the home page) will display all the movies in out database. This is an array of objects so we'll inject the service ```$firebaseArray``` into ```HomeController.js```.

```js
app.controller('HomeController', ['$scope', '$firebaseArray', function($scope, $firebaseArray) {
  console.log("Home controller.");

}]);
```

Our ```SHOW/EDIT``` view will display one movie. This is an object so we'll inject the service ```$firebaseObject``` into the ```ShowController.js```.

```js
app.controller('ShowController', ['$scope', '$firebaseObject', function($scope, $firebaseObject) {
  console.log("Show controller.");

}]);
```


## Firebase .child() Notation

Firebase uses ```.child()``` notation instead of dot notation because Firebase objects are different from JavaScript objects.

![Firebase object](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_firebase_object.png)

Let's write that as a JSON object:

```js
{
  $id: "-KGSpDSJEtvCHNeXWriS",
  {
    movieTitle: "Plan 9 from Outer Space",
    movieDirector: "Edward D. Wood, Jr.",
    moviePlot: "Aliens resurrect dead humans as zombies and vampires to stop humanity from creating the Solaranite (a sort of sun-driven bomb).",
    movieYear: "1959"
  }
}
```

A Firebase object consists of two properties: the key and another, nested object. The nested object contains our properties. This nested object format makes JavaScript dot notation difficult (if not impossible) to use.

You can make JavaScript arrays and objects and use JavaScript methods such as ```push()``` and ```splice()``` and store the results in your Firebase database. If you do this you won't be able to use Firebase methods such as ```$save()``` and ```$remove()```.

Firebase ```.child()``` notation has different syntax for arrays and objects. To select a property of an object use ```.child('propertyName')```, where ```propertyName``` is the name of the property you want.

Selecting an object from an array is trickier. The format is ```.child(key)```. In the above example we used ```.child($routeParams.id)``` where we had the object key in the URL, which had come from ```movie.$id```. Alternatively we could have used the Firebase method ```$keyAt(recordOrIndex)``` but I haven't gotten this to work.




## New Firebase Object

In each controller we create a new Firebase object. This is an example of _object-oriented programming_ (OOP).

```js
var ref = new Firebase("https://my-angularfire-app.firebaseio.com/");
```

Replace ```my-angularfire-app``` with the name of your app. Be sure to use the "Dashboard" URL that ends in ```firebaseio.com```, not the "URL" URL that ends in ```firebaseapp.com```.

Don't confuse the new Firebase object with ```$firebaseObject```. The latter connects to an object in your remote Firebase database. The former is the interface between your local Angular app and the remote Firebase database.

The argument for the new Firebase is the URL of your Firebase database. This is not the same as the URL we used to view our Firebase front end.

* The database access URL ends in ```firebaseIO.com```.
* The front end access URL ends in ```firebaseapp.com```.

You call the new Firebase object or array anything you want. The typical name is ```ref```.

After we create the new Firebase object or array we bind it to the ```$scope```. This makes the remote data available locally, as if the remote data were local.

## Connect New Firebase Object To the $scope.

Now connect the new Firebase object to the ```$scope``` so that the data is available locally. In ```HomeController.js``` use ```$firebaseArray``` and call the array ```movies``` (plural):

```js
var ref = new Firebase("https://my-angularfire-app.firebaseio.com/");
$scope.movies = $firebaseArray(ref);
```

In ```ShowController.js``` use ```$firebaseObject``` and call the object ```movie``` (singular):

```js
var ref = new Firebase("https://my-angularfire-app.firebaseio.com/");
$scope.movie = $firebaseObject(ref);
```

> We could alternatively use $firebaseArray to get the full array of movies, then select a single movie object with the Firebase method [$getRecord](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-getrecordkey):

```js
var ref = new Firebase("https://my-angularfire-app.firebaseio.com/");
$scope.movies = $firebaseArray(ref);
$scope.movie = $scope.movies.$getRecord($routeParams.id);
```

> This works and might make sense if we had a page that worked with both the full array of movies and single movie objects. Presumably using ```$firebaseObject``` will be faster but I haven't noticed a difference with the small databases I've worked with.






## And Why Another Movies Database?

Every coding bootcamp student makes a movies database for their mid-term project. For my graduation project at Galvanize I made an advanced web platform that enabled users to interact with each other while running JavaScript apps collaboratively. Jaws dropped when I presented my project at graduation. No one had ever seen anything like it, and they recognized that they were seeing the future of the web.

Hiring managers were less impressed. The website does nothing if no one else is on it. My description with phrases like "collaborative realtime JavaScript apps" meant nothing to them. I pictured a hiring manager going through a stack of resumes, clicking on project after project, spending no more than seconds on each. I decided to make another movies database. Hiring managers have seen hundreds of movie databases, they know what to expect, and can be quickly impressed by a well-executed movies database.

## Directory Structure

Set up this directory structure:

```
└── CRUDiest-Movies-Firebase
├── .gitignore
└── public
├── app.js
├── css
│   ├── fonts
│   └── style.css
├── index.html
├── javascript
│   ├── controllers
│   │   ├── HomeController.js
│   │   └── ShowController.js
│   ├── routes
│   │   └── routes.js
│   ├── services
│   └── templates
│       ├── home.html
│       └── show.html
├── resume
```

These commands should create these directories and files:

```
mkdir CRUDiest-Movies-Firebase
cd CRUDiest-Movies-Firebase
touch .gitignore
mkdir public
cd public
touch app.js
touch index.html
mkdir css
cd css
mkdir fonts
touch style.css
cd ..
mkdir javascript
cd javascript
mkdir controllers
cd controllers
touch HomeController.js
touch ShowController.js
cd ..
mkdir routes
cd routes
touch routes.js
cd ..
mkdir services
mkdir templates
cd templates
touch home.html
touch show.html
cd ..
cd ..
mkdir resume
cd ..
cd ..
tree
```

## Open a Firebase Account

If you haven't already, [sign up for a free Firebase account](https://www.firebase.com/).

Create an app, then follow the instructions to install ```firebase-tools``` from the command line (CLI):

```
npm install -g firebase-tools
```

This installs ```firebase-tools``` globally so you don't have to do this for every project.

Deploy your app from your project root directory, i.e., from ```CRUDiest-Movies-Firebase```:

```
firebase init
```

This creates a ```firebase.json``` file.

Then upload your files with

```
firebase deploy
```

## GitHub Repository

In your GitHub account, click the ```+``` in the upper right corner to create a new repository. Name it ```CRUDiest Movies Firebase```. Don't check the box for "Initialize this repository with a README".

GitHub will give you instructions to run in ```CRUDiest-Movies-Firebase```:

```
echo "# CRUDiest-Movies-Firebase" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:myAccount/CRUDiest-Movies-Firebase.git
git push -u origin master
```

Change ```myAccount``` to your GitHub account name.

Check that your files uploaded to your GitHub repository.

## index.html

Open the file ```index.html``` in your text editor. If you're using Atom, Type ```html``` followed by the ```TAB``` key to autofill from a snippet.

Your file should look like this:

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title></title>
</head>
<body>

</body>
</html>
```

Change line 2 from ```<html>``` to

```html
<html lang="en" ng-app="CRUDiestMoviesFirebase">
```

This declares that this will be an Angular app, and sets the name of the app. ```ng``` is short for _Angular_.

Below ```<meta charset="utf-8">``` add three more ```<meta>``` tags:

```html
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="description" content="Firebase CRUD app with AngularJS, Bootstrap, and asynchronous typeahead.">
```

The first two set up responsive views for Bootstrap. The third is used by search engines to describe your website.

Change ```<title></title>``` to ```<title>CRUDiest Movies Firebase</title>```.

Hook up the style sheet in the ```<head>``` secton:

```html
<link rel="stylesheet" href="css/style.css">
```

In the ```<body``` add a text header and then hook up the JavaScript files:

```html
<h1>CRUDiest Movies Firebase</h1>

<script type="text/javascript" src="app.js"></script>
<script type="text/javascript" src="javascript/routes/routes.js"></script>
<script type="text/javascript" src="javascript/controllers/HomeController.js"></script>
<script type="text/javascript" src="javascript/controllers/ShowController.js"></script>
```

## AngularFire

Firebase has a "vanilla" interface that we won't be using. It also has interfaces for five frameworks:

* GeoFire for realtime location queries.
* AngularFire for AngularJS.
* EmberFire for Ember.
* ReactFire for React.
* Ionic for developing hybrid mobile apps with AngularFire.

We'll use the AngularFire bindings.

To use AngularFire we need AngularJS, Firebase, and AngularFire. For this project we'll also need the Angular router, Bootstrap, and UI Bootstrap. Install these content delivery networks (CDN) in the ```<head>``` section of ```index.html```;

```html
<!-- AngularJS -->
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.5/angular.js"></script>
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.5/angular-route.js"></script>

<!-- Bootstrap CSS -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-..." crossorigin="anonymous">
<!-- UI Bootstrap-->
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/1.3.2/ui-bootstrap-tpls.min.js"></script>

<!-- Firebase -->
<script type="text/javascript" src="https://cdn.firebase.com/js/client/2.4.2/firebase.js"></script>
<!-- AngularFire -->
<script type="text/javascript" src="https://cdn.firebase.com/libs/angularfire/1.2.0/angularfire.min.js"></script>
```

The ```Bootstrap CSS``` requires that you download your own copy with a complete hash number (starting with ```sha384-```).

Update the version numbers now for
1. Angular: https://angularjs.org/ (Don't use Angular 2, it's a different framework.)
2. Bootstrap CSS: http://getbootstrap.com/
3. UI Bootstrap: https://angular-ui.github.io/bootstrap/
4. Firebase: https://www.firebase.com/docs/web/changelog.html
5. AngularFire: https://www.firebase.com/docs/web/libraries/angular/changelog.html

Every Monday morning I go through my [checklist of software updates](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/Update_Checklist.md).

Alternatively you can [download Angular](https://angularjs.org/) and the other libraries and link to them locally. That's more reliable as you can use your app without an Internet connection. The CDN is easier to install and update. To update it you just change the version number.

## Bootstrap and Angular

Bootstrap starts with a container in ```index.html```:

```html
<div class="container">
  <h1>CRUDiest Movies Database</h1>
</div>
```

Angular uses ```<ng-view />``` to make the template views appear:

```html
<div class="container">
  <h1>CRUDiest Movies Database</h1><
  <ng-view />
</div>
```

Let's center the ```<h1>``` header:

```html
<div class="row text-center">
  <a ng-href="/#/movies"><h1>CRUDiest Movies Database</h1></a>
</div>
```

Let's a backlink so that clicking on the title takes the user back to the home page. In Angular we use ```ng-href``` instead of ```href```:

```html
<a ng-href="/#/movies"><h1>CRUDiest Movies Database</h1></a>
```

## Finish index.html

Your ```index.html``` should now look like:

```html
<!DOCTYPE html>
<html lang="en" ng-app="CRUDiestMoviesFirebase">
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="Firebase CRUD app with AngularJS, Bootstrap, and asynchronous typeahead.">
  <title>CRUDiest Movies Firebase</title>

  <!-- AngularJS -->
  <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.5/angular.js"></script>
  <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.5/angular-route.js"></script>

  <!-- Bootstrap CSS -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
  <!-- UI Bootstrap-->
  <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/1.3.2/ui-bootstrap-tpls.min.js"></script>

  <!-- Firebase -->
  <script type="text/javascript" src="https://cdn.firebase.com/js/client/2.4.2/firebase.js"></script>
  <!-- AngularFire -->
  <script type="text/javascript" src="https://cdn.firebase.com/libs/angularfire/1.2.0/angularfire.min.js"></script>

  <link rel="stylesheet" href="css/style.css">

</head>
<body>
  <div class="container">
    <div class="row text-center">
      <a ng-href="/#/movies"><h1>CRUDiest Movies Database</h1></a>
    </div>
    <ng-view />
  </div>

  <script type="text/javascript" src="app.js"></script>
  <script type="text/javascript" src="javascript/routes/routes.js"></script>
  <script type="text/javascript" src="javascript/controllers/HomeController.js"></script>
  <script type="text/javascript" src="javascript/controllers/ShowController.js"></script>
</body>
</html>
```

Open your browser to the URL that Firebase provided to you. It prints after you run ```firebase deploy```. You should see your ```<h1>``` header:

![Image of header](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb-header.png?raw=true)

Save to GitHuB and deploy to Firebase

```
git status
git add .
git commit -m "Finished index.html."
git push origin master
firebase deploy
```

## app.js

In your file ```app.js``` inject the dependencies for the router and for Firebase:

```js
var app = angular.module("CRUDiestMoviesFirebase", ['ngRoute', 'firebase']);
```



## Angular Routes

We'll set up ```routes.js``` now:

```js
app.config(function($routeProvider) {

  $routeProvider
  .when('/movies', { // INDEX
    templateUrl: 'javascript/templates/home.html',
    controller: 'HomeController'
  })
  .when('/movies/:id', { // SHOW
    templateUrl: 'javascript/templates/show.html',
    controller: 'ShowController'
  })
  .otherwise({ redirectTo: '/movies' });
});
```

The first line adds or configures the ```$routeProvider``` function to the ```app``` object. Each routes starts with ```.when``` followed by a route:

* ```/movies``` is the home page or ```INDEX``` route, where all records are displayed. We'll combine this with ```ADD```.
* ```/movies/:id``` displays one movie, dynamically identified by its ID number, called the ```SHOW``` route. We'll combine this with ```EDIT```.

The ```.otherwise``` route redirects any other requests to the home page.

The order of the routes matters. The ```/movies/:id``` route has to be at the bottom because it catches other routes.

> The dynamic URLs (with the ID numbers) are something Node does easily that's hard to do with Apache.

## Set Up Views

In ```index.html```, put ```<ng-view />``` in the ```<body>``` below the ```<h1>``` element:

This is the Angular directive (it starts with ```ng-```) for displaying a view.

## Add a Movie With UI Bootstrap Typeahead

We'll set up the HTML templates (views) now. In ```home.html``` start with the Bootstrap ```row``` container:

```html
<div class="row">

</div>
```

We need to add some movies.

Let's make a form for adding movies. We could make a form with a dozen inputs for the movie title, poster, actors, director, year, etc. and expect users to type in all this data. Or we can make a _typeahead_ enabling the user to enter one word of a movie title and the typeahead finds the movie in the Open Movies Database (OMDb), then downloads the data.

[UI Bootstrap](https://angular-ui.github.io/bootstrap/) includes a _typeahead_ plugin. UI Bootstrap is the JavaScript plugin library for Angular. Standard Bootstrap JavaScript plugins are [incompatible with Angular](https://scotch.io/tutorials/how-to-correctly-use-bootstrapjs-and-angularjs-together), and don't have a _typeahead_ plugin.

### Linking to UI Bootstrap

We already added the UI Bootstrap CDN to ```index.html```. Here's more detailed instructions on this critical step:

The UI Bootstrap CDN must be below the Angular CDN.

You can [find the latest CDN](https://cdnjs.com/libraries/angular-ui-bootstrap). This website gives you a choice of four CDNs:

* Two with _templates_ that start with ```ui-bootstrap-tpls```.
* Two without templates, that start with ```ui-bootstrap```.
* Two _minified_ files that are small and load quickly, but can't be read by humans, and end in ```min.js```.
* Two not minified files that are bigger but humans can read (and change) the code, and end in ```js```.

We'll use the CDN with templates, minified:

```HTML
<script  type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/1.2.5/ui-bootstrap-tpls.min.js"></script>
```

Link only one of the four CDNs. Adding a second CDN will cause errors.

> Alternatively you can also download the library from the [UI Bootstrap](https://angular-ui.github.io/bootstrap/) website. There's a big purple button that says ```Download```. Move the file into your project folder and make a local link.

### Dependency Injection

Add the dependencies ```ui.bootstrap``` and ```ui.bootstrap.typeahead``` to ```app.js```:

```js
var app = angular.module("CRUDiestMoviesApp", ['ngRoute', 'ui.bootstrap', 'ui.bootstrap.typeahead']);
```

## Typeahead Plugin

Go to [UI Bootstrap](https://angular-ui.github.io/bootstrap/) and scroll down to the bottom. The last plugin is ```Typeahead```. You'll see there are five type of typeahead plugins:

![Five typeaheads](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/typeahead_five_options.png)

* Four of the plugins -- ```Static arrays```, ```ngModelOptions support```, ```Custom templates for results```, and ```Custom popup templates for typeahead's dropdown``` -- look up in an array of values in your controller. This is ideal when you have a limited number of options, e.g., the fifty states. The four choice format the values differently for the user, including one that adds a state flag downloaded live from Wikipedia.
* The ```Asynchronous results``` plugin goes to any database on the Internet with an API. We'll use this to connect to the Internet Movie Database (IMDB).

Add this code to ```home.html```.

```html
<form class="form-horizontal">
  <input type="text"
  class="form-control addMovie"
  ng-model="movie.movieTitle"
  uib-typeahead="address for address in getLocation($viewValue)"
  typeahead-loading="loadingLocations"
  typeahead-no-results="noResults"
  typeahead-min-length="3"
  typeahead-on-select="onSelect($item)"
  placeholder="Add the worst movie you've seen!"/>
  <span class="glyphicon glyphicon-search form-control-feedback"></span>
  <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
  <div ng-show="noResults">
    <i class="glyphicon glyphicon-remove"></i> No Results Found
  </div>
</form>
```

This form is styled as Bootstrap class ```form-horizontal```. The input is a text entry field styled as Bootstrap class ```form-control```. We add a class ```addMovie``` for additional styling.

We connect the form to the ```$scope``` with ```ng-model``` set to the movie title.

The next five lines set up a [UI Bootstrap](https://angular-ui.github.io/bootstrap/) typeahead.

The placeholder tells the user what to do.

The ```search``` glyphicon puts a search icon in the box.

The ```refresh``` glyphicon tells the user that data is being downloaded.

Lastly, the ```remove``` glyphicon tells the user that no movie was found.

In ```HomeController.js``` we'll add two handlers for the typeahead:

```js
$scope.getLocation = function(val) {
  return $http.get('//www.omdbapi.com/?s=' + val)
  .then(function(response){
    return response.data.Search.map(function(item){
      return item.Title;
    });
  });
};

$scope.onSelect = function ($item) {
  $scope.loading = true;
  $scope.movie.movieTitle = null; // needed to prevent previous query from autofilling search form
  console.log("Selected!");
  return $http.get('//www.omdbapi.com/?t=' + $item)
  .then(function(response){
    var movie = {
      movieActors: response.data.Actors,
      movieAwards: response.data.Awards,
      movieComments: [],
      movieCountry: response.data.Country,
      movieDirector: response.data.Director,
      movieGenre: response.data.Genre,
      movieLanguage: response.data.Language,
      movieLikes: 0,
      movieMetascore: response.data.Metascore,
      moviePlot: response.data.Plot,
      moviePoster: response.data.Poster,
      movieRated: response.data.Rated,
      movieRuntime: response.data.Runtime,
      movieTitle: response.data.Title,
      movieWriter: response.data.Writer,
      movieYear: response.data.Year,
      movieImdbID: response.data.imdbID,
      movieImdbRating: response.data.imdbRating,
      movieImdbVotes: response.data.imdbVotes,
      movieDateAdded: Date.now()
    };
    $scope.movies.$add(movie).then(function() {
      $scope.order = '$id' // reset orderBy so that new movie appears in upper left
      $scope.loading = false;
    });
  });
};
```

The ```getLocation()``` handler queries the OMDb for the movie and returns a drop-down menu of ten movies for the user to choose from.

When the user selects a movie from the list ```onSelect()``` fires and adds the movie to our database. The ```$scope.loading``` variable switches on to show the ```refresh``` glyphicon. The next line prevents the previous query from autofilling the search form.

We create a movie object using data from the OMDb, plus a few fields of our own:

* ```movieComments``` is an empty array, ready for users to add comments.
* ```movieLikes``` is initialized at zero, ready for users to like or dislike a movie.
* ```movieDateAdded``` takes the current time and date.

We run the AngularFire method [$add()](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-addnewdata) to add the movie object to our movies array. This is similar to the JavaScript method ```push()```.

After the movie object is added to the movies array a promise is executed. We reset the viewing order so that the new movie appears in the upper left position. Then we switch the ```$scope.loading``` variable off to hide the ```refresh``` glyphicon.

Deploy to Firebase and see if it works.

![Typeahead](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_typeahead.png)

Select a movie, then go to your Firebase Dashboard and the movie should be there.

![Typeahead](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_data.png)

Save your work to your GitHub repository:

```
git add .
git commit -m "UI Bootstrap typeahead working."
git push origin master
```

## Display Movies

Now we'll display our movies in ```home.html```:

```html
<div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
  <a ng-href="/#/movies/{{movie.$id}}"><img class="largeMoviePoster" ng-src="{{movie.moviePoster}}" alt="{{movie.movieTitle}}"></a>
</div>
```

This will display all the movie objects in our movies array, ordered by reverse date added. The ```img``` displays the movie poster, and when the user clicks on the poster the route changes to the ```SHOW``` page.

![Typeahead](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_poster.png)

### Style Movie Posters

The movies posters display in a column down the left side of the browser window. Let's add CSS styling to make the movies display in rows. In ```style.css```:

```css
.movieIndex {
  display: inline;
}
```

## View Movie Title in SHOW/EDIT

Click on a movie poster and the view changes. Note that the URL changes, with the _key_ of ```$id``` of the movie object appearing in the URL. This works because we wrapped the movie image with ```<a ng-href="/#/movies/{{movie.$id}}">``` and set up a route for ```'/movies/:id'```.

In ```show.html```, all Bootstrap views begin with:

```html
<div class="row">

</div>
```

Let's add a form for showing and editing the movie title:

```html
<div class="row">

  <form class="form-horizontal" name="editMovie">

    <div class="form-group">
      <label for="editTitle" class="col-lg-2 control-label">Title: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editTitle" ng-model="movie.movieTitle" ng-change="change()" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
    </div>

  </form>

</div>
```

This doesn't display anything because we need to work on the controller. In ```ShowController.js``` log ```$scope.movie```:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', function($scope, $firebaseObject) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com");
  $scope.movie = $firebaseObject(ref);
  console.log($scope.movie);

}]);
```

If you've added more than one movie to your array, you'll see that ```$scope.movie``` includes all your movies, when we want just one movie. We need to drill down through the array and pick out one movie. Firebase doesn't use JavaScript array notation here, such as brackets or dot notation. Instead it uses a ```child``` method:

```js
$scope.movie = $firebaseObject(ref.child($routeParams.id));
```

We're telling Firebase to get a child object from the array, selected by the ```$routeParams.id``` passed via the URL. We'll also have to inject the service ```$routeParams``` into the controller:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', function($scope, $firebaseObject, $routeParams) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com");
  $scope.movie = $firebaseObject(ref.child($routeParams.id));
  console.log($scope.movie);

}]);
```

Now when you deploy and refresh your browser the movie title should appear in the form.

![Typeahead](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_plan9.png)

Note the UI Bootstrap _tooltip_ in the form that informs users "You can type in this field".



## Edit Movie Title

You can type in the movie title form but if you refresh the browser the change wasn't saved. In the ```show.html``` we have ```ng-model="movie.movieTitle"``` so edits in the view should bind to the ```$scope```. But to get three-way data binding to Firebase we have to use the ```$save``` Firebase method.

In ```show.html``` we also have ```ng-change="change('title')"```. When the user makes a change in the form, the handler ```$scope.change()``` runs in the controller. We're passing through the movie object property `'title'`.

Add ```$scope.change``` to ```ShowController.js```:

```js
$scope.change = function() {
  $scope.movie.$save();
};
```

Now when you edit the field and refresh you'll see that the changes were saved.

## Save Message

Three-way data binding is neat but you may have noticed a user interface/user experience (UI/UX) problem. Users aren't going to check the Firebase Dashboard or refresh the browser to see if their edits were saved. We need to alert the user. We'll show the message "Saved!" next to the field when the data is saved to Firebase. In ```show.html``` add this:

```html
<div class="row">

  <form class="form-horizontal" name="editMovie">
    <div class="form-group">
      <label for="editTitle" class="col-lg-2 control-label">Title: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editTitle" ng-model="movie.movieTitle" ng-change="change()" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <div ng-show="watch.titleSave; watch.titleChange" class="saved">Saved!</div>
      </div>
    </div>
  </form>

</div>
```

Near the top of ```ShowController.js``` initialize the variable:

```js
// Initialize variables
$scope.watch = {
  titleSave: false,
};
```

Near the bottom of```ShowController.js``` make a function using the Firebase method [$watch](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-watchcallback-context):

```js
// Watch movie object title property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieTitle')).$watch(function() {
  $scope.watch.titleSave = true; // Show message
});
```

The Firebase method ```$watch``` registers an event listener any time there is a change to the data, firing its callback function. We've attached ```$watch``` to a grandchild of the movies array. The child selects a movie by its ```$id``` key, and then the grandchild selects the ```movieTitle``` property from the movie object.

The callback function switches the variable ```$scope.watch.titleSave``` to show the message.

That works too well. The ```$watch``` fires when page loads. Let's add a second condition, indicating that the user has changed the data.

In ```show.html``` add the second condition:

```html
<div ng-show="watch.titleChange && watch.titleSave" class="saved">Saved!</div>
```

> We could also write "watch.titleSave; watch.titleChange" but not "watch.titleChange; watch.titleSave". With that syntax the order of the variables matters. The ```&&``` syntax is more reliable.

Now the message will show only when the user makes a change and the data is saved.

In ```ShowController.js``` add the variable to ```$scope.change()```:

```js
$scope.change = function() {
  $scope.movie.$save();
  $scope.watch.titleChange = true; // Show message
};
```

Also initialize the variable:

```js
// Initialize variables
$scope.watch = {
  titleChange: false,
  titleSave: false
};
```

Now ```ShowController.js``` should look like this:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', function($scope, $firebaseObject, $routeParams) {
  console.log("Show controller.");

  // Initialize variables
  $scope.watch = {
    titleChange: false,
    titleSave: false
  };

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com");
  $scope.movie = $firebaseObject(ref.child($routeParams.id));
  console.log($scope.movie);

  $scope.change = function() {
    $scope.movie.$save();
    $scope.watch.titleChange = true; // Show message
  };

  // Watch movie object title property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieTitle')).$watch(function() {
    $scope.watch.titleSave = true; // Show message
  });

}]);
```

Now it'd be nice if the ```Saved!``` message went away after a few seconds. We can't use ```setTimeout()``` because it doesn't work with Angular. Instead we add the Angular service ```$timeout``` to the ```ShowController.js```:

```js
app.controller('ShowController', ['$scope', '$routeParams', '$location', '$firebaseObject', '$timeout', function($scope, $routeParams, $location, $firebaseObject, $timeout) {
  console.log("Show controller.");

}]);
```

Now we can use ```$timeout()``` in a function:

```js
// Shows and hides Saved! message
$scope.change = function(prop) {
  $scope.movie.$save();
  $scope.watch[prop + 'Change'] = true; // Show message
  $timeout(function() {
    $scope.watch[prop + 'Change'] = false; // Hide message
  }, 9900);
};
```

This waits 9.9 seconds and then switches off the message.

It's unnecessary but we can add a timer to the Firebase ```$watch``` as well:

```js
$firebaseObject(ref.child($routeParams.id).child('movieTitle')).$watch(function() {
  $scope.watch.titleSave = true; // Show message
  $timeout(function() {
    $scope.watch.titleSave = false; // Hide message
  }, 9900);
});
```

It'd be nicer if the message were animated. Add styling to ```style.css```:

```css
.saved {
  color: green;
  -webkit-animation: smooth 10s ease-in;
  -moz-animation: smooth 10s ease-in;
  -o-animation: smooth 10s ease-in;
  -ms-animation: smooth 10s ease-in;
  animation: smooth 10s ease-in;
}

@-webkit-keyframes smooth {
  0% { opacity: 0;}
  25% { opacity: 1;}
  50% { opacity: 1;}
  75% { opacity: 1;}
  100% { opacity: 0;}
}
```

The message is aligning with the top of the form. Let's move it down to align with the text in the form:

```css
.saved {
  padding-top: 4px;
  color: green;
  -webkit-animation: smooth 10s ease-in;
  -moz-animation: smooth 10s ease-in;
  -o-animation: smooth 10s ease-in;
  -ms-animation: smooth 10s ease-in;
  animation: smooth 10s ease-in;
}

@-webkit-keyframes smooth {
  0% { opacity: 0;}
  25% { opacity: 1;}
  50% { opacity: 1;}
  75% { opacity: 1;}
  100% { opacity: 0;}
}
```

## Add the Rest of the Movie Data Fields

We've completed two of the hardest parts of the web app: the typeahead and the "Saved!" messages. Now we'll work on the easier features and the styling. We'll save the authorization/authentication, which is another hard part, for the end.

In ```show.html``` within the ```<form>``` add the rest of the fields:

```html
<div class="form-group">
  <label for="editPoster" class="col-lg-2 control-label">Poster: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editPoster" ng-model="movie.moviePoster" ng-change="change('poster')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.posterSave && watch.posterChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editPlot" class="col-lg-2 control-label">Plot: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editPlot" ng-model="movie.moviePlot" ng-change="change('plot')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.plotSave && watch.plotChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editTrivia" class="col-lg-2 control-label">Trivia: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editTrivia" ng-model="movie.movieTrivia" ng-change="change('trivia')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.triviaSave && watch.triviaChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editDirector" class="col-lg-2 control-label">Director: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editDirector" ng-model="movie.movieDirector" ng-change="change('director')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.directorSave && watch.directorChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editWriter" class="col-lg-2 control-label">Writer: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editWriter" ng-model="movie.movieWriter" ng-change="change('writer')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.writerSave && watch.writerChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editActors" class="col-lg-2 control-label">Actors: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editActors" ng-model="movie.movieActors" ng-change="change('actors')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.actorsSave && watch.actorsChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editYear" class="col-lg-2 control-label">Year: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editYear" ng-model="movie.movieYear" ng-change="change('year')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.yearSave && watch.yearChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editCountry" class="col-lg-2 control-label">Country: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editCountry" ng-model="movie.movieCountry" ng-change="change('country')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.countrySave && watch.countryChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editLanguage" class="col-lg-2 control-label">Language: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editLanguage" ng-model="movie.movieLanguage" ng-change="change('language')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.languageSave && watch.languageChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editGenre" class="col-lg-2 control-label">Genre: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editGenre" ng-model="movie.movieGenre" ng-change="change('genre')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.genreSave && watch.genreChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editRated" class="col-lg-2 control-label">Rated: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editRated" ng-model="movie.movieRated" ng-change="change('rated')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.ratedSave && watch.ratedChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editAwards" class="col-lg-2 control-label">Awards: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editAwards" ng-model="movie.movieAwards" ng-change="change('awards')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.awardsSave && watch.awardsChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editIMDBRating" class="col-lg-2 control-label">IMDB Rating: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editIMDBRating" ng-model="movie.movieImdbRating" ng-change="change('imdbRating')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.imdbRatingSave && watch.imdbRatingChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editIMDBVotes" class="col-lg-2 control-label">IMDB Votes: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editIMDBVotes" ng-model="movie.movieImdbVotes" ng-change="change('imdbVotes')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.imdbVotesSave && watch.imdbVotesChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editMetascore" class="col-lg-2 control-label">Metascore: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editMetascore" ng-model="movie.movieMetascore" ng-change="change('metascore')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.metascoreSave && watch.metascoreChange" class="saved">Saved!</p>
  </div>
</div>
```

![All fields added to SHOW/EDIT view](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_all_fields.png)

In ```ShowController.js``` we could write a ```$scope.change()``` handler for each field. Or we could refactor the code to pass the property from the view:

```js
// Shows and hides Saved! message
$scope.change = function(prop) {
  $scope.movie.$save();
  $scope.watch[prop + 'Change'] = true; // Show message
  $timeout(function() {
    $scope.watch[prop + 'Change'] = false; // Hide message
  }, 9900);
};
```

Now this one function works with all the fields. You'll have to refactor the movie title form with ```ng-change="change('title')"```.

We can't write a single function to handle all the Firebase ```$watch``` properties because the ```$watch``` callback doesn't tell us which property changed. (A ```$watch``` callback on a Firebase array provides the key of the object that changed and whether the DOM changed but not the changed property.) We'll have to write many nearly identical functions:

```js
// Watch movie object poster property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('moviePoster')).$watch(function() {
  $scope.watch.posterSave = true; // Show message
  $timeout(function() {
    $scope.watch.posterSave = false; // Hide message
  }, 9900);
});

// Watch movie object plot property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('moviePlot')).$watch(function() {
  $scope.watch.plotSave = true; // Show message
  $timeout(function() {
    $scope.watch.plotSave = false; // Hide message
  }, 9900);
});

// Watch movie object trivia property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieTrivia')).$watch(function() {
  $scope.watch.triviaSave = true; // Show message
  $timeout(function() {
    $scope.watch.triviaSave = false; // Hide message
  }, 9900);
});

// Watch movie object director property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieDirector')).$watch(function() {
  $scope.watch.directorSave = true; // Show message
  $timeout(function() {
    $scope.watch.directorSave = false; // Hide message
  }, 9900);
});

// Watch movie object writer property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieWriter')).$watch(function() {
  $scope.watch.writerSave = true; // Show message
  $timeout(function() {
    $scope.watch.writerSave = false; // Hide message
  }, 9900);
});

// Watch movie object actors property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieActors')).$watch(function() {
  $scope.watch.actorsSave = true; // Show message
  $timeout(function() {
    $scope.watch.actorsSave = false; // Hide message
  }, 9900);
});

// Watch movie object year property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieYear')).$watch(function() {
  $scope.watch.yearSave = true; // Show message
  $timeout(function() {
    $scope.watch.yearSave = false; // Hide message
  }, 9900);
});

// Watch movie object country property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieCountry')).$watch(function() {
  $scope.watch.countrySave = true; // Show message
  $timeout(function() {
    $scope.watch.countrySave = false; // Hide message
  }, 9900);
});

// Watch movie object language property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieLanguage')).$watch(function() {
  $scope.watch.languageSave = true; // Show message
  $timeout(function() {
    $scope.watch.languageSave = false; // Hide message
  }, 9900);
});

// Watch movie object genre property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieGenre')).$watch(function() {
  $scope.watch.genreSave = true; // Show message
  $timeout(function() {
    $scope.watch.genreSave = false; // Hide message
  }, 9900);
});

// Watch movie object rated property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieRated')).$watch(function() {
  $scope.watch.ratedSave = true; // Show message
  $timeout(function() {
    $scope.watch.ratedSave = false; // Hide message
  }, 9900);
});

// Watch movie object awards property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieAwards')).$watch(function() {
  $scope.watch.awardsSave = true; // Show message
  $timeout(function() {
    $scope.watch.awardsSave = false; // Hide message
  }, 9900);
});

// Watch movie object IMDB rating property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieIMDBRating')).$watch(function() {
  $scope.watch.imdbRatingSave = true; // Show message
  $timeout(function() {
    $scope.watch.imdbRatingSave = false; // Hide message
  }, 9900);
});

// Watch movie object IMDB votes property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieIMDBVotes')).$watch(function() {
  $scope.watch.imdbVotesSave = true; // Show message
  $timeout(function() {
    $scope.watch.imdbVotesSave = false; // Hide message
  }, 9900);
});

// Watch movie object metascore property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('movieMetascore')).$watch(function() {
  $scope.watch.metascoreSave = true; // Show message
  $timeout(function() {
    $scope.watch.metascoreSave = false; // Hide message
  }, 9900);
});
```

Now we have a lot more variables. Variables should always be declared and initialized:

```js
// Initialize variables
$scope.watch = {
  titleSave: false,
  titleChange: false,
  posterSave: false,
  posterChange: false,
  plotSave: false,
  plotChange: false,
  triviaSave: false,
  triviaChange: false,
  directorSave: false,
  directorChange: false,
  writerSave: false,
  writerChange: false,
  actorsSave: false,
  actorsChange: false,
  yearSave: false,
  yearChange: false,
  countrySave: false,
  countryChange: false,
  languageSave: false,
  languageChange: false,
  genreSave: false,
  genreChange: false,
  ratedSave: false,
  ratedChange: false,
  awardsSave: false,
  awardsChange: false,
  imdbRatingSave: false,
  imdbRatingChange: false,
  imdbVotesSave: false,
  imdbVotesChange: false,
  metascoreSave: false,
  metascoreChange: false
};
```

## Delete Movie Button

We'll also need to delete movies. We can create a Bootstrap button in ```show.html```:

```html
<div class="form-group">
  <div class="col-lg-2">
    <p>&nbsp;</p>
  </div>
  <div class="col-sm-8">
    <button type="button" name="deleteMovie" ng-click="deleteMovie()" class="form-control btn btn-danger btn-block">Delete Movie</button>
  </div>
  <div class="col-lg-2">
    <p>&nbsp;</p>
  </div>
</div>
```

Clicking the button fires the handler ```$scope.deleteMovie()``` so we'll add that to ```ShowController.js```:

```js
// Delete movie
$scope.deleteMovie = function() {
  $scope.movie.$remove().then(function() {
    console.log("Movie deleted.");
    $location.path( "/movies" );
  }, function(error) {
    console.log("Error, movie not deleted.");
    console.log(error);
  });
};
```

We're using the Firebase method  [$remove()](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-remove) on the movie object. The method returns a promise. When the promise executes we log the event and then redirect the user to the home page.

The ```show.html``` template should look like this now:

```html
<div class="row">

  <form class="form-horizontal" name="editMovie">
    <div class="form-group">
      <label for="editTitle" class="col-lg-2 control-label">Title: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editTitle" ng-model="movie.movieTitle" ng-change="change('title')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <div ng-show="watch.titleChange && watch.titleSave" class="saved">Saved!</div>
      </div>
    </div>

    <div class="form-group">
      <label for="editPoster" class="col-lg-2 control-label">Poster: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editPoster" ng-model="movie.moviePoster" ng-change="change('poster')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.posterSave && watch.posterChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editPlot" class="col-lg-2 control-label">Plot: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editPlot" ng-model="movie.moviePlot" ng-change="change('plot')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.plotSave && watch.plotChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editTrivia" class="col-lg-2 control-label">Trivia: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editTrivia" ng-model="movie.movieTrivia" ng-change="change('trivia')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.triviaSave && watch.triviaChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editDirector" class="col-lg-2 control-label">Director: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editDirector" ng-model="movie.movieDirector" ng-change="change('director')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.directorSave && watch.directorChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editWriter" class="col-lg-2 control-label">Writer: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editWriter" ng-model="movie.movieWriter" ng-change="change('writer')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.writerSave && watch.writerChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editActors" class="col-lg-2 control-label">Actors: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editActors" ng-model="movie.movieActors" ng-change="change('actors')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.actorsSave && watch.actorsChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editYear" class="col-lg-2 control-label">Year: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editYear" ng-model="movie.movieYear" ng-change="change('year')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.yearSave && watch.yearChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editCountry" class="col-lg-2 control-label">Country: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editCountry" ng-model="movie.movieCountry" ng-change="change('country')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.countrySave && watch.countryChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editLanguage" class="col-lg-2 control-label">Language: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editLanguage" ng-model="movie.movieLanguage" ng-change="change('language')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.languageSave && watch.languageChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editGenre" class="col-lg-2 control-label">Genre: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editGenre" ng-model="movie.movieGenre" ng-change="change('genre')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.genreSave && watch.genreChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editRated" class="col-lg-2 control-label">Rated: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editRated" ng-model="movie.movieRated" ng-change="change('rated')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.ratedSave && watch.ratedChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editAwards" class="col-lg-2 control-label">Awards: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editAwards" ng-model="movie.movieAwards" ng-change="change('awards')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.awardsSave && watch.awardsChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editIMDBRating" class="col-lg-2 control-label">IMDB Rating: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editIMDBRating" ng-model="movie.movieImdbRating" ng-change="change('imdbRating')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.imdbRatingSave && watch.imdbRatingChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editIMDBVotes" class="col-lg-2 control-label">IMDB Votes: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editIMDBVotes" ng-model="movie.movieImdbVotes" ng-change="change('imdbVotes')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.imdbVotesSave && watch.imdbVotesChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editMetascore" class="col-lg-2 control-label">Metascore: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editMetascore" ng-model="movie.movieMetascore" ng-change="change('metascore')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.metascoreSave && watch.metascoreChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <div class="col-lg-2">
        <p>&nbsp;</p>
      </div>
      <div class="col-sm-8">
        <button type="button" name="deleteMovie" ng-click="deleteMovie()" class="form-control btn btn-danger btn-block">Delete Movie</button>
      </div>
      <div class="col-lg-2">
        <p>&nbsp;</p>
      </div>
    </div>
  </form>

</div>
```

The ```ShowController,js``` controller should look like this now:

```js
app.controller('ShowController', ['$scope', '$routeParams', '$location', '$firebaseObject', '$timeout', function($scope, $routeParams, $location, $firebaseObject, $timeout) {
  console.log("Show controller.");

  // Initialize variables
  $scope.watch = {
    titleSave: false,
    titleChange: false,
    posterSave: false,
    posterChange: false,
    plotSave: false,
    plotChange: false,
    triviaSave: false,
    triviaChange: false,
    directorSave: false,
    directorChange: false,
    writerSave: false,
    writerChange: false,
    actorsSave: false,
    actorsChange: false,
    yearSave: false,
    yearChange: false,
    countrySave: false,
    countryChange: false,
    languageSave: false,
    languageChange: false,
    genreSave: false,
    genreChange: false,
    ratedSave: false,
    ratedChange: false,
    awardsSave: false,
    awardsChange: false,
    imdbRatingSave: false,
    imdbRatingChange: false,
    imdbVotesSave: false,
    imdbVotesChange: false,
    metascoreSave: false,
    metascoreChange: false
  };

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com");
  $scope.movie = $firebaseObject(ref.child($routeParams.id));

  // Shows and hides Saved! message
  $scope.change = function(prop) {
    $scope.movie.$save();
    $scope.watch[prop + 'Change'] = true; // Show message
    $timeout(function() {
      $scope.watch[prop + 'Change'] = false; // Hide message
    }, 9900);
  };

  // Watch movie object title property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieTitle')).$watch(function(event) {
    $scope.watch.titleSave = true; // Show message
    $timeout(function() {
      $scope.watch.titleSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object poster property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('moviePoster')).$watch(function(event) {
    $scope.watch.posterSave = true; // Show message
    $timeout(function() {
      $scope.watch.posterSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object plot property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('moviePlot')).$watch(function(event) {
    $scope.watch.plotSave = true; // Show message
    $timeout(function() {
      $scope.watch.plotSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object trivia property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieTrivia')).$watch(function(event) {
    $scope.watch.triviaSave = true; // Show message
    $timeout(function() {
      $scope.watch.triviaSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object director property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieDirector')).$watch(function(event) {
    $scope.watch.directorSave = true; // Show message
    $timeout(function() {
      $scope.watch.directorSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object writer property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieWriter')).$watch(function(event) {
    $scope.watch.writerSave = true; // Show message
    $timeout(function() {
      $scope.watch.writerSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object actors property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieActors')).$watch(function(event) {
    $scope.watch.actorsSave = true; // Show message
    $timeout(function() {
      $scope.watch.actorsSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object year property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieYear')).$watch(function(event) {
    $scope.watch.yearSave = true; // Show message
    $timeout(function() {
      $scope.watch.yearSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object country property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieCountry')).$watch(function(event) {
    $scope.watch.countrySave = true; // Show message
    $timeout(function() {
      $scope.watch.countrySave = false; // Hide message
    }, 9900);
  });

  // Watch movie object language property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieLanguage')).$watch(function(event) {
    $scope.watch.languageSave = true; // Show message
    $timeout(function() {
      $scope.watch.languageSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object genre property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieGenre')).$watch(function(event) {
    $scope.watch.genreSave = true; // Show message
    $timeout(function() {
      $scope.watch.genreSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object rated property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieRated')).$watch(function(event) {
    $scope.watch.ratedSave = true; // Show message
    $timeout(function() {
      $scope.watch.ratedSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object awards property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieAwards')).$watch(function(event) {
    $scope.watch.awardsSave = true; // Show message
    $timeout(function() {
      $scope.watch.awardsSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object IMDB rating property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieIMDBRating')).$watch(function(event) {
    $scope.watch.imdbRatingSave = true; // Show message
    $timeout(function() {
      $scope.watch.imdbRatingSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object IMDB votes property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieIMDBVotes')).$watch(function(event) {
    $scope.watch.imdbVotesSave = true; // Show message
    $timeout(function() {
      $scope.watch.imdbVotesSave = false; // Hide message
    }, 9900);
  });

  // Watch movie object metascore property, show "Saved!" message
  $firebaseObject(ref.child($routeParams.id).child('movieMetascore')).$watch(function(event) {
    $scope.watch.metascoreSave = true; // Show message
    $timeout(function() {
      $scope.watch.metascoreSave = false; // Hide message
    }, 9900);
  });

  // Delete movie
  $scope.deleteMovie = function() {
    $scope.movie.$remove().then(function() {
      console.log("Movie deleted.");
      $location.path( "/movies" );
    }, function(error) {
      console.log("Error, movie not deleted.");
      console.log(error);
    });
  };

}]);
```

## Home Page Styling

Let's leave the ```SHOW/EDIT``` page and work on the ```INDEX/NEW``` or home page.

### Title Font

First, we need a better font! Google "movie fonts" and you'll see many choices. I like the "Terminator" font so I downloaded ```Terminator.ttf``` to the ```fonts``` directory (in the ```css``` directory). Then we put the font at the top of ```styles.css```, and then use the font in our ```h1``` header:

```css
@font-face {
  font-family: "Terminator";
  src: url("fonts/Terminator.ttf") format("truetype");
}

h1 {
  font-family: Terminator, sans-serif;
  text-shadow: 4px 4px 4px #aaa;
  color: black;
}
```

![Terminator font](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_terminator.png)

In ```home.html```, let's wrap the ```Add a Movie``` search form in a Bootstrap 12-column ```div```:

```html  
<div class="col-lg-12">
  <form class="form-horizontal">
    <input type="text"
    class="form-control addMovie"
    ng-model="movie.movieTitle"
    uib-typeahead="address for address in getLocation($viewValue)"
    typeahead-loading="loadingLocations"
    typeahead-no-results="noResults"
    typeahead-min-length="3"
    typeahead-on-select="onSelect($item)"
    placeholder="Add the worst movie you've seen!"/>
    <span class="glyphicon glyphicon-search form-control-feedback"></span>
    <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
    <div ng-show="noResults">
      <i class="glyphicon glyphicon-remove"></i> No Results Found
    </div>
  </form>
</div>
```

Let's make the placeholder font a little bigger in the ```Add a Movie``` search form, in ```styles.css```:

```css
.addMovie {
  font-size: larger;
}
```

The ```glyphicon-search``` icon needs help:

```css
.glyphicon-search {
  padding-right: 40px;
  color: silver;
  font-size: larger;
}
```

The movie posters are too big and are slightly different sizes. Let's fix this:

```css
.largeMoviePoster {
  width: 16.3%;
  height: 285px;
  padding-top: 3px;
}
```

![Large movie view](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_largeMovie.png)

## Responsive Views

Six movies in a row looks good on my 15" laptop screen, but what if users have smaller screens? We'll set up Bootstrap responsive views in ```home.html```:

```html
<!-- Responsive views -->
<div class="row visible-xs-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="extraSmallMoviePoster" ng-src="{{movie.moviePoster}}" alt="{{movie.movieTitle}}"></a>
  </div>
</div>

<div class="row visible-sm-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="smallMoviePoster" ng-src="{{movie.moviePoster}}" alt="{{movie.movieTitle}}"></a>
  </div>
</div>

<div class="row visible-md-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="mediumMoviePoster" ng-src="{{movie.moviePoster}}" alt="{{movie.movieTitle}}"></a>
  </div>
</div>

<div class="row visible-lg-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="largeMoviePoster" ng-src="{{movie.moviePoster}}" alt="{{movie.movieTitle}}"></a>
  </div>
</div>
```

Then in ```styles.css``` we'll set five movies in a row on medium screens, four movies in a row on small screens, and two movies in a row oin mobile devices:

```css
.extraSmallMoviePoster {
  width: 49%;
}

.smallMoviePoster {
  width: 24.5%;
  height: 276px;
}

.mediumMoviePoster {
  width: 19.5%;
  height: 282px;
}

.largeMoviePoster {
  width: 16.3%;
  height: 285px;
  padding-top: 3px;
}
```

Now you can make you browser window bigger and smaller and see the number of movies in each row change.

## Order By Options

Perhaps users would like some options for ordering the movies? Let's give users a choice of ordering by date added (the default), by IMDb rating, or by year. We'll have forward and reverse ordering for each, so a total of six buttons, in ```home.html```:

```html
<!-- orderBy labels row -->
<div class="row visible-sm-block visible-md-block visible-lg-block">
  <div class="col-sm-4 text-center">
    <h4>Order by Date Added</h4>
  </div>
  <div class="col-sm-4 text-center">
    <h4>Order by Rating</h4>
  </div>
  <div class="col-sm-4 text-center">
    <h4>Order by Year</h4>
  </div>
</div>

<!-- orderBy buttons row, mobile screens -->
<div class="row visible-xs-block">
  <div class="col-xs-12">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'movieDateAdded'; reverse = true">Order By Recently Added</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'movieDateAdded'; reverse = false">Order By First Added</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'movieImdbRating'; reverse = false">Order By Worst First</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'movieImdbRating'; reverse = true">Order By Best First</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'movieYear'; reverse = true">Order By Newest</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'movieYear'; reverse = false">Order By Oldest</button>
  </div>
</div>

<!-- orderBy buttons row, small, medium, and large screens -->
<div class="row visible-sm-block visible-md-block visible-lg-block">
  <div class="col-sm-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'movieDateAdded'; reverse = true">Recent</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'movieDateAdded'; reverse = false">First</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'movieImdbRating'; reverse = false">Worst</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'movieImdbRating'; reverse = true">Best</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'movieYear'; reverse = true">Newest</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'movieYear'; reverse = false">Oldest</button>
  </div>
</div>
```

![Order by buttons](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_order_by.png)

On mobile devices the labels row is hidden. The buttons are full-width and have more verbose labels.

One small, medium, and large screens the buttons are each two columns, with labels above the buttons.

The colors are standard Bootstrap:

* ```default``` is white.
* ```primary``` is dark blue.
* ```success``` is green.
* ```info``` is light blue.
* ```warning``` is yellow-orange.
* ```danger``` is red.

The ```ng-click``` sets what the buttons do when clicked. Note that none run a handler in the controller. Each button sets two variables, ```order``` and ```reverse```. There are three options for ```order```: ```movieDateAdded```, ```movieImdbRating```, or ```movieYear```. We could use ```$id``` instead of ```movieDateAdded``` but the latter is more reliable.

## Tech Notes and Resume

I'm looking for a job so I want to tell hiring managers about this project, and I want them to download my resume. Let's make two buttons. The ```Add a Movie``` field is wider than necessary so let's make that half the window width, and make the buttons quarter-width each:

```html
<div class="row">

  <!-- "Add a Movie" form -->
  <div class="col-sm-6 col-md-6 col-lg-6">
    <form class="form-horizontal">
      <input type="text"
      class="form-control addMovie"
      ng-model="movie.movieTitle"
      uib-typeahead="address for address in getLocation($viewValue)"
      typeahead-loading="loadingLocations"
      typeahead-no-results="noResults"
      typeahead-min-length="3"
      typeahead-on-select="onSelect($item)"
      placeholder="Add the worst movie you've seen!"/>
      <span class="glyphicon glyphicon-search form-control-feedback"></span>
      <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
      <div ng-show="noResults">
        <i class="glyphicon glyphicon-remove"></i> No Results Found
      </div>
    </form>
  </div>

  <div class="col-sm-3 col-md-3 col-lg-3">
    <button type="button" class="btn btn-danger btn-block" ng-click="techNotes = !techNotes">Tech Notes</button>
  </div>

  <div class="col-sm-3 col-md-3 col-lg-3">
    <a target="_self" href="../../resume/T-D-Kehoe.pdf" download="T-D-Kehoe.pdf">
      <button type="button" class="btn btn-danger btn-block">Résumé</button>
    </a>
  </div>

</div>
```

![Tech notes and resume](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_tech_resume.png)

### Tech Notes

The ```Tech Notes``` button switches the variable ```techNotes``` on or off. Let's make a Tech Notes row:

```HTML
<!-- Tech Notes row -->
<div class="row well well-lg" ng-show="techNotes">
  <p class="lead text-justify">AngularFire CRUD app with AngularJS, Firebase, Bootstrap, and async typeahead.</p>
  <p class="text-justify">This web app feels like a native app, i.e., the movies database feels like it's on your computer. In contrast, the <a ng-href="http://www.imdb.com">Internet Movies Database (IMDb)</a> feels like a website developed a long time ago in a galaxy far, far away...</p>
  <p class="text-justify">AngularJS binds data to Firebase, a NoSQL cloud database. There's no back end and no HTTP requests (except for the typeahead). Compared to a conventional MEAN stack web app with a Node/Express/MongoDB back-end, Firebase synchronizes the user's edits with the remote database without the user clicking a "Submit" button. When multiple users use the app at the same time, movies added by one user, or edits made by one user, appear instantly in the other users' apps.</p>
  <p class="text-justify">The "Add a Movie" data entry form uses the UI Bootstrap asynchronous typeahead. Each keystroke makes an HTTP request to the Open Movie Database, which returns ten movie titles. onSelect (clicking a movie title) fires an HTTP GET request for a single movie to the OMDB which returns the movie data. This movie is then pushed locally to the $scope.movies array. This updates the INDEX view almost instantly. Then, running in the background, an HTTP POST adds the movie to the CRUDiest Movies Firebase. Lastly, an HTTP GET request to the CRUDiest Movies Firebase gets all the movies and syncs to $scope. This is the slowest step but runs in the background while the user is doing other things.</p>
  <p class="text-justify">Users can order the movies by date added, rating, and year. The "Order By" buttons have ng-click set up with two values, <i>order</i> and <i>reverse</i>. These values are passed to the $scope and then to ng-repeat.  Adding a new movie resets the order back to date added so that the new movie always appears in the upper left position.</p>
  <p class="text-justify">The app is responsive. The number of movies displayed per row changes from five in the large view, to four in the medium view, three in the small view, and then two on mobile devices.</p>

  <p class="text-justify">The <a ng-href="https://github.com/tdkehoe/CRUDiest-Firebase">repository</a> is on GitHub. I also a wrote tutorial to make the <a ng-href="https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/Firebase.md">CRUDiest Movies Firebase</a></p>
</div>
```

The ```Tech Notes``` are styled with a Bootstrap well, and the text is justified with Bootstrap.

![Tech notes well](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_tech_notes.png)

### Download Resume

We added a button to ```home.html``` for downloading a resume:

```html
<div class="col-sm-3 col-md-3 col-lg-3">
  <a target="_self" href="../../resume/T-D-Kehoe.pdf" download="T-D-Kehoe.pdf">
    <button type="button" class="btn btn-danger btn-block">Résumé</button>
  </a>
</div>
```

You can put your resume in the directory ```resume```, change the filename in the button, and it should download.

I don't like the underline when you hover over the button. Let's get rid of it, in ```styles.css```:

```css
.resume:link {
  text-decoration: none;
}
```

## Highlight Movies

Users may not know that they can click movie posters to go to the ```SHOW/EDIT``` page. Let's highlight each movie in blue on mouse hover, in ```styles.css```:

```css
.extraSmallMoviePoster:hover, .smallMoviePoster:hover, .mediumMoviePoster:hover, .largeMoviePoster:hover {
  outline: 5px solid blue;
}
```

We're using the _group selector_ or comma to apply this style to more than one class. The colon makes these _pseudo-classes_, e.g., ```.largeMoviePoster``` is a class and ```.largeMoviePoster:hover``` is a pseudo-class.

## Likes

The ```INDEX/NEW``` home page is finished, except for authorization/authentication. Let's finish the ```SHOW/EDIT``` view. In ```show.html``` you can add:

```html
<div class="row">
  <div class="likes col-xs-6 col-sm-6 col-md-6 col-lg-6">
    <span>{{movie.movieLikes}}</span>
  </div>
  <div class="col-xs-6 col-sm-6 col-md-6 col-lg-6">
    <form ng-submit="upLike()">
      <button type="submit" class="btn btn-success btn-lg">
        <span class="glyphicon glyphicon-thumbs-up" aria-hidden="true"></span>
      </button>
    </form>
    <form ng-submit="downLike()">
      <button type="submit" class="btn btn-danger btn-lg">
        <span class="glyphicon glyphicon-thumbs-down" aria-hidden="true"></span>
      </button>
    </form>
  </div>
</div>
```

This makes two Bootstrap columns, equal widths. The left column displays the number of likes. The right column has two forms, above and below. Each form is a single button. One has a "thumbs-up" glyphicon and the other has "thumbs-down". Clicking fires handlers for ```$scope.upLike()``` or ```$scope.downLike()```

The buttons look fine but the number is small and in the upper left corner of its column. Let's style that in ```styles.css```:

```css
.likes {
  font-size: 48pt;
  text-align: right;
}
```

![Likes](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_likes.png)

Let's add the handler in ```ShowController.js```:

```js
// Likes section
$scope.upLike = function() {
  $scope.movie.movieLikes += 1;
  $scope.movie.$save().then(function() {
    console.log("Upliked!");
  }, function(error) {
    console.log("Error, movie not upliked.");
    console.log(error);
  });
};

$scope.downLike = function() {
  $scope.movie.movieLikes -= 1;
  $scope.movie.$save().then(function() {
    console.log("Downliked!");
  }, function(error) {
    console.log("Error, movie not downliked.");
    console.log(error);
  });
};
```

Both handlers affect the property ```movieLikes``` on the movie object. ```$scope.upLike()``` uses the ```+=``` _assignment operator_ to increment the variable by one. ```$scope.downLike()``` uses the less common ```-=``` to decrement by one.

Both handlers then use the Firebase method [$save](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-save) to fire three-way data binding.

## Comments

Nested comments are a feature where NoSQL databases shine. With SQL you'd make a table for the comments, with a key column in which each comment has a key to the movie it's associated with. If you make a mistake setting up the database, or if the database gets corrupted, you'll have a table of comments and not know which movie each comment goes to. Other columns in the comments table would have comment text, comment author, and date added.

With NoSQL, comments are an array of comment objects. Each comment object has several properties: comment text, comment author, and date added. The comments array is a property of the movie object. The movie objects are in the array of movies. You have nested arrays of objects. It's easy to set up and there's no danger of comments becoming disassociated from their movies.

In ```show.html``` we'll add a comments row with three groups of elements:

```html
<!-- Comments row -->
<div class="row">
  <div class="col-sm-12 col-md-12 col-lg-12">
    <span ng-click="showComments = !showComments" class="showComments" data-toggle="tooltip" data-placement="top" title="Click to show or hide comments.">
      <ng-pluralize count="movie.movieComments.length"
      when="{'0': '',
      'one': '1 Comment',
      'other': '{} Comments',
      'NaN': ''}">
    </ng-pluralize><br />
  </span>

  <div ng-hide="showComments" ng-repeat="comment in movie.movieComments">
    <div class="row comment img-rounded">
      <div class="col-sm-8 col-md-8 col-lg-8">
        <span class="commentText">{{comment.commentText}}</span>
        <div class="commentAuthor">
          <span>&#151;{{comment.commentAuthor}}, </span>
          <span>{{comment.commentTimestamp | date:'mediumDate'}}</span>
        </div>
      </div>
      <div class="col-sm-4 col-md-4 col-lg-4">
        <form ng-submit="deleteComment(comment)">
          <button type="submit" class="btn btn-danger btn-sm">Delete Comment</button>
        </form>
      </div>
    </div>
  </div>

  <div class="col-sm-12 col-md-12 col-lg-12">
    <form class="form-horizontal" ng-submit="newComment(comment)">
      <div class="form-group">
        <label for="commentText">Your Comment: </label>
        <input type="text" name="commentText" ng-model="comment.commentText" class="form-control" >
      </div>
      <div class="form-group">
        <label for="commentAuthor">Your Name: </label>
        <input type="text" name="commentAuthor" ng-model="comment.commentAuthor" class="form-control" >
      </div>
      <div class="form-group">
        <button type="submit" class="form-control btn btn-info btn-block">Submit Comment</input>
        </div>
      </form>
    </div>
  </div>
</div>
```

The first element group shows or hides comments. It tells you how many comments there are. ```ng-pluralize``` then either displays nothing if there are no comments, displays "1 comment" if there is one comment, and if there is more than one comment the number of comments is displayed, followed by "comments". A tooltip tells users they can click to show or hide the comments.

The second element group displays the comments. The comment text is displayed first, followed by the author and the date. A ```Delete Comment``` button is on the right side. This button fires the handler ```$scope.deleteComment()```.

The third element group has a form for adding a new comment. It has two fields, for the text and the author. It fires the handler ```$scope.newComment()```.

### Add New Comment

Let's add the ```$scope.newComment``` handler in ```ShowController.js```:

```js
// Comments section
$scope.newComment = function(comment) { // full record is passed from the view
  var commentWithDate = {
    commentText: comment.commentText,
    commentAuthor: comment.commentAuthor,
    commentDate: Date.now(),
  };
  $scope.movie.movieComments = $scope.movie.movieComments || [];
  $scope.comment.commentText = null; // needed to prevent autofilling fields
  $scope.comment.commentAuthor = null; // needed to prevent autofilling fields
  $firebaseArray(ref.child($routeParams.id).child('movieComments')).$add(commentWithDate).then(function() {
    console.log("Comment added!");
  }, function(error) {
    console.log("Error, comment not added.");
    console.log(error);
  });
};
```

The first line passes the comment in from the view.

Next, we take the comment and add a date timestamp.

Next, we check if there is a ```movieComments``` array. If not we create an empty array.

Next we have two lines that prevent autofilling the comments forms with the previous comment and author.

Now we have a long line that does a lot of work. We create a new ```$firebaseArray``` for the array of movies.

> Check that ```$firebaseArray``` is injected in ```ShowController.js```:

```js
app.controller('ShowController', ['$scope', '$routeParams', '$location', '$firebaseObject', '$firebaseArray', '$timeout', function($scope, $routeParams, $location, $firebaseObject, $firebaseArray, $timeout) {
  console.log("Show controller.");

  }]);
  ```

Then we drill down into the array of movies to select one movie: ```$firebaseArray(ref.child($routeParams.id)```

Then we drill down into the comments array: ```$firebaseArray(ref.child($routeParams.id).child('movieComments'))```

Then we add our comment to the array: ```$firebaseArray(ref.child($routeParams.id).child('movieComments')).$add(commentWithDate)```

Finally we have a promise that's executed when the comment is added to the array: ```$firebaseArray(ref.child($routeParams.id).child('movieComments')).$add(commentWithDate).then(function() { });
```

We could add new comments with JavaScript methods instead of Firebase methods:

```js
$scope.movie.movieComments.push(commentObject);
```

If we use JavaScript methods Firebase won't assign keys to the objects in the array. If we use the Firebase method ```$add``` then our objects have keys.

## Delete Comments

The handler ```$scope.deleteComment()``` is trickier than other handlers because it's working with nested arrays and objects. Remember that Firebase uses ```.child()``` notation, not JavaScript dot notation, because Firebase objects are not JavaScript objects. Firebase objects have keys.

This is the comments display section of ```show.html```:

```html
<div ng-hide="showComments" ng-repeat="comment in movie.movieComments">
  <div class="row comment img-rounded">
    <div class="col-sm-8 col-md-8 col-lg-8">
      <span class="commentText">{{comment.commentText}}</span>
      <div class="commentAuthor">
        <span>&#151;{{comment.commentAuthor}}, </span>
        <span>{{comment.commentDate | date:'mediumDate'}}</span>
      </div>
    </div>
    <div class="col-sm-4 col-md-4 col-lg-4">
      <form>
        <button ng-click="movie.movieComments.$remove(comment)" class="btn btn-danger btn-sm">Delete Comment</button>
      </form>
    </div>
  </div>
</div>
```

The comments display because Angular is OK with dot notation:

```html
ng-repeat="comment in movie.movieComments"
```

But the button does nothing because Firebase methods can't be attached to dot notation:

```html
ng-click="movie.movieComments.$remove(comment)"
```

What we need is this:

```html
<div ng-hide="showComments" ng-repeat="comment in comments">
  <div class="row comment img-rounded">
    <div class="col-sm-8 col-md-8 col-lg-8">
      <span class="commentText">{{comment.commentText}}</span>
      <div class="commentAuthor">
        <span>&#151;{{comment.commentAuthor}}, </span>
        <span>{{comment.commentDate | date:'mediumDate'}}</span>
      </div>
    </div>
    <div class="col-sm-4 col-md-4 col-lg-4">
      <form>
        <button ng-click="comments.$remove(comment)" class="btn btn-danger btn-sm">Delete Comment</button>
      </form>
    </div>
  </div>
</div>
```

How do we make a ```$scope.comments``` array to hold our movie's comments? Our movie's comments are here:

```js
$scope.movie.movieComments
```

but this doesn't work:

```js
$scope.comments = $scope.movie.movieComments
```

because it creates an array that Firebase methods can't attach to. (Because the array was created with dot notation.)

Here's how we do it, in ```ShowController.js```:

```js
$scope.comments = $firebaseArray(ref.child($routeParams.id).child('movieComments'));
```

Now the button works. If you use ```$firebaseObject``` instead of ```$firebaseArray```:

```js
$scope.comments = $firebaseObject(ref.child($routeParams.id).child('movieComments'));
```

the button deletes all comments, because ```$remove()``` removes the entire object or array that it's attached to. When we use ```$firebaseArray``` then a different method is used. It's confusing that the two methods have the same name. The ```$firebaseArray``` method ```$remove(recordOrIndex)``` removes one object from an array.

What if you want your logic in the controller, not the view? We can change our button to fire a handler, in `show.html`:

```html
<button ng-click="deleteComment(comment)" class="btn btn-danger btn-sm">Delete Comment</button>
```

Then in the `ShowController.js` we add the handler:

```js
$scope.deleteComment = function(comment) {
  $scope.comments.$remove(comment).then(function() {
    console.log("Comment deleted!");
  }, function(error) {
    console.log("Error, comment not deleted.");
    console.log(error);
  });
};
```

We could pass through only the key:

```html
<button ng-click="deleteComment(comment.$id)" class="btn btn-danger btn-sm">Delete Comment</button>
```

and convert the key into the index:

```js
$scope.deleteComment = function(commentID) {
  var commentIndex = $scope.comments.$indexFor(commentID);
  $scope.comments.$remove(commentIndex).then(function() {
    console.log("Comment deleted!");
  }, function(error) {
    console.log("Error, comment not deleted.");
    console.log(error);
  });
};
```

Or we could pass through the comment and then pull out the `$id` in the handler:

```js
$scope.deleteComment = function(comment) {
  var commentIndex = $scope.comments.$indexFor(comment.$id);
  $scope.comments.$remove(commentIndex);
};
```
