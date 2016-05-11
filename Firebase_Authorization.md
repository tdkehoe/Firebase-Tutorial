# Firebase Authorization and Authentication

_Authentication_ is the process of ascertaining if a user is who he or she claims to be.

_Authorization_ is the process of determining if a user if allowed to do something, e.g., view a page or add data to a database.

_Auth_ means either or both authentication or authorization.

"Old school" websites use a login name and a password. Current design uses _OAuth_ for authorization with third-party websites such as Google, Facebook, Twitter, GitHub, etc. Users like the convenience of clicking "Login With Facebook", etc. But [OAuth 2.0 is controversial](https://en.wikipedia.org/wiki/OAuth#Controversy) because enterprise users made the project "more complex, less interoperable, less useful, more incomplete, and most importantly, less secure" in order "to sell consulting services and integration solutions."

Authorization and authentication are headaches if you write the code yourself, as well as security risks if you don't know what you're doing. To solve this problem several authorization and authentication services are available. Firebase includes an one, and this is a major reason for using Firebase.

Setting up Firebase authorization involves these steps:

* Make buttons on the home page for authorization.
* Get tokens from third-parties such as Google or Facebook.
* User-Based Security Rules
* Routing

## Login Anonymously

We'll start with anonymous login. Even if we don't plan to allow this on our ultra-high-security movies database we'll start here because it's easy.

### Enable Anonymous User Authentication

Navigate to your Firebase dashboard and click ```Login & Auth``` in the left column. Select the ```Anonymous``` tab and click to enable anonymous authentication.

## Inject ```$firebaseAuth``` into ```HomeController.js```

In ```HomeController.js``` inject ```$firebaseAuth```:

```js
app.controller('HomeController', ['$scope', '$http', '$route', '$location', '$firebaseArray', '$firebaseAuth', function($scope, $http, $route, $location, $firebaseArray, $firebaseAuth) {
  ...
}]);
```

## Create ```$firebaseAuth``` Object

Make a object for ```$firebaseAuth``` and put it on the $scope:

```js
var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");
$scope.authObj = $firebaseAuth(ref);
```

### ```$scope.login```

In ```HomeController.js``` add a function ```$scope.login```:

```js
$scope.login = function() {
  $scope.authData = null;
  $scope.error = null;

  $scope.authObj.$authAnonymously().then(function(authData) {
    $scope.authData = authData;
    console.log($scope.authData);
  }).catch(function(error) {
    $scope.error = error;
    console.log($scope.error);
  });
};
```

This function creates two new variables in the ```$scope```: ```authData``` and ```error```. The variables start as ```null``` or no value.

We then run the method ```$authAnonymously()``` on our ```$firebaseAuth``` object. It returns (as a promise) data, which we store as ```$scope.authData```.

[Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) can be fulfilled or rejected. When a promise is fulfilled the ```.then(function()){})``` callback is executed. If the promise is rejected, we can handle the error in the ```.then``` function. But it's neater to chain a ```.catch``` callback for error handling. Here we assign the error message to ```$scope.error```.

### Make Login Buttons

Now we'll make a button for anonymous login on ```home.html```:

```html
<div class="row">
  <div class="col-sm-12">
    <button type="button" class="btn btn-info btn-block" ng-click="login()">Login Anonymously</button>
    <p ng-if="authData">Logged in user: <strong>{{ authData.uid }}</strong></p>
    <p ng-if="error">Error: <strong>{{ error }}</strong></p>
  </div>
</div>
```

Refresh the browser and try it out:

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_anonymously.png)

That's ugly but it works.

### Logout Button

Now we need a ```Logout``` button.

Add ```ng-if="!authData"``` to our ```Login Anonymously``` button:

```HTML
<button type="button" class="btn btn-info btn-block" ng-click="login()" ng-if="!authData">Login Anonymously</button>
```

This shows the ```Login``` button when there's no ```authData``` (the user isn't logged in).

Now we can add a ```Logout``` button:

```HTML
<button type="button" class="btn btn-info btn-block" ng-click="auth.$unauth()" ng-if="authData">Logout</button>
```

That doesn't do anything.

Let's make a handler. Change the button to:

```HTML
<button type="button" class="btn btn-info btn-block" ng-click="logout()" ng-if="authData">Logout</button>
```

In ```HomeController.js``` add a ```$scope.logout()``` handler:

```js
$scope.logout = function() {
  console.log("Logging out!");
  $scope.authObj.$unauth();
  console.log($scope.authData)
};
```

That doesn't do anything either. Let's check the auth status of the user with ```$onAuth()```:

```js
$scope.logout = function() {
  console.log("Logging out!");
  $scope.authObj.$unauth();
  $scope.authObj.$onAuth(function(authData) {
    if (authData) {
      console.log("Logged in as:", authData.uid);
    } else {
      console.log("Logged out");
    }
  });
};
```

The console log says that the user is logged out.

Let's clear the ```$scope.authData``` object by adding ```$scope.authData = null;```:

```js
$scope.logout = function() {
  console.log("Logging out!");
  $scope.authObj.$unauth();
  $scope.authObj.$onAuth(function(authData) {
    if (authData) {
      console.log("Logged in as:", authData.uid);
    } else {
      $scope.authData = null;
      console.log("Logged out");
    }
  });
};
```

That works. This suggests that authorization and ```$scope.authData``` are different objects. You can't see the logged in users on your Firebase dashboard. You can't see if a user is logged in on ```$scope.authData```. You can only see if a user is logged in with ```$onAuth()```. In other words, ```$authAnonymously()``` put the ```authData``` on the ```$scope``` but ```unauth()``` doesn't take it off the ```$scope```.

It might be better not to put the authData on the $scope and instead rely directly on authData.

### Authorize Adding a Movie

Let's make it so that only logged in users can add a movie. We'll hide the ```Add a Movie``` data entry field until the user logs in.

Add ```ng-if="authData"``` to the ```Add a Movie``` field, and to the ```glyphicon-search``` icon:

```html
<div class="col-sm-6 col-md-6 col-lg-6">
  <input type="text"
  class="form-control addMovie"
  name="movieTitle"
  ng-model="movie.movieTitle"
  uib-typeahead="address for address in getLocation($viewValue)"
  typeahead-loading="loadingLocations"
  typeahead-no-results="noResults"
  typeahead-min-length="3"
  typeahead-on-select="onSelect($item)"
  placeholder="Add the worst movie you've seen!"
  ng-if="authData"/>
  <span class="glyphicon glyphicon-search form-control-feedback" ng-if="authData"></span>
  <i ng-show="loadingLocations" class="glyphicon glyphicon-refresh"></i>
  <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
  <div ng-show="noResults">
    <i class="glyphicon glyphicon-remove"></i> No Results Found
  </div>
</div>
```

Now let's add a ```Logout``` button alongside the ```Add a Movie``` field. Change the ```Add a Movie``` field to use five columns, and then add a one-column button:

```HTML
<div class="col-sm-1 col-md-1 col-lg-1">
  <button type="button" class="btn btn-info btn-block" ng-click="logout()" ng-if="authData">Logout</button>
</div>
```

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_logout_button.png)

Now let's show the login button when the ```Add a Movie``` field hides. Copy and paste the ```Login Anonymously``` button into the five-column ```div```, below the ```Add a Movie``` field.

