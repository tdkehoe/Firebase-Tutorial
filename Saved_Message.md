# "Saved" Message

In this chapter you'll start the `SHOW/EDIT` view and then add a "Saved" message indicating that the user's edits have saved to the remote Firebase, without the user clicking a `SUBMIT` button.

An overview and directory for the entire project is in the [README.md](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/README.md).

## Table of Contents
* [Movie Title Entry Form](## Movie Title Entry Form)
* [Show Movie Title](## Show Movie Title)
* [Edit Movie Title](## Edit Movie Title)
* ["Saved" Message](## "Saved" Message)
* [Dual Conditions For Show/Hide Message](## Dual Conditions For Show/Hide Message)
* [Animation](## Animation)


## Movie Title Entry Form

Click on a movie poster and the view changes. Note that the URL changes, with the _key_ or `$id` of the movie object appearing in the URL. This works because we wrapped the movie image with `<a ng-href="/#/movies/{{movie.$id}}">` and set up a route for `'/movies/:id'`.

All Bootstrap views begin with:

```html
<div class="row">

</div>
```

Start `show.html` with that code block.

Let's add a form for showing and editing the movie title:

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
        ng-change="change()"
        uib-tooltip="You can type in this field"
        tooltip-placement="top-left"></input>
      </div>
    </div>

  </form>

</div>
```

The first five lines are mostly Bootstrap styling.

The `<input>` form is doing the work. Let's look at it more closely:

* `<input type="text"` makes a text entry input field.
* `class="form-control"` is Bootstrap styling.
* `name="editTitle"` links the field to its label.
* `ng-model="movie.title"` two-way binds the data to the `movie.title` property. The field displays the value in the property, and if the user types in the field it instantly updates the local `$scope` and the remote Firebase.
* `ng-change="change()"` fires the `$scope.change()` handler when the user types in the form.
* `uib-tooltip="You can type in this field">` provides instructions to users who don't realize they can edit the movie data.
* `tooltip-placement="top-left"` positions the tool tip.

![Title Entry Form](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_title_form.png)

## Show Movie Title

The form doesn't display anything because we need to work on the controller. In `ShowController.js` log `$scope.movie`:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', function($scope, $firebaseObject) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com");
  $scope.movie = $firebaseObject(ref);
  console.log($scope.movie);

}]);
```

If you've added more than one movie to your array, you'll see that `$scope.movie` includes all your movies, when we want just one movie. We need to drill down through the array and pick out one movie. Firebase doesn't use JavaScript brackets or _dot notation_. Instead it uses a _child notation_:

```js
$scope.movie = $firebaseObject(ref.child($routeParams.id));  // Set up single movie object
```

We're telling Firebase to get a child object from the array, selected by the `$routeParams.id` passed via the URL. We'll also have to inject the service `$routeParams` into the controller:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', function($scope, $firebaseObject, $routeParams) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com");
  $scope.movie = $firebaseObject(ref.child($routeParams.id));
  console.log($scope.movie);

}]);
```

Now when you deploy and refresh your browser the movie title should appear in the form.

![Plan 9](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_plan9.png)

Note the UI Bootstrap _tooltip_ in the form that informs users "You can type in this field".

## Edit Movie Title

You can type in the movie title form but if you refresh the browser the change wasn't saved. In the `show.html` we have `ng-model="movie.title"` so edits in the view should bind to the `$scope`. But to get three-way data binding to Firebase we have to use the `$save()` Firebase object method.

In `show.html` we have `ng-change="change()"`. When the user makes a change in the form, the handler `$scope.change()` runs in the controller. The title is passed through to the handler.

Add `$scope.change` to `ShowController.js`:

```js
$scope.change = function() {
  $scope.movie.$save().then(function() {   // pushes changes to the remote Firebase
    console.log("Data saved."); // promise executes
  }, function(error) {
    console.log(error);
  });
};
```

Edit the movie title and look in your Firebase Dashboard. You should see that the data updated. Refresh your browser and you'll see that the changes were saved. Three-way data binding is now working. Changes the user makes in the view instantly save to the movie object in the `$scope` and to the remote Firebase.

Try opening another browser window, so that you have two browsers running your web app. Edit the movie title in one browser and you'll see it update instantly in the other browser. Changes in the remote Firebase instantly update each browser's `$scope` and views.

## "Saved" Message

Three-way data binding is neat but you may have noticed a user interface/user experience (UI/UX) problem. Users aren't going to check the Firebase Dashboard or refresh the browser to see if their edits were saved. We need to alert the user. We could make a `SUBMIT` button and send the user to a new view that says "Your form has been submitted." But that would be so 2000's. Instead we'll show the message "Saved" next to the field when the data is saved to Firebase. In `show.html` add this:

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
        ng-change="change()"
        uib-tooltip="You can type in this field"
        tooltip-placement="top-left"></input>
      </div>

      <div class="col-lg-2">
        <div ng-show="watch.titleSave" class="saved">Saved</div>
      </div>

    </div>

  </form>

</div>
```

We now have twelve columns (2 + 8 + 2), a full Bootstrap row. In the left row `ng-show` will show the message `Saved` if the value of `$scope.watch.titleSave` is true.

Near the top of `ShowController.js` initialize the variable:

```js
// Initialize variables
$scope.watch = {
  titleSave: false,
};
```

Near the bottom of `ShowController.js` make a function using the Firebase method [$watch](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-watchcallback-context):

```js
// Watch movie object title property, show "Saved!" message
$firebaseObject(ref.child($routeParams.id).child('title')).$watch(function() {
  $scope.watch.titleSave = true; // Show message
});
```

The Firebase method `$watch` registers an event listener which is notified any time there is a  change to the data, firing its callback function. We've attached `$watch` to a grandchild of the movies array. The child selects a movie by its `$id` key, and then the grandchild selects the `title` property from the movie object.

The callback function switches the variable `$scope.watch.titleSave` to show the message.

## Dual Conditions For Show/Hide Message

That works too well. The `$watch` fires when page loads. Let's add a second condition, indicating that the user has changed the data.

In `show.html` add the second condition:

```html
<div ng-show="watch.titleSave && watch.titleChange" class="saved">Saved!</div>
```

> We could also write "watch.titleSave; watch.titleChange" but not "watch.titleChange; watch.titleSave". With that syntax the order of the variables matters. The `&&` syntax is more reliable.

Now the message will show only when the user makes a change and the data is saved.

In `ShowController.js` add the variable to `$scope.change()`. Let's put it in the promise, and remove the console log:

```js
// Shows and hides "Saved" message
$scope.change = function() {
  $scope.movie.$save().then(function() {   // pushes changes to the remote Firebase
    $scope.watch.titleChange = true; // Show "Saved" message
  }, function(error) {
    console.log(error);
  });
};
```

And initialize the variable:

```js
// Initialize variables
$scope.watch = {
  titleChange: false,
  titleSave: false
};
```

Now `ShowController.js` should look like this (you can remove `console.log($scope.movie);`):

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', function($scope, $firebaseObject, $routeParams) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com/");
  $scope.movie = $firebaseObject(ref.child($routeParams.id));

  // Initialize variables
  $scope.watch = {
    titleChange: false,
    titleSave: false
  };

  // Shows and hides "Saved" message
  $scope.change = function() {
    $scope.movie.$save().then(function() {   // pushes changes to the remote Firebase
      $scope.watch.titleChange = true; // Show "Saved" message
    }, function(error) {
      console.log(error);
    });
  };

  // Watch movie object title property, show "Saved" message
  $firebaseObject(ref.child($routeParams.id).child('title')).$watch(function() {
    $scope.watch.titleSave = true; // Show "Saved" message
  });

}]);
```

## Animation

Now it'd be nice if the `Saved!` message went away after a few seconds. We can't use the JavaScript method `setTimeout()` because it doesn't work with Angular. Instead we add the Angular service `$timeout` to the `ShowController.js`:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', '$timeout', function($scope, $firebaseObject, $routeParams, $timeout) {
  console.log("Show controller.");

}]);
```

