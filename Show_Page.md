# Async Typeahead

In this chapter you'll work on the `SHOW/EDIT` view. Specifically we'll add the rest of the data fields, and then add a `Delete Movie` button.

An overview and directory for the entire project is in the [README.md](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/README.md).

## Table of Contents

* [Add the Rest of the Movie Data Fields](## Add the Rest of the Movie Data Fields)
* [Refactor `$scope.change` Handler](## Refactor `$scope.change` Handler)
* [Delete Movie Button](## Delete Movie Button)

## Add the Rest of the Movie Data Fields

We've completed two of the hardest parts of the web app: the typeahead and the "Saved" message. Now we'll work on the easier features and the styling. We'll save the authorization/authentication, which is another hard part, for the end.

In `show.html` within the `<form>` add the rest of the fields:

```html
<div class="form-group">
  <label for="editPoster" class="col-lg-2 control-label">Poster: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editPoster" ng-model="movie.poster" ng-change="change('poster')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.posterSave && watch.posterChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editPlot" class="col-lg-2 control-label">Plot: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editPlot" ng-model="movie.plot" ng-change="change('plot')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
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
    <input type="text" class="form-control" name="editDirector" ng-model="movie.director" ng-change="change('director')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.directorSave && watch.directorChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editWriter" class="col-lg-2 control-label">Writer: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editWriter" ng-model="movie.writer" ng-change="change('writer')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.writerSave && watch.writerChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editActors" class="col-lg-2 control-label">Actors: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editActors" ng-model="movie.actors" ng-change="change('actors')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.actorsSave && watch.actorsChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editYear" class="col-lg-2 control-label">Year: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editYear" ng-model="movie.year" ng-change="change('year')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.yearSave && watch.yearChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editCountry" class="col-lg-2 control-label">Country: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editCountry" ng-model="movie.country" ng-change="change('country')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.countrySave && watch.countryChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editLanguage" class="col-lg-2 control-label">Language: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editLanguage" ng-model="movie.language" ng-change="change('language')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.languageSave && watch.languageChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editGenre" class="col-lg-2 control-label">Genre: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editGenre" ng-model="movie.genre" ng-change="change('genre')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.genreSave && watch.genreChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editRated" class="col-lg-2 control-label">Rated: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editRated" ng-model="movie.rated" ng-change="change('rated')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.ratedSave && watch.ratedChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editAwards" class="col-lg-2 control-label">Awards: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editAwards" ng-model="movie.awards" ng-change="change('awards')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.awardsSave && watch.awardsChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editIMDBRating" class="col-lg-2 control-label">IMDB Rating: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editIMDBRating" ng-model="movie.imdbRating" ng-change="change('imdbRating')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.imdbRatingSave && watch.imdbRatingChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editIMDBVotes" class="col-lg-2 control-label">IMDB Votes: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editIMDBVotes" ng-model="movie.imdbVotes" ng-change="change('imdbVotes')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.imdbVotesSave && watch.imdbVotesChange" class="saved">Saved!</p>
  </div>
</div>

<div class="form-group">
  <label for="editMetascore" class="col-lg-2 control-label">Metascore: </label>
  <div class="col-lg-8">
    <input type="text" class="form-control" name="editMetascore" ng-model="movie.metascore" ng-change="change('metascore')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
  </div>
  <div class="col-lg-2">
    <p ng-show="watch.metascoreSave && watch.metascoreChange" class="saved">Saved!</p>
  </div>
</div>
```

![All fields added to SHOW/EDIT view](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_all_fields.png)

## Refactor `$scope.change` Handler

In `ShowController.js` we could write a `$scope.change()` handler for each field. Or we could pass in the movie object properties from the forms, call it `prop` in the handler, and make the handler work with all of the movie object properties:

```js
// Shows and hides "Saved" message
$scope.change = function(prop) {
  $scope.movie.$save().then(function() {   // pushes changes to the remote Firebase
    $scope.watch[prop + 'Change'] = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch[prop + 'Change'] = false; // Hide "Saved" message
    }, 9900);
  }, function(error) {
    console.log(error);
  });
};
```

And in `show.html` refactor the movie title form:

```html
ng-change="change('title')"
```

Now this one function works with all the fields.

We can't write a single function to handle all of the Firebase `$watch` properties because the `$watch` callback doesn't tell us which property changed. (A `$watch` callback on a Firebase array provides the key of the object that changed and whether the DOM changed but not the changed property.) We'll have to write many nearly identical functions:

```js
// Watch movie object poster property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('poster')).$watch(function() {
  $scope.watch.posterSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.posterSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object plot property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('plot')).$watch(function() {
  $scope.watch.plotSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.plotSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object trivia property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('movieTrivia')).$watch(function() {
  $scope.watch.triviaSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.triviaSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object director property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('director')).$watch(function() {
  $scope.watch.directorSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.directorSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object writer property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('writer')).$watch(function() {
  $scope.watch.writerSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.writerSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object actors property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('actors')).$watch(function() {
  $scope.watch.actorsSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.actorsSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object year property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('year')).$watch(function() {
  $scope.watch.yearSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.yearSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object country property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('country')).$watch(function() {
  $scope.watch.countrySave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.countrySave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object language property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('language')).$watch(function() {
  $scope.watch.languageSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.languageSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object genre property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('genre')).$watch(function() {
  $scope.watch.genreSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.genreSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object rated property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('rated')).$watch(function() {
  $scope.watch.ratedSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.ratedSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object awards property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('awards')).$watch(function() {
  $scope.watch.awardsSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.awardsSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object IMDB rating property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('imdbRating')).$watch(function() {
  $scope.watch.imdbRatingSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.imdbRatingSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object IMDB votes property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('imdbVotes')).$watch(function() {
  $scope.watch.imdbVotesSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.imdbVotesSave = false; // Hide "Saved" message
  }, 9900);
});

// Watch movie object metascore property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('metascore')).$watch(function() {
  $scope.watch.metascoreSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.metascoreSave = false; // Hide "Saved" message
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

We'll also need to delete movies. We can create a Bootstrap button in `show.html`:

```html
<div class="form-group">
  <div class="col-lg-2">
    <p>&nbsp;</p>
  </div>
  <div class="col-lg-8">
    <button type="button"
    ng-click="deleteMovie()"
    class="form-control btn btn-danger btn-block">Delete Movie</button>
  </div>
  <div class="col-lg-2">
    <p>&nbsp;</p>
  </div>
</div>
```

Most of these lines are Bootstrap styling. The `<button>` element does the work:

* `<button type="button"` makes a button.
* `ng-click="deleteMovie()"` fires the handler `$scope.deleteMovie()` when the user clicks the button.
* `class="form-control btn btn-danger btn-block">` is Bootstrap styling for a red button.
* `Delete Movie</button>` is the button's label.

Clicking the button fires the handler `$scope.deleteMovie()` so we'll add that to `ShowController.js`:

```js
// Delete movie
$scope.deleteMovie = function() {
  $scope.movie.$remove().then(function() { // deletes the movie object from the movies array
    console.log("Movie deleted."); // executes a promise
    $location.path( "/movies" ); // redirect user back to the homepage
  }, function(error) {
    console.log("Error, movie not deleted.");
    console.log(error);
  });
};
```

This line that redirects the users back to the homepage uses the service `$location`. We'll inject that into `ShowController.js`:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', '$timeout', '$location', function($scope, $firebaseObject, $routeParams, $timeout, $location) {
  console.log("Show controller.");

}]);
```

We're using the Firebase method  [$remove()](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-remove) on the movie object. The method returns a promise. When the promise executes we log the event and then redirect the user to the home page.



### Save and Commit Your Work

Files changed in this chapter:

```
show.html
ShowController.js
style.css
```

The `show.html` template should look like this now:

```html
<div class="row">

  <form class="form-horizontal" name="editMovie">

    <div class="form-group">
      <label for="editTitle" class="col-lg-2 control-label">Title: </label>
      <div class="col-lg-8">
        <input type="text"
        class="form-control"
        name="editTitle"
        ng-model="movie.title"
        ng-change="change('title')"
        uib-tooltip="You can type in this field"
        tooltip-placement="top-left"></input>
      </div>
      <div class="col-lg-2">
        <div ng-show="watch.titleSave && watch.titleChange" class="saved">Saved</div>
      </div>
    </div>

    <div class="form-group">
      <label for="editPoster" class="col-lg-2 control-label">Poster: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editPoster" ng-model="movie.poster" ng-change="change('poster')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.posterSave && watch.posterChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editPlot" class="col-lg-2 control-label">Plot: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editPlot" ng-model="movie.plot" ng-change="change('plot')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
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
        <input type="text" class="form-control" name="editDirector" ng-model="movie.director" ng-change="change('director')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.directorSave && watch.directorChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editWriter" class="col-lg-2 control-label">Writer: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editWriter" ng-model="movie.writer" ng-change="change('writer')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.writerSave && watch.writerChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editActors" class="col-lg-2 control-label">Actors: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editActors" ng-model="movie.actors" ng-change="change('actors')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.actorsSave && watch.actorsChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editYear" class="col-lg-2 control-label">Year: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editYear" ng-model="movie.year" ng-change="change('year')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.yearSave && watch.yearChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editCountry" class="col-lg-2 control-label">Country: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editCountry" ng-model="movie.country" ng-change="change('country')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.countrySave && watch.countryChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editLanguage" class="col-lg-2 control-label">Language: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editLanguage" ng-model="movie.language" ng-change="change('language')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.languageSave && watch.languageChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editGenre" class="col-lg-2 control-label">Genre: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editGenre" ng-model="movie.genre" ng-change="change('genre')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.genreSave && watch.genreChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editRated" class="col-lg-2 control-label">Rated: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editRated" ng-model="movie.rated" ng-change="change('rated')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.ratedSave && watch.ratedChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editAwards" class="col-lg-2 control-label">Awards: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editAwards" ng-model="movie.awards" ng-change="change('awards')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.awardsSave && watch.awardsChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editIMDBRating" class="col-lg-2 control-label">IMDB Rating: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editIMDBRating" ng-model="movie.imdbRating" ng-change="change('imdbRating')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.imdbRatingSave && watch.imdbRatingChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editIMDBVotes" class="col-lg-2 control-label">IMDB Votes: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editIMDBVotes" ng-model="movie.imdbVotes" ng-change="change('imdbVotes')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.imdbVotesSave && watch.imdbVotesChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <label for="editMetascore" class="col-lg-2 control-label">Metascore: </label>
      <div class="col-lg-8">
        <input type="text" class="form-control" name="editMetascore" ng-model="movie.metascore" ng-change="change('metascore')" tooltip-placement="top-left" uib-tooltip="You can type in this field"></label>
      </div>
      <div class="col-lg-2">
        <p ng-show="watch.metascoreSave && watch.metascoreChange" class="saved">Saved!</p>
      </div>
    </div>

    <div class="form-group">
      <div class="col-lg-2">
        <p>&nbsp;</p>
      </div>
      <div class="col-lg-8">
        <button type="button"
        ng-click="deleteMovie()"
        class="form-control btn btn-danger btn-block">Delete Movie</button>
      </div>
      <div class="col-lg-2">
        <p>&nbsp;</p>
      </div>
    </div>

  </form>

</div>
```

The `ShowController.js` controller should look like this now:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', '$timeout', '$location', function($scope, $firebaseObject, $routeParams, $timeout, $location) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com/");
  $scope.movie = $firebaseObject(ref.child($routeParams.id));

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

  // Shows and hides "Saved" message
  $scope.change = function(prop) {
    $scope.movie.$save().then(function() {   // pushes changes to the remote Firebase
      $scope.watch[prop + 'Change'] = true; // Show "Saved" message
      $timeout(function() {
        $scope.watch[prop + 'Change'] = false; // Hide "Saved" message
      }, 9900);
    }, function(error) {
      console.log(error);
    });
  };

  // Watch movie object title property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('title')).$watch(function() {
    $scope.watch.titleSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.titleSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object poster property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('poster')).$watch(function() {
    $scope.watch.posterSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.posterSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object plot property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('plot')).$watch(function() {
    $scope.watch.plotSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.plotSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object trivia property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('movieTrivia')).$watch(function() {
    $scope.watch.triviaSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.triviaSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object director property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('director')).$watch(function() {
    $scope.watch.directorSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.directorSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object writer property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('writer')).$watch(function() {
    $scope.watch.writerSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.writerSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object actors property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('actors')).$watch(function() {
    $scope.watch.actorsSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.actorsSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object year property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('year')).$watch(function() {
    $scope.watch.yearSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.yearSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object country property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('country')).$watch(function() {
    $scope.watch.countrySave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.countrySave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object language property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('language')).$watch(function() {
    $scope.watch.languageSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.languageSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object genre property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('genre')).$watch(function() {
    $scope.watch.genreSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.genreSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object rated property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('rated')).$watch(function() {
    $scope.watch.ratedSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.ratedSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object awards property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('awards')).$watch(function() {
    $scope.watch.awardsSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.awardsSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object IMDB rating property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('imdbRating')).$watch(function() {
    $scope.watch.imdbRatingSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.imdbRatingSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object IMDB votes property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('imdbVotes')).$watch(function() {
    $scope.watch.imdbVotesSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.imdbVotesSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Watch movie object metascore property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('metascore')).$watch(function() {
    $scope.watch.metascoreSave = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.metascoreSave = false; // Hide "Saved" message
    }, 9900);
  });

  // Delete movie
  $scope.deleteMovie = function() {
    $scope.movie.$remove().then(function() { // deletes the movie object from the movies array
      console.log("Movie deleted."); // executes a promise
      $location.path( "/movies" ); // redirect user back to the homepage
    }, function(error) {
      console.log("Error, movie not deleted.");
      console.log(error);
    });
  };

}]);
```

Your `style.css` should look like this:

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

.movieIndex {
  display: inline;
}

.addMovie {
  font-size: larger;
}

.glyphicon-search {
  padding-right: 40px;
  color: silver;
  font-size: larger;
}

.largeposter {
  width: 16.3%;
  height: 285px;
  padding-top: 3px;
}

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

Deploy to Firebase:

```
firebase deploy
```

and save to GitHuB:
```
git status
git add .
git commit -m "Show page improved."
git push origin master
```