```html
<div class="col-sm-5 col-md-5 col-lg-5">
  <input type="text"
  class="form-control addMovie"
  name="movieTitle"
  ng-model="movie.movieTitle"
  uib-typeahead="address for address in getLocation($viewValue)"
  typeahead-loading="loadingLocations"
  typeahead-no-results="noResults"
  typeahead-min-length="3"
  typeahead-on-select="onSelect($item)"
  placeholder="Add the worst movie you've seen!"
  ng-if="authData"/>
  <span class="glyphicon glyphicon-search form-control-feedback" ng-if="authData"></span>
  <i ng-show="loadingLocations" class="glyphicon glyphicon-refresh"></i>
  <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
  <div ng-show="noResults">
    <i class="glyphicon glyphicon-remove"></i> No Results Found
  </div>

  <button type="button" class="btn btn-info btn-block" ng-click="login()" ng-if="!authData">Login Anonymously</button>
</div>

<div class="col-sm-1 col-md-1 col-lg-1">
  <button type="button" class="btn btn-info btn-block" ng-click="logout()" ng-if="authData">Logout</button>
</div>
```

Remove the old login button. Now the interface looks cleaner.

### Adding More Login Services

Let's add buttons for Google, Facebook, Twitter, and e-mail and password. Start by copying and pasting four ```Login Anonymously``` buttons, and changing the labels:

```HTML
<div class="col-sm-5 col-md-5 col-lg-5">
  <input type="text"
  class="form-control addMovie"
  name="movieTitle"
  ng-model="movie.movieTitle"
  uib-typeahead="address for address in getLocation($viewValue)"
  typeahead-loading="loadingLocations"
  typeahead-no-results="noResults"
  typeahead-min-length="3"
  typeahead-on-select="onSelect($item)"
  placeholder="Add the worst movie you've seen!"
  ng-if="authData"/>
  <span class="glyphicon glyphicon-search form-control-feedback" ng-if="authData"></span>
  <i ng-show="loadingLocations" class="glyphicon glyphicon-refresh"></i>
  <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
  <div ng-show="noResults">
    <i class="glyphicon glyphicon-remove"></i> No Results Found
  </div>

  <span ng-if="!authData">Login with: </span>
  <button type="button" class="btn btn-info" ng-click="login()" ng-if="!authData">Google</button>
  <button type="button" class="btn btn-info" ng-click="login()" ng-if="!authData">Facebook</button>
  <button type="button" class="btn btn-info" ng-click="login()" ng-if="!authData">Twitter</button>
  <button type="button" class="btn btn-info" ng-click="login()" ng-if="!authData">GitHub</button>
  <button type="button" class="btn btn-info" ng-click="login()" ng-if="!authData">Anonymously</button>

</div>
```

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_four_buttons.png)

Now we'll set up these third-party providers.

## Get Client ID and Client Secret From Google

