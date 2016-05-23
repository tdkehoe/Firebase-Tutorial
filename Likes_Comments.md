# Likes and Comments

In this chapter you'll add likes and comments to the `SHOW/EDIT` page. The comments are an array of objects that is a property of the movie object, i.e., we'll learn to use nested arrays and objects in Firebase.

An overview and directory for the entire project is in the [README.md](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/README.md).

## Table of Contents

* [Likes Buttons](## Likes Buttons)
* [Likes Handler](## Likes Handler)
* [Comments](## Comments)
* [Add a New Comment](## Add a New Comment)
* [Delete Comments](## Delete Comments)

## Likes Buttons

The `INDEX/NEW` home page is finished, except for authorization/authentication. Let's finish the `SHOW/EDIT` view. In `show.html` add a new row at the bottom, below the `<form>`:

```html
<div class="row">
  <div class="likes col-xs-6 col-sm-6 col-md-6 col-lg-6">
    <span>{{movie.likes}}</span>
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

This makes two Bootstrap columns, equal widths. The left column displays the number of likes. The right column has two forms, above and below. Each form is a single button. One has a "thumbs-up" glyphicon and the other has "thumbs-down". Clicking each fires handlers for `$scope.upLike()` or `$scope.downLike()`

The buttons look fine but the number is small and in the upper left corner of its column. Let's style that in `styles.css`:

```css
.likes {
  font-size: 48pt;
  text-align: right;
}
```

![Likes](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_likes.png)

## Likes Handler

Let's add handlers in `ShowController.js`. Let's put them below the `watch` functions and above the `$scope.deleteMovie` handler:

```js
// Likes section
$scope.upLike = function() {
  $scope.movie.likes += 1;
  $scope.movie.$save().then(function() {
    console.log("Upliked!");
  }, function(error) {
    console.log("Error, movie not upliked.");
    console.log(error);
  });
};

$scope.downLike = function() {
  $scope.movie.likes -= 1;
  $scope.movie.$save().then(function() {
    console.log("Downliked!");
  }, function(error) {
    console.log("Error, movie not downliked.");
    console.log(error);
  });
};
```

Both handlers affect the property `likes` on the movie object. `$scope.upLike()` uses the `+=` _assignment operator_ to increment the variable by one. `$scope.downLike()` uses the less common `-=` to decrement by one.

Both handlers then use the Firebase method [$save](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-save) to fire three-way data binding.

## Comments

Nested comments are a feature where NoSQL databases shine. With SQL you'd make a table for the comments, with a key column in which each comment has a key to the movie it's associated with. If you make a mistake setting up the database, or if the database gets corrupted, you'll have a table of comments and not know which movie each comment goes to. Other columns in the comments table would have comment text, comment author, and date added.

With NoSQL, comments are an array of comment objects. Each comment object has several properties: comment text, comment author, and date added. The comments array is a property of the movie object. The movie objects are in the array of movies. You have nested arrays of objects. It's easy to set up and there's no danger of comments becoming disassociated from their movies.

In `show.html` we'll add a comments row with three groups of elements. Put this at the bottom, below the "likes" buttons:

```html
<!-- Comments row -->
<div class="row">
  <div class="col-sm-12 col-md-12 col-lg-12">
    <span ng-click="showComments = !showComments"
    class="showComments"
    data-toggle="tooltip"
    data-placement="top"
    title="Click to show or hide comments.">
      <ng-pluralize count="comments.length"
      when="{'0': '',
      'one': '1 Comment',
      'other': '{} Comments',
      'NaN': ''}">
    </ng-pluralize><br />
  </span>

  <div ng-hide="showComments" ng-repeat="comment in comments">
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

The first element group shows or hides comments. It also tells you how many comments there are. `ng-pluralize` then either displays nothing if there are no comments, displays "1 comment" if there is one comment, and if there is more than one comment the number of comments is displayed, followed by "comments". A tooltip tells users they can click to show or hide the comments.

The second element group displays the comments. The comment text is displayed first, followed by the author and the date. A `Delete Comment` button is on the right side. This button fires the handler `$scope.deleteComment()`.

The third element group has a form for adding a new comment. It has two fields, for the text and the author. It fires the handler `$scope.newComment()`. Now this is the only group you'll see in the view, until you add comments.

### Add New Comment

The comments are an array so we need to inject the `$firebaseArray` service into `ShowController.js`:

```js
app.controller('ShowController', ['$scope', '$firebaseArray', '$firebaseObject', '$routeParams', '$timeout', '$location', function($scope, $firebaseArray, $firebaseObject, $routeParams, $timeout, $location) {
  console.log("Show controller.");

}]);
```

Let's add the `$scope.newComment` handler in `ShowController.js`. It can go below the "likes" handlers and above the "Delete movie" handler:

```js
// Comments section
$scope.newComment = function(comment) { // full record is passed from the view
  var commentWithDate = {
    commentText: comment.commentText,
    commentAuthor: comment.commentAuthor,
    commentDate: Date.now() // Timestamp comment
  };
  $scope.movie.comments = $scope.movie.comments || []; // checks if older comments exist
  $scope.comment.commentText = null; // needed to prevent autofilling fields
  $scope.comment.commentAuthor = null; // needed to prevent autofilling fields
  $firebaseArray(ref.child($routeParams.id).child('comments')).$add(commentWithDate).then(function() {
    console.log("Comment added!");
  }, function(error) {
    console.log("Error, comment not added.");
    console.log(error);
  });
};
```

The argument passes the comment in from the view.

Next, we take the comment and add a timestamp.

Next, we check if there is a `comments` array, i.e., if there are older comments. If not we create an empty array.

Next we have two lines that prevent autofilling the comments forms with the previous comment and author.

Now we have a long line that does a lot of work. We create a new `$firebaseArray` for the array of movies.

Then we drill down into the array of movies to select one movie: `$firebaseArray(ref.child($routeParams.id)`

Then we drill down into the comments array: `$firebaseArray(ref.child($routeParams.id).child('comments'))`

Then we add our comment to the array: `$firebaseArray(ref.child($routeParams.id).child('comments')).$add(commentWithDate)`

Finally we execute a promise when the comment is added to the array: `$firebaseArray(ref.child($routeParams.id).child('comments')).$add(commentWithDate).then(function() { });`

> One of my pet peeves is developers who type random letters, e.g., `rerewrew`. Maybe that's OK if you're going to delete the data in a few minutes but if you're leaving a comment on a public web app everyone after you will see your brainless scribbling. Relax for a moment, take a deep breath, and think of something funny to write. It'll put you in a better mood now and every time you see it, and it'll make later users smile. You don't have to write anything brilliant, just attach a celebrity to the comment and it'll be funny. E.g., "What's not to like?" with the name of your (least) favorite celebrity. Eva Braun seems to make a lot of comments when I'm testing code. :-)

## Delete Comments

This is one of the most difficult parts of the app. I suggest studying the [Firebase Principles](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/Firebase_Principles.md) chapter and the [Firebase API documentation](https://www.firebase.com/docs/web/libraries/angular/api.html).

The handler `$scope.deleteComment()` is working with nested arrays and objects. Remember that Firebase uses `.child()` notation, not JavaScript dot notation, because Firebase objects are not JavaScript objects. Firebase objects have keys.

This is the comments display section of `show.html`:

```html
<div ng-hide="showComments" ng-repeat="comment in movie.comments">
  <div class="row comment img-rounded">
    <div class="col-sm-8 col-md-8 col-lg-8">
      <span class="commentText">{{comment.commentText}}</span>
      <div class="commentAuthor">
        <span>&#151;{{comment.commentAuthor}}</span>
        <span>{{comment.commentDate | date:'mediumDate'}}</span>
      </div>
    </div>
    <div class="col-sm-4 col-md-4 col-lg-4">
      <form>
        <button ng-click="movie.comments.$remove(comment)" class="btn btn-danger btn-sm">Delete Comment</button>
      </form>
    </div>
  </div>
</div>
```

The comments display because Angular is OK with dot notation:

```html
ng-repeat="comment in movie.comments"
```

But the button does nothing because Firebase methods can't be attached to dot notation:

```html
ng-click="movie.comments.$remove(comment)"
```

What we need is this:

```html
<div ng-hide="showComments" ng-repeat="comment in comments">
  <div class="row comment img-rounded">
    <div class="col-sm-8 col-md-8 col-lg-8">
      <span class="commentText">{{comment.commentText}}</span>
      <div class="commentAuthor">
        <span>&#151;{{comment.commentAuthor}}</span>
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

How do we make a `$scope.comments` array to hold our movie's comments? Our movie's comments are here:

```js
$scope.movie.comments
```

but this doesn't work:

```js
$scope.comments = $scope.movie.comments
```

because it creates an array that Firebase methods can't attach to, because the array was created with dot notation.

Here's how we do it. Add this synchroray to `ShowController.js`, near the top, below the syncroject:

```js
$scope.comments = $firebaseArray(ref.child($routeParams.id).child('comments'));
```

If you use `$firebaseObject` instead of `$firebaseArray`:

```js
$scope.comments = $firebaseObject(ref.child($routeParams.id).child('comments'));
```

the button deletes all comments, because `$remove()` removes the entire object or array that it's attached to. When we use `$firebaseArray` then a different method is used. It's confusing that the two methods have the same name. The `$firebaseArray` method `$remove(recordOrIndex)` removes one object from an array.

What if you want your logic in the controller, not the view? We can change our button to fire a handler, in `show.html`:

```html
<button ng-click="deleteComment(comment)" class="btn btn-danger btn-sm">Delete Comment</button>
```

Then in the `ShowController.js` we add the handler, below the comments section:

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

## Style Comments

Let's add some styling for the comments to `style.css`:

```css
.comment {
  border-style: solid;
  border-width: thin;
  padding: 5px;
  border-color: silver;
}

.commentAuthor {
  text-align: right;
  font-style: italic;
}

.showComments {
  font-weight: bold;
}
```

## Two Columns in Large View

In the large responsive view we have plenty of room for two columns.

We'll make two six-column containers. In `show.html` wrap the `<form>` with a `<div>` and then wrap everything else in another `<div>`:

```html
<div class="col-sm-6 col-md-6 col-lg-6"> <!-- left column -->

  <!-- <form> goes here -->

</div> <!-- end left column -->

<div class="col-sm-6 col-md-6 col-lg-6"> <!-- right column -->

  <!-- likes and comments go here -->

</div> <!-- end right column -->
```

![Two columns](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_two_columns.png)

Let's add the poster to the right column, above the likes:

```html
<img class="moviePoster center-block" ng-src="{{movie.poster}}" alt="{{movie.title}}">

<hr />
```

We put a horizontal line between the poster and the likes.

![Two columns](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_show_poster.png)

We need a `Tech Notes` on this page to point out the awesome features to hiring managers. We'll put the button at the top of the right column for maximum visibility:

```html
<button type="button" class="btn btn-danger btn-block" ng-click="techSummary = !techSummary">Tech Notes</button>
<br />

<!-- Tech Summary row -->
<div class="row well well-lg" ng-show="techSummary">
  <p class="text-justify">This large screen view combines the SHOW and EDIT views. You can edit the fields in the left column. The small screen views have seperate SHOW and EDIT pages.</p>
  <p class="text-justify">To get a single movie object from the array of movies, one can use $firebaseArray to get the array of movies from the remote database and then use the Firebase method $getRecord(key) to get one movie from the array. Or one can use $firebaseObject(ref.child(key)) to get a single movie from the remote database. The latter method is used on this page for improved performance and reliability.</p>
  <p class="text-justify">Edits are saved immediately to the remote database using Firebase three-way binding. No "Update" button is needed. To inform users that their edits have been saved the Firebase method $watch() is used to display an animated "Saved!" message. This was one of the most challenging features to implement. Each field has its own $watch() method, as opposed to watching the entire database for changes, which would trigger the "Saved!" message when other users add or edit other movies. To hide the "Saved!" message when the page loads the message shows only when two conditions are met, the user changing the field and the remote database changing. The Angular service $timeout() is used to display the message for 9.9 seconds. A 10-second animation changes the opacity to make the message fade in and out.</p>
  <p class="text-justify">The Firebase remote database is set with security rules to allow only logged-in users to write data.</p>
  <p class="text-justify">The like/dislike buttons are styled with glyphicons.</p>
  <p class="text-justify">Comments are an array of objects nested in the movie object. This is easy with the NoSQL database but would be more work with an SQL database. The "number of comments display" hides and shows the comments when clicked. A tooltip informs users of this feature. The number of comments pluralizes.</p>
</div>
```

![Two columns](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_show_notes.png)

### Save and Commit Your Work

Check the responsive views.

Files changed in this chapter:

```
show.html
ShowController.js
style.css
```

Your `show.html` should now look like this:

```html
<div class="row">

  <div class="col-sm-6 col-md-6 col-lg-6"> <!-- left column -->

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

  </div> <!-- end left column -->

  <div class="col-sm-6 col-md-6 col-lg-6"> <!-- right column -->

    <button type="button" class="btn btn-danger btn-block" ng-click="techSummary = !techSummary">Tech Notes</button>
    <br />

    <!-- Tech Summary row -->
    <div class="row well well-lg" ng-show="techSummary">
      <p class="text-justify">This large screen view combines the SHOW and EDIT views. You can edit the fields in the left column. The small screen views have seperate SHOW and EDIT pages.</p>
      <p class="text-justify">To get a single movie object from the array of movies, one can use $firebaseArray to get the array of movies from the remote database and then use the Firebase method $getRecord(key) to get one movie from the array. Or one can use $firebaseObject(ref.child(key)) to get a single movie from the remote database. The latter method is used on this page for improved performance and reliability.</p>
      <p class="text-justify">Edits are saved immediately to the remote database using Firebase three-way binding. No "Update" button is needed. To inform users that their edits have been saved the Firebase method $watch() is used to display an animated "Saved!" message. This was one of the most challenging features to implement. Each field has its own $watch() method, as opposed to watching the entire database for changes, which would trigger the "Saved!" message when other users add or edit other movies. To hide the "Saved!" message when the page loads the message shows only when two conditions are met, the user changing the field and the remote database changing. The Angular service $timeout() is used to display the message for 9.9 seconds. A 10-second animation changes the opacity to make the message fade in and out.</p>
      <p class="text-justify">The Firebase remote database is set with security rules to allow only logged-in users to write data.</p>
      <p class="text-justify">The like/dislike buttons are styled with glyphicons.</p>
      <p class="text-justify">Comments are an array of objects nested in the movie object. This is easy with the NoSQL database but would be more work with an SQL database. The "number of comments display" hides and shows the comments when clicked. A tooltip informs users of this feature. The number of comments pluralizes.</p>
    </div>

    <img class="moviePoster center-block" ng-src="{{movie.poster}}" alt="{{movie.title}}">

    <hr />

    <div class="row">
      <div class="likes col-xs-6 col-sm-6 col-md-6 col-lg-6">
        <span>{{movie.likes}}</span>
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

    <!-- Comments row -->
    <div class="row">
      <div class="col-sm-12 col-md-12 col-lg-12">
        <span ng-click="showComments = !showComments"
        class="showComments"
        data-toggle="tooltip"
        data-placement="top"
        title="Click to show or hide comments.">
        <ng-pluralize count="comments.length"
        when="{'0': '',
        'one': '1 Comment',
        'other': '{} Comments',
        'NaN': ''}">
      </ng-pluralize><br />
    </span>

    <div ng-hide="showComments" ng-repeat="comment in comments">
      <div class="row comment img-rounded">
        <div class="col-sm-8 col-md-8 col-lg-8">
          <span class="commentText">{{comment.commentText}}</span>
          <div class="commentAuthor">
            <span>&#151;{{comment.commentAuthor}}</span>
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

</div> <!-- end right column -->

</div>
```

Your `ShowController.js` should now look like this:

```js
app.controller('ShowController', ['$scope', '$firebaseArray', '$firebaseObject', '$routeParams', '$timeout', '$location', function($scope, $firebaseArray, $firebaseObject, $routeParams, $timeout, $location) {
  console.log("Show controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com/");
  $scope.movie = $firebaseObject(ref.child($routeParams.id)); // Set up single movie object
  $scope.comments = $firebaseArray(ref.child($routeParams.id).child('comments')); // Set up comments array

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

  // Likes section
  $scope.upLike = function() {
    $scope.movie.likes += 1;
    $scope.movie.$save().then(function() {
      console.log("Upliked!");
    }, function(error) {
      console.log("Error, movie not upliked.");
      console.log(error);
    });
  };

  $scope.downLike = function() {
    $scope.movie.likes -= 1;
    $scope.movie.$save().then(function() {
      console.log("Downliked!");
    }, function(error) {
      console.log("Error, movie not downliked.");
      console.log(error);
    });
  };

  // Comments section
  $scope.newComment = function(comment) { // full record is passed from the view
    var commentWithDate = {
      commentText: comment.commentText,
      commentAuthor: comment.commentAuthor,
      commentDate: Date.now()  // Timestamp comment
    };
    $scope.comments = $scope.comments || []; // checks if older comments exist
    $scope.comment.commentText = null; // needed to prevent autofilling fields
    $scope.comment.commentAuthor = null; // needed to prevent autofilling fields
    $firebaseArray(ref.child($routeParams.id).child('comments')).$add(commentWithDate).then(function() {
      console.log("Comment added!");
    }, function(error) {
      console.log("Error, comment not added.");
      console.log(error);
    });
  };

  $scope.deleteComment = function(comment) {
    $scope.comments.$remove(comment).then(function() {
      console.log("Comment deleted!");
    }, function(error) {
      console.log("Error, comment not deleted.");
      console.log(error);
    });
  };

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
@font-face {
  font-family: "Terminator";
  src: url("fonts/Terminator.ttf") format("truetype");
}

h1 {
  font-family: Terminator, sans-serif;
  text-shadow: 4px 4px 4px #aaa;
  color: black;
}

/*HOME PAGE STYLES*/

.addMovie {
  font-size: larger;
}

.glyphicon-search {
  padding-right: 40px;
  color: silver;
  font-size: larger;
}

.resume:link {
  text-decoration: none;
}

.movieIndex {
  display: inline;
}

.extraSmallposter {
  width: 49%;
}

.smallposter {
  width: 24.5%;
  height: 276px;
}

.mediumposter {
  width: 19.5%;
  height: 282px;
}

.largeposter {
  width: 16.3%;
  height: 285px;
  padding-top: 3px;
}

.extraSmallposter:hover, .smallposter:hover, .mediumposter:hover, .largeposter:hover {
  outline: 5px solid blue;
}

/*SHOW/EDIT STYLES*/

.likes {
  font-size: 48pt;
  text-align: right;
}

.comment {
  border-style: solid;
  border-width: thin;
  padding: 5px;
  border-color: silver;
}

.commentAuthor {
  text-align: right;
  font-style: italic;
}

.showComments {
  font-weight: bold;
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
git commit -m "Likes and comments working."
git push origin master
```
