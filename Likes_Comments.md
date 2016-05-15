# Async Typeahead

In this chapter you'll build a project using Firebase. Specifically, we'll use the AngularFire bindings with Angular and Bootstrap.

The order of chapters is

1. [Firebase Principles](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/Firebase_Principles.md) (this chapter)
2. [Firebase Project: Make the CRUDiest Movies Database](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/Firebase_CRUD.md)
4. [Async Typehead]()
3. [Firebase Auth: Add Login to Your Project](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/Firebase_Authorization.md)

## Firebase Principles Table of Contents

* [Set Up Your Directory Structure](## Set Up Your Directory Structure)
* [Open A Firebase Account](## Open A Firebase Account)
* [Set Up `index.html`](## Set Up `index.html`)
* [Set Up Angular](## Set Up Angular)
* [Add a Movie With UI Bootstrap Typeahead](## Add a Movie With UI Bootstrap Typeahead)
* [Display Movies in Index View (Homepage)](## Display Movies in Index View (Homepage))
* [View Movie Title in SHOW/EDIT](## View Movie Title in SHOW/EDIT)

### Contact Me

Please update this document and make [pull requests](https://github.com/tdkehoe/Firebase-Tutorial) or send me an e-mail at kehoe@casafuturatech.com.






## Likes

The `INDEX/NEW` home page is finished, except for authorization/authentication. Let's finish the `SHOW/EDIT` view. In `show.html` you can add:

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

This makes two Bootstrap columns, equal widths. The left column displays the number of likes. The right column has two forms, above and below. Each form is a single button. One has a "thumbs-up" glyphicon and the other has "thumbs-down". Clicking fires handlers for `$scope.upLike()` or `$scope.downLike()`

The buttons look fine but the number is small and in the upper left corner of its column. Let's style that in `styles.css`:

```css
.likes {
  font-size: 48pt;
  text-align: right;
}
```

![Likes](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_likes.png)

Let's add the handler in `ShowController.js`:

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

In `show.html` we'll add a comments row with three groups of elements:

```html
<!-- Comments row -->
<div class="row">
  <div class="col-sm-12 col-md-12 col-lg-12">
    <span ng-click="showComments = !showComments" class="showComments" data-toggle="tooltip" data-placement="top" title="Click to show or hide comments.">
      <ng-pluralize count="movie.comments.length"
      when="{'0': '',
      'one': '1 Comment',
      'other': '{} Comments',
      'NaN': ''}">
    </ng-pluralize><br />
  </span>

  <div ng-hide="showComments" ng-repeat="comment in movie.comments">
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

The first element group shows or hides comments. It tells you how many comments there are. `ng-pluralize` then either displays nothing if there are no comments, displays "1 comment" if there is one comment, and if there is more than one comment the number of comments is displayed, followed by "comments". A tooltip tells users they can click to show or hide the comments.

The second element group displays the comments. The comment text is displayed first, followed by the author and the date. A `Delete Comment` button is on the right side. This button fires the handler `$scope.deleteComment()`.

The third element group has a form for adding a new comment. It has two fields, for the text and the author. It fires the handler `$scope.newComment()`.

### Add New Comment

Let's add the `$scope.newComment` handler in `ShowController.js`:

```js
// Comments section
$scope.newComment = function(comment) { // full record is passed from the view
  var commentWithDate = {
    commentText: comment.commentText,
    commentAuthor: comment.commentAuthor,
    commentDate: Date.now(),
  };
  $scope.movie.comments = $scope.movie.comments || [];
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

The first line passes the comment in from the view.

Next, we take the comment and add a date timestamp.

Next, we check if there is a `comments` array. If not we create an empty array.

Next we have two lines that prevent autofilling the comments forms with the previous comment and author.

Now we have a long line that does a lot of work. We create a new `$firebaseArray` for the array of movies.

> Check that `$firebaseArray` is injected in `ShowController.js`:

```js
app.controller('ShowController', ['$scope', '$routeParams', '$location', '$firebaseObject', '$firebaseArray', '$timeout', function($scope, $routeParams, $location, $firebaseObject, $firebaseArray, $timeout) {
  console.log("Show controller.");

}]);
```

Then we drill down into the array of movies to select one movie: `$firebaseArray(ref.child($routeParams.id)`

Then we drill down into the comments array: `$firebaseArray(ref.child($routeParams.id).child('comments'))`

Then we add our comment to the array: `$firebaseArray(ref.child($routeParams.id).child('comments')).$add(commentWithDate)`

Finally we have a promise that's executed when the comment is added to the array: `$firebaseArray(ref.child($routeParams.id).child('comments')).$add(commentWithDate).then(function() { });`

We could add new comments with JavaScript methods instead of Firebase methods:

```js
$scope.movie.comments.push(commentObject);
```

If we use JavaScript methods Firebase won't assign keys to the objects in the array. If we use the Firebase method `$add` then our objects have keys.

## Delete Comments

The handler `$scope.deleteComment()` is trickier than other handlers because it's working with nested arrays and objects. Remember that Firebase uses `.child()` notation, not JavaScript dot notation, because Firebase objects are not JavaScript objects. Firebase objects have keys.

This is the comments display section of `show.html`:

```html
<div ng-hide="showComments" ng-repeat="comment in movie.comments">
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

How do we make a `$scope.comments` array to hold our movie's comments? Our movie's comments are here:

```js
$scope.movie.comments
```

but this doesn't work:

```js
$scope.comments = $scope.movie.comments
```

because it creates an array that Firebase methods can't attach to. (Because the array was created with dot notation.)

Here's how we do it, in `ShowController.js`:

```js
$scope.comments = $firebaseArray(ref.child($routeParams.id).child('comments'));
```

Now the button works. If you use `$firebaseObject` instead of `$firebaseArray`:

```js
$scope.comments = $firebaseObject(ref.child($routeParams.id).child('comments'));
```

the button deletes all comments, because `$remove()` removes the entire object or array that it's attached to. When we use `$firebaseArray` then a different method is used. It's confusing that the two methods have the same name. The `$firebaseArray` method `$remove(recordOrIndex)` removes one object from an array.

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