Go to your [Google Developers Console](https://console.developers.google.com/apis/library) and click on ```Credentials``` in the left column. Put in your project name, click ```Create```, then ```Create credentials```, then ```OAuth client ID```. Fill out the forms and get your ```Client ID``` and ```Client secret```.

In the field ```Authorized JavaScript origins``` put in ```https://auth.firebase.com```. This is not your app's URL or home page.

In the field ```Authorized redirect URIs``` put in ```https://auth.firebase.com/v2/``` followed by your project name followed by ```/auth/google/callback```. For example, my project name is ```crudiest-firebase``` so the ```Authorized redirect URIs``` is  ```https://auth.firebase.com/v2/crudiest-firebase/auth/google/callback```.

If you don't enter your authorized redirect URI, when you attempt to login you'll get an error message such as ```Given URL is not allowed by the Application configuration: One or more of the given URLs is not allowed by the App's settings.``` This is confusing because your Firebase Dashboard Login & Auth tabs has a field to enter ```Authorized Domains for OAuth Redirects```. This is not the same as ```Authorized redirect URIs```. Firebase automatically creates the ```Authorized Domains for OAuth Redirects``` in its dashboard, you shouldn't need to enter any domains here. In contrast, the ```Authorized redirect URIs``` at the provider (Google, Facebook, etc.) must have a domain entered.

### Save Your Client IDs and Client secrets

Make a new file and call it ```OAuth_keys.txt```. Put it in your project folder at the root level, not in the ```public``` folder. Add the file to your ```.gitignore``` file. You don't want to upload this information to a public GitHub repository.

In this file make a form:

```
GOOGLE

Client ID:

Client secret:

Authorized JavaScript origins:
https://auth.firebase.com

Authorized redirect URIs:
https://auth.firebase.com/v2/MY-APP/auth/google/callback
```

Later we'll add the information for Facebook, GitHub, etc.

### Firebase Login & Auth

Now navigate to your Firebase dashboard and click ```Login & Auth``` in the left column. Select the ```Google``` tab, click to enable Google authentication, and enter your Google Client ID and Google Client Secret.

### $scope.loginGoogle

In ```HomeController.js``` add a ```$scope.loginGoogle``` function:

```js
$scope.loginGoogle = function() {
  $scope.authData = null;
  $scope.error = null;
  $scope.authObj.$authWithOAuthPopup("google").then(function(authData) {
    $scope.authData = authData;
    console.log("Logged in as:", authData.uid);
  }).catch(function(error) {
    console.error("Authentication failed:", error);
  });
};
```

Now in ```home.html``` set the ```ng-click```:

```HTML
<button type="button" class="btn btn-info" ng-click="loginGoogle()" ng-if="!authData.uid">Google</button>
```

Deploy your code, refresh your browser, and the ```Google``` button should open a pop-up window. OK the authorization and you should see the ```Add a Movie``` field, and the console log should say ```Logged in as: ``` followed by the user ID.

### Facebook

Go to [facebook for developers](https://developers.facebook.com/apps) and click ```+ Add a New App```.

Get your ```Facebook App ID``` and ```Facebook App Secret```. Save them in your file ```OAuth-keys.txt```.

In your ```developers.facebook.com```, look in the left column under ```PRODUCT SETTINGS``` for ```Facebook Login```. On this page, look for ```Valid OAuth redirect URIs```.

In this field put in ```https://auth.firebase.com/v2/``` followed by your project name followed by ```/auth/facebook/callback```. For example, my project name is ```crudiest-firebase``` so the ```Authorized redirect URIs``` is  ```https://auth.firebase.com/v2/crudiest-firebase/auth/facebook/callback```.

Save changes and go to the Firebase Dashboard. In ```Login & Auth``` in the ```Facebook``` tab, check the box for ```Enable Facebook Authentication``` and enter your ```Facebook App ID``` and ```Facebook App Secret```.

Set up the button in ```home.html```:

```html
<button type="button" class="btn btn-info" ng-click="loginFacebook()" ng-if="!authData.uid">Facebook</button>
```

In ```HomeController.js``` set up the ```loginFacebook()``` function:

```js
$scope.loginFacebook = function() {
  $scope.authData = null;
  $scope.error = null;
  $scope.authObj.$authWithOAuthPopup("facebook").then(function(authData) {
    $scope.authData = authData;
    console.log("Logged in as:", authData.uid);
  }).catch(function(error) {
    console.error("Authentication failed:", error);
  });
};
```

#### Facebook SDK

Alternatively, Facebook has a _Software Development Kit_ (SDK). The kit has two pieces:

* A JavaScript block that you're told to put in the  ```<body>``` of your ```index.html```. You can put this script into a controller. There is a short code block and a long code block, depending on what features you want.
* HTML code blocks for your view. These give you a button, display the user's login status, etc.

The SDK is easy to use and works well if you're not using Angular. However, the Facebook SDK has an ```authResponse``` object when Firebase has an ```authData``` object. Facebook has a field for ```userID``` when Firebase has a field for ```uid```. We need ```authData.uid``` for make the ```Add a Movie``` field and the ```Logout``` button display. Getting the data from the Facebook SDK into our ```$scope``` in the format we need isn't easy.

### Twitter

Go to [Twitter Apps](https://apps.twitter.com/) and click ```Create New App```.

Enter your app's name, description, website, and the ```Callback URL```. In this field put in ```https://auth.firebase.com/v2/``` followed by your project name followed by ```/auth/twitter/callback```. For example, my project name is ```crudiest-firebase``` so the ```Authorized redirect URIs``` is  ```https://auth.firebase.com/v2/crudiest-firebase/auth/twitter/callback```.

In the ```Keys and Access Tokens``` tab copy your  ```Consumer Key (API Key)``` and ```Consumer Secret (API Secret)``` to your file ```OAuth-keys.txt```.

Go to your Firebase Dashboard. In ```Login & Auth``` in the ```Twitter``` tab, check the box for ```Enable Twitter Authentication``` and enter your ```Twitter API Key``` and ```Twitter App Secret```.

Set up the button in ```home.html```:

```html
<button type="button" class="btn btn-info" ng-click="loginTwitter()" ng-if="!authData.uid">Twitter</button>
```

In ```HomeController.js``` set up the ```loginTwitter()``` function:

```js
$scope.loginTwitter = function() {
  $scope.authData = null;
  $scope.error = null;
  $scope.authObj.$authWithOAuthPopup("twitter").then(function(authData) {
    $scope.authData = authData;
    console.log("Logged in as:", authData.uid);
  }).catch(function(error) {
    console.error("Authentication failed:", error);
  });
};
```

### GitHub

Go to [Developer applications](https://github.com/settings/developers) and click ```Register new application```.

Enter your application's name, Homepage URL, description, and the ```Authorization callback URL```. In this field put in ```https://auth.firebase.com/v2/``` followed by your project name followed by ```/auth/github/callback```. For example, my project name is ```crudiest-firebase``` so the ```Authorized redirect URIs``` is  ```https://auth.firebase.com/v2/crudiest-firebase/auth/github/callback```.

In the ```Keys and Access Tokens``` tab copy your  ```Client ID``` and ```Client Secret``` to your file ```OAuth-keys.txt```.

Go to your Firebase Dashboard. In ```Login & Auth``` in the ```GitHub``` tab, check the box for ```Enable GitHub Authentication``` and enter your ```GitHub Client ID``` and ```GitHub Client Secret```.

Set up the button in ```home.html```:

```html
<button type="button" class="btn btn-info" ng-click="loginGitHub()" ng-if="!authData.uid">GitHub</button>
```

In ```HomeController.js``` set up the ```loginGitHub()``` function:

```js
$scope.loginGitHub = function() {
  $scope.authData = null;
  $scope.error = null;
  $scope.authObj.$authWithOAuthPopup("github").then(function(authData) {
    $scope.authData = authData;
    console.log("Logged in as:", authData.uid);
  }).catch(function(error) {
    console.error("Authentication failed:", error);
  });
};
```

### Complete OAuth2 Code

Here's the ```home.html``` code with all the OAuth2 buttons:

```html
<div class="row">

  <div class="addNewMovie row">
    <form class="form-horizontal" ng-submit="addMovie()" name="newMovie">

      <div class="col-sm-2 col-md-2 col-lg-2">
        <button type="button" class="btn btn-danger btn-block" ng-click="techSummary = !techSummary">Tech Notes</button>
      </div>

      <div class="col-sm-6 col-md-6 col-lg-6">
        <input type="text"
        class="form-control addMovie"
        name="movieTitle"
        ng-model="movie.movieTitle"
        uib-typeahead="address for address in getLocation($viewValue)"
        typeahead-loading="loadingLocations"
        typeahead-no-results="noResults"
        typeahead-min-length="3"
        typeahead-on-select="onSelect($item)"
        placeholder="Add the worst movie you've seen!"
        ng-if="authData"/>
        <span class="glyphicon glyphicon-search form-control-feedback" ng-if="authData"></span>
        <i ng-show="loadingLocations" class="glyphicon glyphicon-refresh"></i>
        <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
        <div ng-show="noResults">
          <i class="glyphicon glyphicon-remove"></i> No Results Found
        </div>

        <span ng-if="!authData">Login with: </span>
        <button type="button" class="btn btn-info" ng-click="loginGoogle()" ng-if="!authData.uid">Google</button>

    <button type="button" class="btn btn-info" ng-click="loginFacebook()" ng-if="!authData.uid">Facebook</button>
    <button type="button" class="btn btn-info" ng-click="loginTwitter()" ng-if="!authData.uid">Twitter</button>
    <button type="button" class="btn btn-info" ng-click="loginGitHub()" ng-if="!authData.uid">GitHub</button>
    <button type="button" class="btn btn-info" ng-click="loginEmail()" ng-if="!authData.uid">E-mail &amp; password</button>

  </div>

  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-info btn-block" ng-click="loginAnon()" ng-if="!authData.uid">Anonymously</button>
    <button type="button" class="btn btn-info btn-block" ng-click="logout()" ng-if="authData.uid">Logout</button>
  </div>

  <div class="col-sm-2 col-md-2 col-lg-2">
    <a target="_self" href="../../resume/T-D-Kehoe.pdf" download="T-D-Kehoe.pdf" class="resume">
      <button type="button" class="btn btn-danger btn-block">Résumé</button>
    </a>
  </div>

</form>
</div>

<!-- Tech Notes row -->
<div class="row well well-lg" ng-show="techSummary">
  <p class="lead text-justify">MEAN stack CRUD app optimized for speed with Angular, Bootstrap, and async typeahead.</p>
  <p class="text-justify">This is two apps, with separate back and front ends. The back end uses Node, Express, and MongoDB and is deployed on Heroku. The data is served to an API as JSON objects. MongoDB is accessed via the lightweight, schemaless Monk Node module.</p>
  <p class="text-justify">The front end uses Angular and Bootstrap and is deployed on Firebase. There are three views: INDEX/NEW (Home), SHOW, and EDIT. The asynchronous typeahead reduces the typing required to add a new movie to a few keystrokes so a separate NEW view isn't needed.</p>
  <p class="text-justify">The asynchronous typeahead is implemented with UI Boostrap. Each keystroke makes an HTTP request to the Open Movie Database, which returns ten movie titles. onSelect (clicking a movie title) fires an HTTP GET request for a single movie to the OMDB which returns the movie data. This movie is then pushed locally to the $scope.movies array. This updates the INDEX view almost instantly. Then, running in the background, an HTTP POST adds the movie to the CRUDiest Movies Database. Lastly, an HTTP GET request to the CRUDiest Movies Database gets all the movies and syncs to $scope. This is the slowest step but happens without the user's awareness.</p>
  <p class="text-justify">The "Order By" buttons have ng-click set up with two values, <i>order</i> and <i>reverse</i>. These values are passed to the $scope and then to ng-repeat.</p>
  <p class="text-justify">The app is responsive. The number of movies displayed per row changes from five in the large view, to four in the medium view, three in the small view, and two on mobile devices.</p>
  <p class="text-justify">On the SHOW page, comments are an array of objects nested in the movie object. This is easy with the NoSQL database but would be more work with an SQL database. The "number of comments display" hides and shows the comments when clicked. A tooltip informs users of this feature. The number of comments pluralizes. The likes/dislikes buttons use glyphicons.</p>
  <p class="text-justify">The <a ng-href="https://github.com/tdkehoe/CRUDiest-Movies-Database-Node-back-end">back end code</a> and <a ng-href="https://github.com/tdkehoe/CRUDiest-Movies-Database-Angular-Front-End">front end code</a> are on GitHub. Tutorials to make the <a ng-href="https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/Node_and_Express.md">back end</a>, the <a ng-href="https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/Angular_CRUD.md">front end</a>, and the <a ng-href="https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/Typeahead.md">asynchronous typeahead</a> are also on GitHub.</p>
</div>

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
  <div class="col-sm-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = '$id'; reverse = true">Order By Recently Added</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = '$id'; reverse = false">Order By Least Recently Added</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'movieImdbRating'; reverse = false">Order By Worst First</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'movieImdbRating'; reverse = true">Order By Best First</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'movieYear'; reverse = true">Order By Newest</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'movieYear'; reverse = false">Order By Oldest</button>
  </div>
</div>

<!-- orderBy buttons row, large screens -->
<div class="row visible-sm-block visible-md-block visible-lg-block">
  <div class="col-sm-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = '$id'; reverse = true">Recent</button>
  </div>
  <div class="col-sm-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = '$id'; reverse = false">Oldest</button>
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

</div>

```

Here's ```HomeController.js``` with all the OAuth2 functions:

```js
app.controller('HomeController', ['$scope', '$http', '$route', '$location', '$firebaseArray', '$firebaseAuth', function($scope, $http, $route, $location, $firebaseArray, $firebaseAuth) {
  console.log("Home controller.");
  $scope.loading = true;

  var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");
  $scope.authObj = $firebaseAuth(ref);
  $scope.movies = $firebaseArray(ref);
  $scope.order = '$id';
  $scope.reverse = true;
  $scope.loading = false;

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
    console.log("Selected!");
    return $http.get('//www.omdbapi.com/?t=' + $item)
    .then(function(response){
      var movie = {
        movieActors: response.data.Actors,
        movieAwards: response.data.Awards,
        movieCountry: response.data.Country,
        movieDirector: response.data.Director,
        movieGenre: response.data.Genre,
        movieLanguage: response.data.Language,
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
        movieLikes: 0
      };
      // reset orderBy so that new movie appears in upper left
      $scope.order = '$id'
      $scope.reverse = true;
      $scope.movies.$add(movie);
      $scope.loading = false;
    });
  };

  $scope.loginAnon = function() {
    $scope.authData = null;
    $scope.error = null;
    $scope.authObj.$authAnonymously().then(function(authData) {
      $scope.authData = authData;
      console.log($scope.authData);
    }).catch(function(error) {
      $scope.error = error;
      console.log($scope.error);
    });
  };

  $scope.loginGoogle = function() {
    $scope.authData = null;
    $scope.error = null;
    $scope.authObj.$authWithOAuthPopup("google").then(function(authData) {
      $scope.authData = authData;
      console.log("Logged in as:", authData.uid);
    }).catch(function(error) {
      console.error("Authentication failed:", error);
    });
  };

  $scope.loginFacebook = function() {
    $scope.authData = null;
    $scope.error = null;
    $scope.authObj.$authWithOAuthPopup("facebook").then(function(authData) {
      $scope.authData = authData;
      console.log("Logged in as:", authData.uid);
    }).catch(function(error) {
      console.error("Authentication failed:", error);
    });
  };

  $scope.loginTwitter = function() {
    $scope.authData = null;
    $scope.error = null;
    $scope.authObj.$authWithOAuthPopup("twitter").then(function(authData) {
      $scope.authData = authData;
      console.log("Logged in as:", authData.uid);
    }).catch(function(error) {
      console.error("Authentication failed:", error);
    });
  };

  $scope.loginGitHub = function() {
    $scope.authData = null;
    $scope.error = null;
    $scope.authObj.$authWithOAuthPopup("github").then(function(authData) {
      $scope.authData = authData;
      console.log("Logged in as:", authData.uid);
    }).catch(function(error) {
      console.error("Authentication failed:", error);
    });
  };

  // Logout

  $scope.logout = function() {
    console.log("Logging out!");
    $scope.authObj.$unauth();
    $scope.authObj.$onAuth(function(authData) {
      if (authData) {
        console.log("Logged in as:", authData.uid);
      } else {
        $scope.authData = null;
        console.log("Logged out");
        console.log($scope.authData);
      }
    });
  };

}]);
```

### E-mail and Password

You might think that e-mail and password auth would be easier than OAuth2 but it's actually more complex. You need to have views and functions for users to:

* Create an account, including checking if the e-mail address is already in use.
* Login.
* Change e-mail address.
* Change password.
* Send a password reset e-mail.
* An admin function to delete accounts.

Firebase provides all these functions.

For the UX/UI design we'll use a _modal_ window.

> A *modal window* is a subordinate window on top of the browser's main window which blocks the user from using the main window until action is taken in the modal window.

### UI Bootstrap Modal Window

A UI Bootstrap modal window requires making:

* An HTML button in the view to open the modal window.
* A template.
* A controller.
* A link in ```index.html``` to hook up the controller.

Start with the button to open the modal window in ```home.html```:

```html
<button type="button" class="btn btn-info" ng-click="openLoginModal('md')" ng-controller="EmailLoginModalCtrl">E-mail &amp; password</button>
```

Make a new file, call it ```emailLoginModalContent.html```, and save it in the directory ```templates```:

```html
<div class="modal-header">
  <h3 class="modal-title">E-mail &amp; Password Login</h3>
</div>

<div class="modal-body">

</div>

<div class="modal-footer">
  <button class="btn btn-warning" type="button" ng-click="cancel()">Close Modal Window</button>
</div>
```

In ```HomeController.js``` we need a handler to open the modal window:

```js
  $scope.openLoginModal = function(size) {
    var modalInstance = $uibModal.open({
      templateUrl: 'javascript/templates/emailLoginModalContent.html',
      controller: 'EmailLoginModalInstanceCtrl',
      size: size
    });
  };
```

The method ```open``` on the ```$uibModal``` service creates an object, called the _modal instance_. The [documentation](https://angular-ui.github.io/bootstrap/) lists 17 properties. Most of these you can leave off and accept the default setting. We will specify just three properties:

* ```templateUrl``` is the path to the template with the modal's content.
* ```controller``` is the controller for the modal instance (the second controller).
* ```size``` is the size of the modal window. The choices are ```'sm'```, ```'md'```, or ```'lg'```.

Note that the modal object looks like a router.

Now we'll make the controller. Call it ```EmailLoginModalInstanceCtrl.js``` and save it in the controllers directory:

```js
app.controller('EmailLoginModalInstanceCtrl', function ($scope, $uibModalInstance) {

  // Close modal window button
  $scope.cancel = function () {
    $uibModalInstance.close();
  };

});
```

We've included a handler to close the modal window.

Finally, in ```index.html``` hook up the controller:

```html
<script type="text/javascript" src="javascript/controllers/EmailLoginModalInstanceCtrl.js"></script>
```

Deploy and refresh, click the button, and the modal window should open:

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/modal_window.png)

#### Modal Content

In ```emailLoginModalContent.html``` add a Bootstrap form for entering an e-mail address and password:

```html
<div class="modal-header">
  <h3 class="modal-title">E-mail &amp; Password Login</h3>
</div>

<div class="modal-body">
  <form>
    <div class="form-group">
      <label for="enterEmail">E-mail address</label>
      <input type="email" ng-model="user.email" class="form-control" id="enterEmail" placeholder="E-mail address">
    </div>
    <div class="form-group">
      <label for="enterPassword">Password</label>
      <input type="password" ng-model="user.password" class="form-control" id="enterPassword" placeholder="Password">
    </div>
    <input type="submit" ng-click="newUser(user)" class="btn btn-default" value="Create New User" />
    <input type="submit" ng-click="loginUser(user)" class="btn btn-default" value="Login" />
    <input type="button" ng-click="reset()" class="btn btn-default" value="Reset" />
  </form>
</div>

<div class="modal-footer">
  <uib-alert ng-repeat="alert in alerts" type="{{alert.type}}" close="closeAlert($index)">{{alert.msg}}</uib-alert>
  <button class="btn btn-warning" type="button" ng-click="cancel()">Close Modal Window</button>
</div>
```
The form has two data entry forms. The first is for the e-mail address. Setting ```type="email"``` verifies that the user entered a correctly formatted e-mail address. Similarly in the second field setting ```type="password"``` hides the password.

The form has three buttons. ```Create New User``` runs the function ```$scope.newUser()```. Note that we're not passing through the email and password, e.g., ```ng-click="newUser(email, password)"``` in the view and ```$scope.newUser = function(email, password) {``` in the controller. We could, it works either way, but the Angular way is to pass data through on the ```$scope```. We'll see an advantage of this in a minute.

The ```Login``` button is similar.

The button ```Reset``` runs the handler ```$scope.reset()```.

#### EmailLoginModalInstanceCtrl

First, add the ```$firebaseAuth``` service to ```EmailLoginModalInstanceCtrl.js```:

```js
app.controller('EmailLoginModalInstanceCtrl', ['$scope', '$uibModalInstance', '$firebaseAuth', function($scope, $uibModalInstance, $firebaseAuth) {
  console.log("EmailLoginModalInstanceCtrl controller.");

  // Close modal window button
  $scope.cancel = function () {
    $uibModalInstance.close();
  };

}]);
```

Now add the Firebase ```ref```:

```js
app.controller('EmailLoginModalInstanceCtrl', ['$scope', '$uibModalInstance', '$firebaseAuth', function($scope, $uibModalInstance, $firebaseAuth) {
  console.log("EmailLoginModalInstanceCtrl controller.");

  var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");

  // Close modal window button
  $scope.cancel = function () {
    $uibModalInstance.close();
  };

}]);
```

Now add the handler ```$scope.newUser```:

```js
app.controller('EmailLoginModalInstanceCtrl', ['$scope', '$uibModalInstance', '$firebaseAuth', function($scope, $uibModalInstance, $firebaseAuth) {
  console.log("EmailLoginModalInstanceCtrl controller.");

  var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");

  // Close modal window button
  $scope.cancel = function () {
    $uibModalInstance.dismiss('cancel');
  };

  // Create new user with e-mail & password
  $scope.user = {};
  $scope.newUser = function(user) {
    ref.createUser({
      email: $scope.user.email,
      password: $scope.user.password
    }, function(error, userData) {
      if (error) {
        console.log("Error creating user:", error);
      } else {
        console.log("Successfully created user account with uid:", userData.uid);
      }
    });
  };

}]);
```

Enter a user e-mail and password. You should see a successful e-mail message in your console.

On success we'll close the modal window:

```js
app.controller('EmailLoginModalInstanceCtrl', ['$scope', '$uibModalInstance', '$firebaseAuth', function($scope, $uibModalInstance, $firebaseAuth) {
  console.log("EmailLoginModalInstanceCtrl controller.");

  var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");

  // Close modal window button
  $scope.cancel = function () {
    $uibModalInstance.close();
  };

  // Create new user with e-mail & password
  $scope.user = {};
  $scope.newUser = function(user) {
    ref.createUser({
      email: $scope.user.email,
      password: $scope.user.password
    }, function(error, userData) {
      if (error) {
        console.log("Error creating user:", error);
      } else {
        console.log("Successfully created user account with uid:", userData.uid);
        $uibModalInstance.close();
      }
    });
  };

}]);
```

Before we close the modal window we should reset the form, so that the previous user's data doesn't show the next time the form is opened:

```js
app.controller('EmailLoginModalInstanceCtrl', ['$scope', '$uibModalInstance', '$firebaseAuth', function($scope, $uibModalInstance, $firebaseAuth) {
  console.log("EmailLoginModalInstanceCtrl controller.");

  var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");

  // Close modal window button
  $scope.cancel = function () {
    $uibModalInstance.close();
  };

  // Create new user with e-mail & password
  $scope.user = {};
  $scope.newUser = function(user) {
    ref.createUser({
      email: $scope.user.email,
      password: $scope.user.password
    }, function(error, userData) {
      if (error) {
        console.log("Error creating user:", error);
      } else {
        console.log("Successfully created user account with uid:", userData.uid);
        $scope.reset();
        $scope.$apply(function() {
          console.log("Applied!");
        });
        $uibModalInstance.close();
      }
    });
  };

}]);
```

You can look at your new users in your Firebase Dashboard in ```Login & Auth``` (left column) under the tab ```Email & Password``` at the bottom of the page.

There's one bug. When you enter something that's not an e-mail address in the form for the e-mail address, then click ```Reset```, the non-address doesn't clear. The form verification allows the reset only to clear valid e-mail addresses.

#### Duplicate User Creation

What happens when we enter the same e-mail address twice, to create duplicate accounts? You'll see in the console: ```Error creating user: Error: The specified email address is already in use.``` OK, but the user should see the error message too. Let's create an alert.

HTML alerts are easy but we'll use a more stylish Bootstrap alert. Dismissible alerts require JavaScript so we have to use UI Bootstrap [alerts](https://angular-ui.github.io/bootstrap/).

Add the alert to ```emailLoginModalContent.html```. Let's put the alert into the ```modal-footer```.

```html
<uib-alert class="alert" ng-repeat="alert in alerts" type="{{alert.type}}" close="closeAlert($index)">{{alert.msg}}</uib-alert>
```

In ```EmailLoginModalInstanceCtrl.js``` we create an array for alerts. Then we'll make a function to dismiss the alert:

```js
// Alerts
$scope.alerts = [];
$scope.closeAlert = function(index) {
  $scope.alerts.splice(index, 1);
};
```

Finally, we call the alert within the error function:

```js
if (error) {
  console.log("Error creating user:", error);
  $scope.alerts = [{
      type: 'danger',
      msg: 'Error: The specified email address is already in use.'
    }];
  $scope.$apply(function() {
    console.log("Applied!");
  });
}
```

We created an array of alerts. When there's an error, we push an alert object into the array. The alert object has two properties, the ```type``` and the  ```message```. We then run ```$scope.apply()``` for the two-way binding to make the alert available in the view.

In the view, we've created an element that displays all the alerts in the array. The alert has a dismiss button. The message is taken from the alert object.

The ```type``` property has the same values as [Bootstrap alerts](http://getbootstrap.com/components/#alerts): ```'success'```, ```'info'```, ```'warning'```, and ```'danger'```. The types must be in quotes.

Let's try it out:

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_alert_duplicate.png)

Click ```Create New User``` a few times to make the alert repeat.

### Login User

Now we'll let the users login.

In ```EmailLoginModalInstanceCtrl.js``` add a function similar to the function to create a new user:

```js
$scope.loginUser = function(user) {
  ref.authWithPassword({
    email: $scope.user.email,
    password: $scope.user.password
  }, function(error, authData) {
    if (error) {
      console.log("Login Failed!", error);
    } else {
      console.log("Authenticated successfully with payload:", authData);
    }
  });
};
```

Run it and you should see in the console ```Authenticated successfully with payload: ``` followed by the auth object.

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_auth_object.png)

But we need to reset and close the modal window after success:

```js
$scope.loginUser = function(user) {
  ref.authWithPassword({
    email: $scope.user.email,
    password: $scope.user.password
  }, function(error, authData) {
    if (error) {
      console.log("Login Failed!", error);
    } else {
      console.log("Authenticated successfully with payload:", authData);
      $scope.reset();
      $scope.$apply(function() {
        console.log("Applied!");
      });
      $uibModalInstance.close();
    }
  });
};
```

The modal window closes but the ```Add a Movie``` form doesn't appear. We need to put the auth object on the ```$scope```:

```js
$scope.loginUser = function(user) {
  ref.authWithPassword({
    email: $scope.user.email,
    password: $scope.user.password
  }, function(error, authData) {
    if (error) {
      console.log("Login Failed!", error);
    } else {
      console.log("Authenticated successfully with payload:", authData);
      $scope.authData = authData;
      console.log($scope.authData.uid);
      $scope.reset();
      $scope.$apply(function() {
        console.log("Applied!");
      });
      $uibModalInstance.close();
    }
  });
};
```

That doesn't work. ```$scope.authData.uid``` prints in the console, then the modal window closes, but in the home window the ```Add a Movie``` form doesn't appear. If we console log ```$scope.authData``` it's undefined, i.e., not available.

The modal window and its controller are on a _child $scope_. Data in the global $scope are available to the child $scope, but data in the child $scope isn't available to the global $scope. Looking in the [UI Bootstrap documentation](https://angular-ui.github.io/bootstrap/), the section ```return``` says

> close(result) (Type: function) - Can be used to close a modal, passing a result.

We'll pass ```authData``` as the ```result```:

```js
// Login user
$scope.loginUser = function(user) {
  ref.authWithPassword({
    email: $scope.user.email,
    password: $scope.user.password
  }, function(error, authData) {
    if (error) {
      console.log("Login Failed!", error);
    } else {
      console.log("Authenticated successfully with payload:", authData);
      $scope.reset();
      $scope.$apply(function() {
        console.log("Applied!");
      });
      $uibModalInstance.close(authData);
    }
  });
};
```

Now we have to execute code in ```HomeController.js``` to put ```authData``` on the global ```$scope```.

```js
// Open modal window
$scope.openLoginModal = function(size) {
  var modalInstance = $uibModal.open({
    templateUrl: 'javascript/templates/emailLoginModalContent.html',
    controller: 'EmailLoginModalInstanceCtrl',
    size: size
  });
  modalInstance.result.then(function(authData){
    $scope.authData = authData;
  });
};
```

The documentation says:

> result (Type: promise) - Is resolved when a modal is closed

After the modal window closes then the promise executes. In the promise we put ```authData``` on the ```$scope```. The ```Add a Movie``` form now shows.

### Login Error Alerts

Let's test a wrong e-mail address. I see in the console ```Login Failed! Error: The specified user does not exist.```

Let's test a wrong password. I see in the console ```Login Failed! Error: The specified password is incorrect.```

We need to alert the user. We could give the user different error messages for wrong e-mail address vs. wrong password, but it's recommended not to give this info to potential hackers. It's better to have a single, ambiguous error message to make life harder for hackers:

```js
// Login user
$scope.loginUser = function(user) {
  ref.authWithPassword({
    email: $scope.user.email,
    password: $scope.user.password
  }, function(error, authData) {
    if (error) {
      console.log("Login Failed!", error);
      $scope.alerts.push({
        type: 'danger',
        msg: 'Error: The specified e-mail address or password is incorrect.'
      });
      $scope.$apply(function() {
        console.log("Applied!");
      });
    } else {
      console.log("Authenticated successfully with payload:", authData);
      $scope.reset();
      $scope.$apply(function() {
        console.log("Applied!");
      });
      $uibModalInstance.close(authData);
    }
  });
};
```

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_auth_object.png)

OK, now everything is working!

### Straighten Up Buttons

Some users will want to change their e-mail address. We'll need to provide an ```Account``` button to open a view to edit the e-mail address. Later we can use the same view for changing the password. Let's put the ```Account``` button next to the ```Logout``` button. The columns in ```home.html``` have gotten confused, let's straighten these up:

```html
<div class="row">

  <!-- Login buttons -->

  <div class="col-sm-8 col-md-8 col-lg-8" ng-if="!authData.uid">
    <span ng-if="!authData">Login with: </span>
    <button type="button" class="btn btn-info" ng-click="loginGoogle()">Google</button>
    <button type="button" class="btn btn-info" ng-click="loginFacebook()">Facebook</button>
    <button type="button" class="btn btn-info" ng-click="loginTwitter()">Twitter</button>
    <button type="button" class="btn btn-info" ng-click="loginGitHub()">GitHub</button>
    <button type="button" class="btn btn-info" ng-click="loginEmail()">E-mail &amp; password</button>
    <button type="button" class="btn btn-info" ng-click="loginAnon()">Anonymously</button>
  </div>

  <!-- When user is logged in -->

  <div class="col-sm-4 col-md-4 col-lg-4" ng-if="authData">
    <form class="form-horizontal" ng-submit="addMovie()" name="newMovie">
      <input type="text"
      class="form-control addMovie"
      name="movieTitle"
      ng-model="movie.movieTitle"
      uib-typeahead="address for address in getLocation($viewValue)"
      typeahead-loading="loadingLocations"
      typeahead-no-results="noResults"
      typeahead-min-length="3"
      typeahead-on-select="onSelect($item)"
      placeholder="Add the worst movie you've seen!"/>
      <span class="glyphicon glyphicon-search form-control-feedback" ng-if="authData"></span>
      <i ng-show="loadingLocations" class="glyphicon glyphicon-refresh"></i>
      <i ng-show="loading" class="glyphicon glyphicon-refresh"></i>
      <div ng-show="noResults">
        <i class="glyphicon glyphicon-remove"></i> No Results Found
      </div>
    </form>
  </div>

  <div class="col-sm-2 col-md-2 col-lg-2" ng-if="authData.uid">
    <button type="button" class="btn btn-info btn-block" ng-click="logout()">Logout</button>
  </div>

  <div class="col-sm-2 col-md-2 col-lg-2" ng-if="authData.uid">
    <button type="button" class="btn btn-info btn-block" ng-click="logout()">Account</button>
  </div>

  <!-- Tech notes and resume -->

  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-danger btn-block" ng-click="techSummary = !techSummary">Tech Notes</button>
  </div>

  <div class="col-sm-2 col-md-2 col-lg-2">
    <a target="_self" href="../../resume/T-D-Kehoe.pdf" download="T-D-Kehoe.pdf" class="resume">
      <button type="button" class="btn btn-danger btn-block">Résumé</button>
    </a>
  </div>

</div>
```

Here's the view when the user isn't logged in:

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_buttons.png)

Here's the view when the user is logged in:

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/login_logged_in.png)

