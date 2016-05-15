# Async Typeahead

In this chapter you'll add UI Bootstrap's async typeahead.

An overview and directory for the entire project is in the [README.md](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/README.md).

## Table of Contents

1. [Set Up Bootstrap](## Set Up Bootstrap)
2. [UI Bootstrap CDN](## UI Bootstrap CDN)
3. [App.js Dependency Injection](## App.js Dependency Injection)
4. [Typeahead Plugin](## Typeahead Plugin)
5. [Controller Dependency Injection](## Controller Dependency Injection)
6. [Handlers in Controller](## Handlers in Controller)
7. [Display Movies in Index View (Homepage)](## Display Movies in Index View (Homepage))
8. [Style Movie Posters](## Style Movie Posters)

## Set Up Bootstrap

We'll set up the HTML templates (views) now. In `home.html` start with the Bootstrap `row` container:

```html
<div class="row">

</div>
```

## UI Bootstrap CDN

We need to add some movies.

Let's make a form for adding movies. We could make a form with a dozen inputs for the movie title, poster, actors, director, year, etc. and expect users to type in all this data. Or we can make a _typeahead_ enabling the user to enter one word of a movie title and the typeahead finds the movie in the Open Movies Database (OMDb), then downloads the data.

[UI Bootstrap](https://angular-ui.github.io/bootstrap/) includes a _typeahead_ plugin. UI Bootstrap is the JavaScript plugin library for Angular. Standard Bootstrap JavaScript plugins are [incompatible with Angular](https://scotch.io/tutorials/how-to-correctly-use-bootstrapjs-and-angularjs-together).

We already added the UI Bootstrap CDN to `index.html`. Here's more detailed instructions on this critical step:

The UI Bootstrap CDN must be below the Angular CDN.

You can [find the latest CDN](https://cdnjs.com/libraries/angular-ui-bootstrap). This website gives you a choice of four CDNs:

* Two with _templates_ that start with `ui-bootstrap-tpls`.
* Two without templates, that start with `ui-bootstrap`.
* Two _minified_ files that are small and load quickly, but can't be read by humans, and end in `min.js`.
* Two not minified files that are bigger but humans can read (and change) the code, and end in `js`.

We'll use the CDN with templates, minified:

```HTML
<script  type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/1.2.5/ui-bootstrap-tpls.min.js"></script>
```

Link only one of the four CDNs. Adding a second CDN will cause errors.

> Alternatively you can also download the library from the [UI Bootstrap](https://angular-ui.github.io/bootstrap/) website. There's a big purple button that says `Download`. Move the file into your project folder and make a local link.

## App.js Dependency Injection

Add the dependencies `ui.bootstrap` and `ui.bootstrap.typeahead` to `app.js`:

```js
var app = angular.module("CRUDiestMoviesFirebase", ['ngRoute', 'firebase', `ui.bootstrap`, `ui.bootstrap.typeahead`]);
```

## Typeahead Plugin

Go to [UI Bootstrap](https://angular-ui.github.io/bootstrap/) and scroll down to the bottom. The last plugin is `Typeahead`. You'll see there are five types of typeahead plugins:

![Five typeaheads](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/typeahead_five_options.png)

* Four of the plugins -- `Static arrays`, `ngModelOptions support`, `Custom templates for results`, and `Custom popup templates for typeahead's dropdown` -- look up in an array of values in your controller. This is ideal when you have a limited number of options, e.g., the fifty states. The four choices format the values differently for the user, including one that adds a state flag downloaded live from Wikipedia.
* The `Asynchronous results` plugin goes to any database on the Internet with an API. We'll use this to connect to the Internet Movie Database (IMDB).

Add this code to `home.html`.

```html
<form class="form-horizontal">
  <input type="text"
  class="form-control addMovie"
  ng-model="movie.title"
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

This form is styled as Bootstrap class `form-horizontal`. The input is a text entry field styled as Bootstrap class `form-control`. We add a class `addMovie` for additional styling.

We connect the form to the `$scope` with `ng-model` set to the movie title.

The next five lines set the [UI Bootstrap](https://angular-ui.github.io/bootstrap/) typeahead parameters.

The placeholder tells the user what to do.

The `search` glyphicon puts a search icon in the box.

The `refresh` glyphicon tells the user that data is being downloaded.

Lastly, the `remove` glyphicon tells the user that no movie was found.

## Controller Dependency Injection

In `HomeController.js` inject the dependency `$http`:

```js
app.controller('HomeController', ['$scope', `$http`, '$firebaseArray', function($scope, $http, $firebaseArray) {
  console.log("Home controller.");

}]);
```

The handler `$scope.getLocation` uses the `$http` service to send an HTTP request to the Open Movies Database (OMDb).

## Handlers in Controller

In `HomeController.js` we'll add two handlers for the typeahead:

```js
$scope.getLocation = function(val) {
  return $http.get('//www.omdbapi.com/?s=' + val) // send an HTTP request to the OMDb
  .then(function(response){ // then execute a promise
    return response.data.Search.map(function(item){ // when OMDb can't find a movie to match the search string an error is logged "TypeError: Cannot read property 'map' of undefined". This error can be ignored, when the OMDb finds a movie then no error is logged.
      return item.Title;
    });
  }, function(error) {
    console.log(error);
  });
};

$scope.onSelect = function ($item) {
  $scope.loading = true; // switch on the "downloading data" glyphicon
  $scope.movie.title = null; // needed to prevent previous query from autofilling search form
  console.log("Selected!");
  return $http.get('//www.omdbapi.com/?t=' + $item) // send an HTTP request to the OMDb to get a movie object
  .then(function(response){ // then execute a promise
    var movie = { // make a movie object locally matching the downloaded OMDb movie object
      actors: response.data.Actors, // local fields are filled with data from the OMDb
      awards: response.data.Awards,
      comments: [],
      country: response.data.Country,
      director: response.data.Director,
      genre: response.data.Genre,
      language: response.data.Language,
      likes: 0,
      metascore: response.data.Metascore,
      plot: response.data.Plot,
      poster: response.data.Poster,
      rated: response.data.Rated,
      runtime: response.data.Runtime,
      title: response.data.Title,
      writer: response.data.Writer,
      year: response.data.Year,
      imdbID: response.data.imdbID,
      imdbRating: response.data.imdbRating,
      imdbVotes: response.data.imdbVotes,
      dateAdded: Date.now()
    };
    $scope.movies.$add(movie).then(function() { // use a Firebase array method to add the new movie object to our movies array
      $scope.order = '$id' // reset orderBy so that new movie appears in upper left
      $scope.loading = false; // switch off the "downloading data" glyphicon
    });
  });
};
```

The `$scope.getLocation()` handler queries the OMDb for the movie and returns a drop-down menu of ten movies for the user to choose from. Note: you'll see an error message logged in the console "TypeError: Cannot read property 'map' of undefined" for every keystroke in which OMDb can't find a matching movie title word. This error message can be ignored.

When the user selects a movie from the list `$scope.onSelect()` fires and adds the movie object to our array of movies. The `$scope.loading` variable switches on to show the `refresh` glyphicon. The next line prevents the previous query from autofilling the search form.

We create a movie object using data from the OMDb, plus a few fields of our own:

* `comments` is an empty array, ready for users to add comments.
* `likes` is initialized at zero, ready for users to like or dislike a movie.
* `dateAdded` takes the current time and date.

We run the AngularFire method [$add()](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-addnewdata) to add the movie object to our movies array. This is similar to the JavaScript method `push()`.

After the movie object is added to the movies array a promise is executed. We reset the viewing order so that the new movie appears in the upper left position. Then we switch the `$scope.loading` variable off to hide the `refresh` glyphicon.

Deploy to Firebase and see if it works.

![Typeahead](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_typeahead.png)

Select a movie, then go to your Firebase Dashboard and the movie should be there.

![Data](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_data.png)

## Display Movies in Index View (Homepage)

Now we'll display our movies in `home.html`:

```html
<div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
  <a ng-href="/#/movies/{{movie.$id}}"><img class="largeposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
</div>
```

This will display all the movie objects in our movies array, ordered by reverse date added. The `img` displays the movie poster, and when the user clicks on the poster the route changes to the `SHOW` page.

## Style Movie Posters

The movies posters display in a column down the left side of the browser window. Let's add CSS styling to make the movies display in rows. In `style.css`:

```css
.movieIndex {
  display: inline;
}
```

Deploy to Firebase, refresh your browser, and you should see your first movie.

![First Movie](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_first_movie.png)

## Save and Commit Your Work

Deploy to Firebase and save to GitHuB:

```
firebase deploy
git status
git add .
git commit -m "Finished async typeahead."
git push origin master
```