Now we can use the Angular method `$timeout()` in a function:

```js
// Shows and hides "Saved" message
$scope.change = function() {
  $scope.movie.$save().then(function() {   // pushes changes to the remote Firebase
    $scope.watch.titleChange = true; // Show "Saved" message
    $timeout(function() {
      $scope.watch.titleChange = false; // Hide "Saved" message
    }, 9900);
  }, function(error) {
    console.log(error);
  });
};
```

This waits 9.9 seconds and then switches off the message.

It's unnecessary but we can add a timer to the Firebase `$watch` as well:

```js
// Watch movie object title property, show "Saved" message
$firebaseObject(ref.child($routeParams.id).child('title')).$watch(function() {
  $scope.watch.titleSave = true; // Show "Saved" message
  $timeout(function() {
    $scope.watch.titleSave = false; // Hide "Saved" message
  }, 9900);
});
```

It'd be nicer if the message were animated. Add styling to `style.css`:

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
  ...
}
```

### Save and Commit Your Work

Files changed in this chapter:

```
show.html
ShowController.js
style.css
```

Your `show.html` should now look like this:

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
      <div class="col-sm-8">
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

Your `ShowController.js` should now look like this:

```js
app.controller('ShowController', ['$scope', '$firebaseObject', '$routeParams', '$timeout', '$location', function($scope, $firebaseObject, $routeParams, $timeout, $location) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com/");
  $scope.movie = $firebaseObject(ref.child($routeParams.id));  // Set up single movie object

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

Your `style.css` should now look like this:

```css
.movieIndex {
  display: inline;
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
git commit -m "Saved message working."
git push origin master
```