### Change E-mail Address

We'll make another modal window for updating the user's e-mail address and password.

Set the ```Account``` button to open the user's account forms:

```html
<div class="col-sm-2 col-md-2 col-lg-2" ng-show="authData.uid">
  <button type="button" class="btn btn-info btn-block" ng-click="openAccountModal('md')">Account</button>
</div>
```

We'll start by showing the user's e-mail address. In ```accountModalContent.html```:

```html
<div class="modal-header">
  <h3 class="modal-title">Update Account Settings</h3>
</div>

<div class="modal-body">

</div>

<div class="modal-footer">
  <uib-alert ng-repeat="alert in alerts" type="{{alert.type}}" close="closeAlert($index)">{{alert.msg}}</uib-alert>
  <button class="btn btn-warning" type="button" ng-click="cancel()">Close Modal Window</button>
</div>
```

Make a controller ```AccountModalInstanceCtrl.js``` and save it in the controllers folder:

```js
app.controller('AccountModalInstanceCtrl', ['$scope', '$uibModalInstance', '$firebaseAuth', function($scope, $uibModalInstance, $firebaseAuth) {
  console.log("AccountModalInstanceCtrl controller.");

  var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");
  $scope.authObj = $firebaseAuth(ref);

  // Close modal window button
  $scope.cancel = function () {
    $uibModalInstance.close();
  };

  // Reset button
  $scope.master = {};
  $scope.reset = function() {
    console.log("Resetting!");
    angular.copy($scope.master, $scope.user);
  };

  // Alerts
  $scope.alerts = [];
  $scope.closeAlert = function(index) {
    $scope.alerts.splice(index, 1);
  };

}]);
```

