# Setting Up Firebase With Angular

In this chapter you'll set up Firebase and Angular.

An overview and directory for the entire project is in the [README.md](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/README.md).

All the code is provided so you can cut-and-paste if you're impatient. But I recommend typing every character. The top student in my class at the Galvanize coding bootcamp told me that he never cuts-and-pastes, he always types everything out. And after he finished a project he'd "rm -rf" (delete) the project and make it again.

## Table of Contents

* [Set Up Your Directory Structure](## Set Up Your Directory Structure)
* [Open A Firebase Account](## Open A Firebase Account)
* [Set Up `index.html`](## Set Up `index.html`)
* [Set Up Angular](## Set Up Angular)

## Set Up Your Directory Structure

Set up this directory structure:

```
── CRUDiest-Movies-Firebase
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
        └── scripts
            └── angular-ui
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
mkdir scripts
cd scripts
mkdir angular-ui
cd ..
cd ..
tree
```

## Open a Firebase Account

If you haven't already, [sign up for a free Firebase account](https://www.firebase.com/).

Create an app, then follow the instructions to install `firebase-tools` from the command line (CLI):

```
npm install -g firebase-tools
```

This installs `firebase-tools` globally so you don't have to do this for every project.

Deploy your app from your project root directory, i.e., from `CRUDiest-Movies-Firebase`:

```
firebase init
```

This creates a `firebase.json` file. It should look similar to this:

Your `firebase.json` file should look something like this:

```js
{
  "firebase": "my-firebase",
  "public": "public",
  "ignore": [
    "firebase.json",
    "**/.*",
    "**/node_modules/**"
  ]
}
```

Then when you write code run

```
firebase deploy
```

and refresh your browser to see your changes.

> If your changes don't seem to take effect then your browser may be cacheing your web app or its data. [Clear Chrome's cache](https://support.google.com/chrome/answer/95582?hl=en) or use the Chrome browser's "Incognito" mode.

### GitHub Repository

In your GitHub account, click the `+` in the upper right corner to create a new repository. Name it `CRUDiest Movies Firebase`. Don't check the box for "Initialize this repository with a README".

GitHub will give you instructions to run in `CRUDiest-Movies-Firebase`:

```
echo "# CRUDiest-Movies-Firebase" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:myAccount/CRUDiest-Movies-Firebase.git
git push -u origin master
```

Use the code provided by GitHub, not the above code, or change `myAccount` to your GitHub account name.

## Set Up `index.html`

Open the file `index.html` in your text editor. If you're using Atom, type `html` followed by the `TAB` key to autofill a snippet.

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

Change line 2 from `<html>` to

```html
<html lang="en" ng-app="CRUDiestMoviesFirebase">
```

This declares that this will be an Angular app, and sets the name of the app. `ng` is short for _Angular_.

Below `<meta charset="utf-8">` add three more `<meta>` tags:

```html
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="description" content="Firebase CRUD app with AngularJS, Bootstrap, and asynchronous typeahead.">
```

The first two set up responsive views for Bootstrap. The third is used by search engines to describe your website.

Change `<title></title>` to `<title>CRUDiest Movies Firebase</title>`.

Hook up the style sheet in the `<head>` section:

```html
<link rel="stylesheet" href="css/style.css">
```

In the `<body` section add a text header and then hook up the JavaScript files:

```html
<h1>CRUDiest Movies Firebase</h1>

<script type="text/javascript" src="app.js"></script>
<script type="text/javascript" src="javascript/routes/routes.js"></script>
<script type="text/javascript" src="javascript/controllers/HomeController.js"></script>
<script type="text/javascript" src="javascript/controllers/ShowController.js"></script>
```

### Content Delivery Networks (CDNs)

To use AngularFire we need AngularJS, Firebase, and AngularFire. For this project we'll also need the Angular router, Bootstrap, and UI Bootstrap. Install these content delivery networks (CDN) in the `<head>` section of `index.html`;

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

The `Bootstrap CSS` requires that you download your own copy with a complete hash number (starting with `sha384-`.

Update the version numbers:
1. Angular: https://angularjs.org/ (Don't use Angular 2, it's a different framework.)
2. Bootstrap CSS: http://getbootstrap.com/
3. UI Bootstrap: https://angular-ui.github.io/bootstrap/
4. Firebase: https://www.firebase.com/docs/web/changelog.html
5. AngularFire: https://www.firebase.com/docs/web/libraries/angular/changelog.html

Alternatively you can [download Angular](https://angularjs.org/) and the other libraries and link to them locally. That's more reliable as you can use your app without an Internet connection. The CDN is easier to install and update. To update it you just change the version number.

### Bootstrap and Angular

Bootstrap starts with a container in `index.html`:

```html
<div class="container">
  <h1>CRUDiest Movies Database</h1>
</div>
```

Angular uses `<ng-view />` to make the template views appear:

```html
<div class="container">
  <h1>CRUDiest Movies Database</h1>
  <ng-view />
</div>
```

Let's center the `<h1>` header:

```html
<div class="container">
  <div class="row text-center">
    <h1>CRUDiest Movies Database</h1>
  </div>
  <ng-view />
</div>
```

Let's make a backlink so that clicking on the title takes the user back to the home page. In Angular we use `ng-href` instead of `href`:

```html
<a ng-href="/#/movies"><h1>CRUDiest Movies Database</h1></a>
```

### Finish index.html

Your `index.html` should now look like:

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
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-..." crossorigin="anonymous">
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

Deploy to Firebase from your command line:

```
firebase deploy
```

Open your browser to the URL that Firebase prints after you run `firebase deploy`. Use the `URL` URL, not the `Dashboard` URL

You should see your `<h1>` header:

![Image of header](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_header.png?raw=true)

## Set Up Angular

Open your `app.js` file.

Inject `firebase` into your `app.js` Angular module, along with your router:

```js
var app = angular.module("CRUDiestMoviesFirebase", ['ngRoute', 'firebase']);
```

### Set Up Controllers

In `HomeController.js` set up the controller:

```js
app.controller('HomeController', ['$scope', function($scope) {
  console.log("Home controller.");

}]);
```

We injected the dependencies `$scope` to access our local arrays and objects.

In `ShowController.js` set up the same code, changing `Home` to `Show`.

### Creating a Firebase Reference

In each controller we create a Firebase _reference_. You can call it anything you want but the norm is `ref`.

The Firebase reference is an object created with the `new` operator and the Firebase constructor function (_object-oriented programming_  or OOP):

```js
// Create Firebase reference
var ref = new Firebase("https://my-firebase.firebaseio.com/");
```

Replace `https://my-firebase.firebaseio.com/` with your Firebase Dashboard URL. Don't use the URL that ends in `firebaseapp.com`.

The Firebase reference doesn't create a connection to the remote Firebase nor does it download data.

This is the easiest part of Firebase. The syntax never changes and is done once in each controller, near the top.

Your controllers should now look like this:

```js
app.controller('HomeController', ['$scope', function($scope) {
  console.log("Home controller.");

  var ref = new Firebase("https://my-firebase.firebaseio.com/");

}]);
```

and

```js
app.controller('ShowController', ['$scope', function($scope) {
  console.log("Show controller.");

  var ref = new Firebase("https://my-firebase.firebaseio.com/");

}]);
```

### Connect the Firebase Reference To the `$scope`

Now connect the new Firebase `ref` to the `$scope` so that the data is available locally.

In `HomeController.js` inject the dependency `$firebaseArray`:

```js
app.controller('HomeController', ['$scope', '$firebaseArray', function($scope, $firebaseArray) {
  ...
}]);
```

Then make what I call a _synchroray_ or _synchrolizer_ after the Firebase `ref`. I have no idea is there is an official term for these things.

We'll call the array `movies` (plural) because the `INDEX/NEW` view will display all of our movies:

```js
var ref = new Firebase("https://my-firebase.firebaseio.com/");
$scope.movies = $firebaseArray(ref);
```

Your `HomeController.js` should now look like this:

```js
app.controller('HomeController', ['$scope', '$firebaseArray', function($scope, $firebaseArray) {
  console.log("Home controller.");

  var ref = new Firebase("https://my-firebase.firebaseio.com/");
  $scope.movies = $firebaseArray(ref);

}]);
```

Make the `ShowController.js` similar but inject `$firebaseObject` instead of `$firebaseArray`. Then make a _synchroject_ using `firebaseObject`. Call the local array `movie` (singular), as the `SHOW/EDIT` view will display only one movie:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', function($scope, $firebaseObject) {
  console.log("Show controller.");

  var ref = new Firebase("https://my-firebase.firebaseio.com/");
  $scope.movie = $firebaseObject(ref);

}]);
```

## Angular Routes

We'll set up `routes.js` now:

```js
app.config(function($routeProvider) {

  $routeProvider
  .when('/movies', { // INDEX/NEW
    templateUrl: 'javascript/templates/home.html',
    controller: 'HomeController'
  })
  .when('/movies/:id', { // SHOW/EDIT
    templateUrl: 'javascript/templates/show.html',
    controller: 'ShowController'
  })
  .otherwise({ redirectTo: '/movies' });
});
```

The first line injects the `$routeProvider` service to the `app` object. Each routes starts with `.when` followed by a route:

* `/movies` is the home page or `INDEX` route, where all records are displayed. We'll combine this with the `NEW` route.
* `/movies/:id` displays one movie, dynamically identified by its ID number, called the `SHOW` route. We'll combine this with the `EDIT` route.

The `.otherwise` route redirects any other requests to the home page.

The order of the routes matters. The `/movies/:id` route has to be at the bottom because it catches other routes.

### Save and Commit Your Work

Files changed in this chapter:

```
index.html
app.js
HomeController.js
ShowController.js
routes.js
```

Deploy to Firebase:

```
firebase deploy
```

and save to GitHuB:

```
git status
git add .
git commit -m "Finished index.html and set up Angular."
git push origin master
```