Lastly, hook up the controller in ```index.html```:
```html
<script type="text/javascript" src="javascript/controllers/AccountModalInstanceCtrl.js"></script>
```

Let's make tabs in the modal window body:

```html
<div class="modal-header">
  <h3 class="modal-title">Update Account Settings</h3>
</div>

<div class="modal-body">

  <p>Your e-mail address: <strong>{{authData.password.email}}</strong></p>

  <uib-tabset>
    <uib-tab heading="Update E-mail Address">
    </uib-tab>
    <uib-tab heading="Update Password">
    </uib-tab>
    <uib-tab heading="Reset Lost Password">
    </uib-tab>
  </uib-tabset>

</div>

<div class="modal-footer">
  <uib-alert ng-repeat="alert in alerts" type="{{alert.type}}" close="closeAlert($index)">{{alert.msg}}</uib-alert>
  <button class="btn btn-warning" type="button" ng-click="cancel()">Close Modal Window</button>
</div>
```

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/modal_account_update.png)

The user's e-mail address isn't showing up because ```authData``` isn't available to the modal window. We'll use the AngularFire method ```$getAuth()``` to get the current ```authData``` object:

```js
app.controller('AccountModalInstanceCtrl', ['$scope', '$uibModalInstance', '$firebaseAuth', function($scope, $uibModalInstance, $firebaseAuth) {
  console.log("AccountModalInstanceCtrl controller.");

  var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");
  $scope.authObj = $firebaseAuth(ref);

  var authData = $scope.authObj.$getAuth();
  $scope.authData = authData;

  if (authData) {
    console.log("Logged in as:", authData.uid);
  } else {
    console.log("Logged out");
  }

  ...

}]);
```

If this works then take out the code block

```js
if (authData) {
  console.log("Logged in as:", authData.uid);
} else {
  console.log("Logged out");
}
```

![Atom HTML](/Users/TDK/playground/BreakingStuff/media/modal_account_email.png)

Let's put in a form for changing your e-mail address:

```html
<div class="modal-header">
  <h3 class="modal-title">Update Account Settings</h3>
</div>

<div class="modal-body">

  <p>Your e-mail address:<strong>{{authData.password.email}}</strong></p>

  <uib-tabset>
    <uib-tab heading="Update E-mail Address">
    </uib-tab>
    <uib-tab heading="Update Password">
    </uib-tab>
    <uib-tab heading="Reset Lost Password">
    </uib-tab>
  </uib-tabset>

  <form>
    <div class="form-group">
      <label for="changeEmail">New e-mail address: </label>
      <input type="email" ng-model="user.email" class="form-control" id="changeEmail" placeholder={{authData.password.email}}>
    </div>
    <div class="form-group">
      <label for="enterPassword">Enter password: </label>
      <input type="password" ng-model="user.password" class="form-control" id="enterPassword" placeholder="Password">
    </div>
    <input type="submit" ng-click="updateAddress(user)" class="btn btn-default" value="Update E-mail Address" />
    <input type="button" ng-click="reset()" class="btn btn-default" value="Reset" />
  </form>

</div>

<div class="modal-footer">
  <uib-alert ng-repeat="alert in alerts" type="{{alert.type}}" close="closeAlert($index)">{{alert.msg}}</uib-alert>
  <button class="btn btn-warning" type="button" ng-click="cancel()">Close Modal Window</button>
</div>
```

Now we'll make the handler in ```AccountModalInstanceCtrl.js```:

```js
// Update e-mail address
$scope.updateAddress = function(user) {
  ref.changeEmail({
    oldEmail : $scope.authData.password.email,
    newEmail : user.email,
    password : user.password
  }, function(error) {
    if (error === null) {
      console.log("Email changed successfully");
    } else {
      console.log("Error changing email:", error);
    }
  });
};
```

That worked, according to the console log and the Firebase Dashboard's list of users. But the user needs to be told that it worked. We've alerady put an alert in ```accountModalContent.html```:

```html
<uib-alert ng-repeat="alert in alerts" type="{{alert.type}}" close="closeAlert($index)">{{alert.msg}}</uib-alert>
```

We need to put the alert into the handler. Let's also put in an alert for success:

```js
// Update e-mail address
$scope.updateAddress = function(user) {
  ref.changeEmail({
    oldEmail : $scope.authData.password.email,
    newEmail : user.email,
    password : user.password
  }, function(error) {
    if (error === null) {
      console.log("Email changed successfully");
      $scope.alerts = [{
          type: 'success',
          msg: 'E-mail changed successfully'
        }];
      $scope.$apply(function() {
        console.log("Applied!");
      });
    } else {
      console.log("Error changing email:", error);
      console.log(error)
      $scope.alerts = [{
          type: 'danger',
          msg: 'Incorect password'
        }];
      $scope.$apply(function() {
        console.log("Applied!");
      });
    }
  });
};
```

### Change Password

We'll copy the ```Change Password``` form from the ```Change E-mail Address``` form and make a few changes:

```html
<uib-tab heading="Update Password">
  <form>
    <div class="form-group">
      <label for="changePassword">Old password: </label>
      <input type="password" ng-model="user.oldPassword" class="form-control" id="changePassword" placeholder="Old password">
    </div>
    <div class="form-group">
      <label for="enterPassword">New password: </label>
      <input type="password" ng-model="user.newPassword" class="form-control" id="enterPassword" placeholder="New password">
    </div>
    <input type="submit" ng-click="updatePassword(user)" class="btn btn-default" value="Update Password" />
    <input type="button" ng-click="reset()" class="btn btn-default" value="Reset" />
  </form>
</uib-tab>
```

We copy the handler too:

```js
// Change password

$scope.updatePassword = function(user) {
  ref.changePassword({
    email : $scope.authData.password.email,
    oldPassword : user.oldPassword,
    newPassword : user.newPassword
  }, function(error) {
    if (error === null) {
      console.log("Password changed successfully");
      $scope.changePassword = false;
      $scope.alerts.push({
        type: 'success',
        msg: 'Success! Password updated.'
      });
      $scope.$apply(function() {
        console.log("Applied!");
      });
    } else {
      console.log("Error changing password:", error);
      $scope.alerts = [{
          type: 'danger',
          msg: error
        }];
      $scope.$apply(function() {
        console.log("Applied!");
      });
    }
  });
};
```

### Reset Lost Password

OK, one last function! Firebase provides a function for sending the user an e-mail to reset a lost password.

No inputs are needed, just a button and a handler. The button doesn't need to hide or show anything, just run the handler:

```html
<input type="button" ng-click="resetNewPassword()" class="btn btn-default" value="Reset lost password" />
```

And the handler:

```js
// Reset lost password
$scope.resetNewPassword = function(user) {
  ref.resetPassword({
    email : $scope.authData.password.email
  }, function(error) {
    if (error === null) {
      console.log("Password reset email sent successfully");
      $scope.alerts.push({
        type: 'success',
        msg: 'Password reset email sent successfully.'
      });
      $scope.$apply(function() {
        console.log("Applied!");
      });
    } else {
      console.log("Error sending password reset email:", error);
      $scope.alerts = [{
        type: 'danger',
        msg: error
      }];
      $scope.$apply(function() {
        console.log("Applied!");
      });
    }
  });
};
```

Save to GitHub.

### Stay Logged In When Changing Views

Click on a movie poster to go to the ```SHOW/EDIT``` view, then return to the ```INDEX/NEW``` (home) page. You're no longer logged in.

To fix this, in ```HomeController.js``` below ```$scope.authObj = $firebaseAuth(ref);``` add:

```js
var authData = $scope.authObj.$getAuth();
$scope.authData = authData;
```

The Firebase method ```$getAuth()``` gets the ```authData``` object. We then put the data object on the ```$scope``` to make it available for the view to hide and show elements.

### Routing With Auth

The whole point of authorization and authentication is to allow logged-in users to access certain pages, and block users who aren't logged-in. We do this in the router. (We don't do this in the controllers because it's a lot of code, and because the page may flash into view briefly while the controller is running code.)

We're using ```ngRoute```, Angular's official router, which doesn't have auth support. I.e., out of the box Angular doesn't have a way to access or block authorized routes. ```ui-router``` is the other popular router, and it doesn't have auth support either. Luckily [Firebase provides two helper methods for route authorization](https://www.firebase.com/docs/web/libraries/angular/guide/user-auth.html#section-routers):

* ```$waitForAuth()``` gets the authentication state before the route is rendered. We'll use this for the home page, although it's not necessary.
* ```$requireAuth()``` is used when we want to require a route to have an authenticated user, and redirect unauthenticted users somewhere else. We'll use this for the ```SHOW/EDIT``` page.

First we're going to make a factory. A _factory_ is the other type of Angular modular or  _service_. We've used services written by other people, such as ```ngRoute```, ```$firebaseObject```, and various UI Bootstrap services. We haven't written a service so this will be our first service.

Services create objects. There are two types of services. A _service_ creates a unique object. A _factory_ creates many, identical objects.

Here is our factory:

```js
app.factory("Auth", ["$firebaseAuth", function($firebaseAuth) {
  var ref = new Firebase("https://my-firebase.firebaseio.com");
  return $firebaseAuth(ref);
}]);
```

Create a new directory ```services``` in your directory ```javascript```. Save the factory as ```authFactory.js```. Change the Firebase URL to your Firebase URL.

All our factory does is create objects called ```Auth``` by connecting to Firebase and getting the auth object. We've done this before in controllers.

In ```routes.js``` add three code blocks:

```js
app.config(function($routeProvider) {

  $routeProvider
  .when('/movies', { // INDEX
    templateUrl: 'javascript/templates/home.html',
    controller: 'HomeController'
  })
  .when('/movies/:id/edit', { // UPDATE
    templateUrl: 'javascript/templates/edit.html',
    controller: 'EditController'
  })
  .when('/movies/:id', { // SHOW
    templateUrl: 'javascript/templates/show.html',
    controller: 'ShowController',
    resolve: {
      "currentAuth": ["Auth", function(Auth) {
        return Auth.$requireAuth();
      }]
    }
  })
  .otherwise({ redirectTo: '/movies' });
});

app.run(function($rootScope, $location){
  $rootScope.$on('$routeChangeError', function(event, next, previous, error){
    // We can catch the error thrown when the $requireAuth promise is rejected
    // and redirect the user back to the home page
    if (error === "AUTH_REQUIRED"){
      $location.path('/');
    }
  })
});
```

The ```resolve``` property for ```show.html``` gets an ```Auth``` object from the factory and checks if the user is logged in.

The ```$routeChangeError``` function sends the user back to the home page if he or she isn't logged in.

(We're planning to remove the ```EDIT``` route.)

You can run this, it should work.



## Security & Rules

Firebase also has security to protect your remote database from hackers. In your Firebase Deashboard, in the left column look for ```Security & Rules```. If you have a red exclamation point, it's alerting you that you should specify your database security rules.

Make a file called ```SecurityRules.json``` and put it in your project root, i.e., _not_ in your ```public``` folder.

In your Firebase Deshboard ```Security & Rules``` section, you should see the default rules:

```js
{
    "rules": {
        ".read": true,
        ".write": true
    }
}
```

That says that your data can be read or written, by anyone. A third rule is also available, ```.validate```, which could check if e-mail addresses are correctly formatted or limit comments to 165 characters, etc.

Rules have various variables. We'll use ```auth```, which is the auth object. We'll start with a simple rule allowing anyone to read our website but only logged-in users have write access:

```js
{
  "rules": {
    // public read access
    ".read": true,
    // only logged-in users hve write access
    ".write": "auth != null"
  }
}
```

Ww can refine that further to require a user ID:

```js
{
  "rules": {
    // public read access
    ".read": true,
    // only logged-in users have write access
    ".write": "auth.uid != null"
  }
}
```

Let's allow only anonymously logged-in users to edit data:

```js
{
  "rules": {
    // public read access
    ".read": true,
    // only anonynously logged-in users have write access
    ".write": "auth.provider === 'anonymous'"
  }
}
```

Try logging in anonymously and changing data, then log out, log in with one of the OAuth2 providers, and try to change data.

Let's put it back to ```".write": "auth.uid != null"```. The [Firebase documentation](https://www.firebase.com/docs/security/guide/) goes into detail about the many rules you can add. For example, we could allow users to only edit movies they've added.
