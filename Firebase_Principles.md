# Firebase Principles

## Table of Contents

* [A Short History of Websites](## A Short History of Websites)
* [What Is Firebase?](## What Is Firebase?)
* [The Most Confusing Parts of Firebase](## The Most Confusing Parts of Firebase)
* [Setting Up Your Firebase App](## Setting Up Your Firebase App)
* [Firebase Object Methods](## Firebase Object Methods)
* [Firebase Array Methods](## Firebase Array Methods)

## A Short History of Websites

Firebase is a NoSQL cloud database service. Storing your data in the cloud has become popular for a variety of reasons. To explain the advantages of Firebase, allow me to walk through my twenty years of building websites. In the 1990s we built static websites by hand-coding HTML. The World Wide Web (WWW) enabled users to look up information but websites weren't interactive. The browser sent an _HTTP request_ to the server, which served an HTML file to the browser, and the browser rendered the HTML code for the user to see. Then the user clicked a link, which sent another HTTP request to the server for another webpage, and so on.

In 1999 I wrote the first documentation for using a new language called Personal Home Page (PHP) with a new database called MySQL. My documentation became the most popular and highest-rated download from the website DevShed.com. Being able to connect a database to your HTML enabled developers to make interactive websites, e.g., a dating website where users could make profiles. Soon the WWW was dominated by websites where users put stuff into databases, e.g., MySpace.com and Facebook.com. We wrote static HTML webpages with inserted PHP code that got data to fill in spaces on the webpage, such as a user's name or picture. The browser sent an HTTP request to the server, which ran PHP, Java, or another programming language to translate the HTTP requests into SQL queries. The data filled in the spaces of a static webpage with dynamic content, then served it to the browser.

The next step was _dynamic websites_, in which a database retrieved parts of a website and assembled the parts into webpages. No longer did webmasters have to write HTML. Instead we used _content management systems_ (CMS) such as WordPress. A blogger could set up a complex website by plugging in modules, with little or no coding knowledge. With Ruby on Rails (RoR) developers could spin up a website in hours instead of weeks, plugging in modules for almost anything. The browser sent an HTTP request to the server, which hit the database perhaps hundreds of times to assemble the webpage, then rendered the webpage and served it to the browser. The user clicked on a button or a link, another HTTP request went to the server, the server hit the database hundreds more times, etc.

Dynamic websites are slow. Users click and wait while a lot of stuff happens on the server, before their page loads. Users bounce if a website is slow or requires too many clicks and page reloads. Webmasters pay top dollar for the fastest hosting services, and their websites are still slow.

Vast numbers of software engineers work at companies with SQL back ends, coding in PHP, C, Java, or Python. With SQL a database administrator must set up the _schema_ or structure of the database. The arrays and objects that software developers create must be translated into tables of columns and rows. Like most professions, learning the work isn't easy, but once you learn it you can expect to do the same stuff every day for the next twenty years.

The next step was JavaScript. JavaScript is the only language that runs in the browser. Initially JavaScript did only minor tasks on websites but then frameworks such as Angular were developed. Initially the browser sends an HTTP request to the server, but the server sends not a rendered webpage but an application that runs on the user's computer, in the browser. The user clicks on buttons and the app does stuff, such as opening a calendar, without going back to the server. The user interface or user experience (UI/UX) is fast, practically instantaneous.

JavaScript web apps still need data. An app can open a calendar, but to open a calendar of your AirBnB reservations the app has to send an HTTP request to the server to access the database and send back data. This is faster than a client-server website because only small amounts of data are requested (instead of entire webpages), and the webpages (now called _views_) don't have to be rendered each time they change, as only the parts that change are updated.

With JavaScript web apps the _back end_ servers do little. The servers just translate HTTP requests into SQL queries and send data in HTTP responses. _NoSQL databases_ eliminate the translation into SQL queries. NoSQL databases store arrays of objects, connecting seamlessly to programming languages, especially JavaScript and JavaScript Object Notation (JSON). NoSQL databases generally don't require schema. A NoSQL server can be spun up in a few hours, and after that there's little or nothing to do on the back end.

## What Is Firebase?

Firebase is a NoSQL cloud database. You write your front-end app in Angular, React, or another framework. You use the `$firebaseArray` and `$firebaseObject` _services_ in your controllers to _bind_ your local arrays and objects to arrays and objects in the remote database. Your local arrays and objects stay synchronized to the remote database without HTTP requests or database queries. You don't need to write schema on the back end, the remote arrays and objects mirror your local arrays and objects.

When data changes in Firebase the app updates, and vice versa. In contrast, with a client-server structure the app only updates when the browser sends an HTTP request to the server, usually when the user clicks something. If you order a pizza and the driver's GPS is constantly sending updates, you don't want to have to click every time you want to see where the driver is. You want the app to show the driver getting closer to you without you having to click anything.

I also made a [MEAN stack movies database](https://crudiest-movies.firebaseapp.com/#/movies) using Node, Express, and MongoDB on the back end. If multiple users are using the apps at the same time, the Firebase users see new movies added or data edited by other users appear magically, with the user doing nothing. In contrast, in the MEAN stack movies database new data from other users only appears if the user refreshes the browser.

A third service, `$firebaseAuth`, creates an _auth object_ to log in a user, authorize which views the user can access, what data the user can read or write, etc.

With Firebase, front-end developers can focus on the UI/UX without thinking about the back end. Compare the [CRUDiest Movies Firebase](https://crudiest-firebase.firebaseapp.com/#/movies) to the [Internet Movies Database](http://www.imdb.com/) (IMDb). The IMDb feels like it was written a long time ago in a galaxy far, far away. In contrast, the Firebase app feels like it's a native app accessing a database on your computer.

To reiterate the reasons to use Firebase:

* You don't need to run a server. Servers take time to set up, and then can crash or are shut down by your host for maintenance. A Node/Express/MongoDB server adds no value to your website, i.e., it doesn't do anything that you can't do in a front-end framework such as AngularJS (e.g., routing).
* Data binding synchronizes your local arrays and objects to the remote arrays and objects. You don't have to write HTTP requests or database queries. Data updates in your app without your users having to click anything.
* Firebase handles authorization and authentication for you, including OAuth2, e-mail & password login, and routing authorization.

## The Most Confusing Parts of Firebase

This section lists the most confusing parts of Firebase. This section is only about `$firebaseArray` and `$firebaseObject`. The confusing parts of `$firebaseAuth` are covered in the Firebase Auth chapter.

### Firebase Methods vs. JavaScript Methods

Firebase is a superset of JavaScript. You could write your app in JavsScript and bind your arrays and objects to Firebase, as if Firebase were a back end database like MongoDB. But you'd be missing much of Firebase's advantages. Firebase is a superset of JavaScript, with its own methods and special objects. Firebase methods start with `$` and the special objects start with `$$`.

For example, to add an object to array in JavaScript you use `push()`. In Firebase you use `$add()`.

To remove an object from an array in JavaScript you use `splice()`. In Firebase you use `$remove()`.

If you use JavaScript methods such as `push()` and `splice()` you won't get Firebase objects with keys, and Firebase methods won't work. Look for the equivalent Firebase method, e.g., `$add` instead of `push()`, and `$remove` instead of `splice()`. Don't mix JavaScript methods with Firebase methods.

### Firebase Objects vs. JavaScript (JSON) Objects

Firebase objects are different from JavaScript objects. Let's make an array of cruddy movies. Each movie will be an object in the array. Each movie will have an array of comments, and each comment is an object. We'll start with JavaScript Object Notation (JSON):

```js
[
  {
    title: "Plan 9 from Outer Space",
    director: "Edward D. Wood, Jr.",
    plot: "Aliens resurrect dead humans as zombies and vampires to stop humanity from creating the Solaranite (a sort of sun-driven bomb).",
    year: "1959",
    comments: [
      {
        commentText: 'I was just thinking about this movie.',
        commentAuthor: 'Ludwig Wittgenstein'
      },
      {
        commentText: 'I love Vampira!',
        commentAuthor: 'Bertrand Russell'
      }
    ]
  },
  {
    title: "Battlefield Earth",
    actors: "Tom Cruise",
    ...
  }
]
```

Now we'll look at the Firebase object:

```js
[
  {
    $$conf: Object,
    $id: "-KGSpDSJEtvCHNeXWriS",
    $priority: null,
    title: "Plan 9 from Outer Space",
    director: "Edward D. Wood, Jr.",
    plot: "Aliens resurrect dead humans as zombies and vampires to stop humanity from creating the Solaranite (a sort of sun-driven bomb).",
    year: "1959",
    comments: [
      {
        $$conf: Object,
        $id: "-KGSpDSJEtvCH23ert654",
        $priority: null,
        commentText: 'I was just thinking about this movie.',
        commentAuthor: 'Ludwig Wittgenstein'
      },
      {
        $$conf: Object,
        $id: "-KGSpDSJEtvCwer34he0",
        $priority: null,
        commentText: 'I love Vampira!',
        commentAuthor: 'Bertrand Russell'
      }
    ]
  },
  {
    $$conf: Object,
    $id: "-KGSpDSJEtefwef434e21",
    $priority: null,
    title: "Battlefield Earth",
    actors: "Tom Cruise",
    ...
  }
]
```

Firebase adds three metadata properties to objects:

* `$$conf` is an object that consists of metadata for what the object binds to, listeners assigned to the object, the `ref` that the object is chained to, and what the object is synchronized to.
* `$id` is the object's key.
* `$priority` specifies the order in which directives apply to a DOM element.

Note that only objects have these three metadata properties. Arrays don't have them. Primitives (booleans, strings, numbers) don't have them.

To add confusion, in the Firebase Dashboard displays objects like this:

```js
[
  {
    $id: "-KGSpDSJEtvCHNeXWriS",
    {
      title: "Plan 9 from Outer Space",
      director: "Edward D. Wood, Jr.",
      plot: "Aliens resurrect dead humans as zombies and vampires to stop humanity from creating the Solaranite (a sort of sun-driven bomb).",
      year: "1959",
      comments: [
        {
          $id: "-KGSpDSJEtvCH23ert654",
          {
            commentText: 'I was just thinking about this movie.',
            commentAuthor: 'Ludwig Wittgenstein'
          }
        },
        {
          $id: "-KGSpDSJEtvCwer34he0",
          {
            commentText: 'I love Vampira!',
            commentAuthor: 'Bertrand Russell'
          }
        }
      ]
    }
  },
  {
    $id: "-KGSpDSJEtefwef434e21",
    {
      title: "Battlefield Earth",
      actors: "Tom Cruise",
      ...
    }
  }
]
```

If that were how Firebase data were structured then dot notation wouldn't work, e.g., `movie.title`. Not that you should use dot notation with Firebase...

### Firebase Child Notation vs. JavaScript Dot Notation

We can't use JavaScript dot notation with Firebase methods. Instead we use _Firebase child notation_:

```js
$firebaseArray(ref.child($routeParams.id).child('comments'));
```

This may be the most confusing area of Firebase. Let me count the ways...

1. The arguments for a child array and a child object are different. A child array is selected by the name of its property, in quotes, as the argument, e.g., `.child('comments')`. That's easy enough. But selecting a child object from an array uses the object's key as the argument. But you wouldn't want to hardcode the key, e.g., `.child('-KGSpDSJEtvCHNeXWriS')`. You need to represent the key as a variable using the `$id` method, e.g., `.child(movie.$id)`.
2. You don't chain a Firebase method to the child notation, e.g., `...child('comments').$remove()`. Instead you create a `$firebaseArray` or `$firebaseObject` with the child notation, set that on the `$scope`, and chain the Firebase method to the array or object on the `$scope`.
3. It's easy to mistakenly mix JavaScript dot notation with Firebase child notation, and get the error message "...is not a function." For example, ``movies($routeParams.id).child('comments').$remove(comment)`` won't work because `movies` is dot notation. You don't see a dot, but somewhere in the steps to create that array and put it on the `$scope` there was a dot.
4. I'll repeat the last point because it's so easy to make a mistake. When you write JavaScript you get used to chaining objects and methods with dot notation you'll write several steps that work as expected, because you don't have a Firebase method in the chain. Then in the next step you try to use a Firebase method and it doesn't work, because several steps earlier you used dot notation.

### Objects vs. Records

Three Firebase array methods can take the _record_ as an argument. These methods are:

* `$save(recordOrIndex)`
* `$remove(recordOrIndex)`
* `$keyAt(recordOrIndex)`.

A _record_ and an _object_ are not the same. You can't use objects as arguments for these methods.

```js
var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");

$scope.movies = $firebaseArray(ref); // get movies array

$scope.movie = $firebaseObject(ref.child($routeParams.id)); // get movie object

$scope.movie.$loaded().then(function() {
  console.log($scope.movie); // d {$$conf: Object, $id: "-KHQMGal4h1HuME3dcLQ", $priority: null, movieActors: "Lilla Labanc, Kinga Czifra, Ádám Csernóczki, Attila Jankóczky", movieAwards: "N/A"…}
  var record = $scope.movies.$getRecord($scope.movie.$id);
  console.log(record); // Object {movieActors: "Lilla Labanc, Kinga Czifra, Ádám Csernóczki, Attila Jankóczky", movieAwards: "N/A", movieCountry: "Hungary", movieDateAdded: 1462895450585, movieDirector: "Gábor Forgács"…}
  console.log(record === $scope.movie) // false
});
```

![Record vs. Object](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_record_object.png)

The record and the movie object are identical except that the movie object has a `$$conf` property.

The _object_ includes a `$$conf` object.

The _record_ is the keys and values of the object, i.e., the regular ol' JavaScript object without metadata.

If you get the error message "Invalid record; could determine key for [object Object]", after you wonder whether that was supposed to say "_couldn't_ determine key", this error message means that you used an object when the method is expecting a record. Translated into English, the error message means, "Invalid record; the argument for this method appears to be an object not a record. Use $getRecord(key) to get an object's record."

Firebase doesn't have a `$record` return method, e.g., `movie.$record` would return the movie object without the `$$conf` metadata. That would be similar to the `$id` return method, e.g., `movie.$id` returns the movie object's key. The only way to get an object's record is to use `$getRecord(key)` on the array.

### Async Operations

Database queries are always asynchronous. If you have code to execute after a Firebase method always put the code in a promise. For example, after a user deletes a movie from our array of cruddy movies, we return the user to the home page. Synchronously we would write this:

```js
$scope.deleteMovie = function() {
  $scope.movie.$remove(); // delete movie from array of movies
  $location.path( "/movies" ); // return user to home page
};
```

The user would be returned to the home page, see the deleted movie in the array of movies, and then moments later the movie would disappear from the array. That would be weird.

Asynchronously we would write:

```js
$scope.deleteMovie = function() {
  $scope.movie.$remove().then(function() { // delete movie from array of movies
    $location.path( "/movies" ); // return user to home page
  }, function(error) {
    console.log(error);
  });
};
```

The user is moved to the home page after the movie is removed from the array.

Always execute your code in promises. You shouldn't experience async problems. If you suspect async conditions, e.g., logging a value returns `null`, then use the `$loaded()` method (below) for debugging.

### Array Methods and Object Methods With the Same Names

Some Firebase array methods and object methods have the same names but do different things. In particular, keep an eye on `$remove()` and `$save()`.

The object version of `$remove()` removes an object from an array, if it's chained to an object. But if it's chained to an array (because you thought you were using the array version) it removes the entire array!

The object version of `$save()` saves the object it's chained to. The array version saves an object's _record_ but doesn't objects. The saved record then updates the object so the object gets saved, if you set all this up correctly. More on this later.

## Setting Up Your Firebase App

This section presents the "boilerplate" for setting up your app to use Firebase.

### Inject firebase Dependency To Angular Module

Inject `firebase` into your Angular module:

```js
var app = angular.module("CRUDiestMoviesApp", ['ngRoute', 'ui.bootstrap', 'firebase']);
```

### new Firebase Constructor Function

In each controller we connect to Firebase with the `new` operator and the Firebase constructor function:

```js
// Connect to Firebase
var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");
```

This is the easiest part of Firebase. The syntax never changes and is done once in each controller, near the top.

### $firebaseArray To Access the Entire Database

If your controller will be working with an array, inject the service `$firebaseArray` into the controller:

```js
app.controller('MyCtrl', ['$scope', '$firebaseArray', function($scope, $firebaseArray) {

});
```

Then reference the array and put it on the `$scope`. The top level of Firebase is always an array so if we want access to all the movies in our database we use:

```js
$scope.movies = $firebaseArray(ref);
```

> What are these things called? Array access links?

Now the entire database is available on the `$scope`.

### $firebaseObject to Access Objects

If your controller will be working with objects, inject the service `$firebaseObject` into the controller:

```js
app.controller('MyCtrl', ['$scope', '$firebaseObject', function($scope, $firebaseArray) {

});
```

You can inject both the `$firebaseArray` service and the `$firebaseObject` service into a controller.

Then put the object on the `$scope` using Firebase child notation:

```js
$scope.movie = $firebaseObject(ref.child($routeParams.id));
```

> What are these things called? Object access links?

`ref` is the entire Firebase array, which we just on the `$scope` as `$scope.movies`. Now we're selecting one movie from the array and calling it `$scope.movie`. In Firebase child notation we select an object from an array by using the key as the argument to `.child()`. Here I'm taking the key from the URL, where we passed it from another view.

> If you really want to screw up your app, put an object on the `$scope` using `$firebaseObject` and Firebase child notation and then access the same object using `$firebaseArray` and dot notation. You'll have two objects in your `$scope` that you think are the same object. On top of that one object will have keys and the other won't, so Firebase methods will sometimes work and sometimes say "...is not a function".

### Nested Firebase Array

To access a nested array, we don't use dot notation with the full array, e.g., `$scope.movies.movie.comments`. We set up another `$firebaseArray` and use Firebase child notation:

```js
$scope.comments = $firebaseArray(ref.child($routeParams.id).child('comments'));
```

### Nested Firebase Object

To access a nested object set up another `$firebaseObject` and use Firebase child notation:

```js
$scope.comment = $firebaseObject(ref.child($routeParams.id).child('comments').child(commentID));
```

`commentID` is passed in from `comment.$id` or similar.

### More Than One Array or Object in a Controller

If your controller works with more than one array and/or object, make access links to each array and/or code. To repeat for the fifteenth time, don't set up an access link to a high-level array or object and then use dot notation to access nested arrays and objects.

```js
// Access all movies in array
$scope.movies = $firebaseArray(ref);

// Set up single movie object
$scope.movie = $firebaseObject(ref.child($routeParams.id));

// Set up comments array
$scope.comments = $firebaseArray(ref.child($routeParams.id).child('movieComments'));

// Set up single comment
$scope.comment = $firebaseObject(ref.child($routeParams.id).child('comments').child(commentID));
```

> What are these called? Array and object accessors?

## Firebase Object Methods

Firebase has ten methods that can be chained to a `$firebaseObject`:

The methods I use:
* `$save()`
* `$remove()`
* `$watch(callback, context)`
* `$id`

The method I use for debugging but not in production:
* `$loaded()`

The methods I've never used:
* `$unwatch()`
* `$bindTo(scope, varName)`
* `$ref()`
* `$destroy()`

You'll notice that all Firebase methods start with `$`. Also note that most `$firebaseObject` methods don't take an argument.

### $save()

After you change data in a property in the `$scope` you execute [`$save`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-save) to save the changes to the remote database. For example, let's say that we change the movie _The Girl Who Played With Fire_ to be better titled _The Girl Who Played With Firebase_. We then save the change:

```js
$scope.movie.$save().then(function() {
  console.log("Movie update saved.");
}, function(error) {
  console.log("Error, movie update not saved.");
  console.log(error);
});;
```

Note that we don't pass the title into the function and make it an argument of `$save`:

```js
$scope.change = function(title) {
  $scope.movie.$save(title);
};
```

`$save()` never takes an argument. It always saves the entire object it's chained to.

Note also that `$save()` is used only with objects, not with arrays.

### $remove()

To delete an object from an array, chain [`$remove()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-remove) to the object. For example, users many want to remove a movie from the array of movies:

```js
$scope.deleteMovie = function() {
  $scope.movie.$remove().then(function() { // delete movie from array of movies
    console.log("Movie deleted.");
    $location.path( "/movies" ); // return user to home page
  }, function(error) {
    console.log("Error, movie not deleted.");
    console.log(error);
  });
};
```

`$remove()` never takes an argument. It deletes the entire object it's chained to.

### $watch(callback, context)

[`$watch()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-watchcallback-context) sets up an event listener that executes its promise when the data in the remote Firebase changes. In the movies database we use this after a user updates information about a movie, to show a message that the user's data has been saved.

```js
$firebaseObject(ref.child($routeParams.id).child('movieTitle')).$watch(function(event) {
  $scope.watch.titleSave = true; // Show "Saved!" message
  $timeout(function() {
    $scope.watch.titleSave = false; // Hide "Saved!" message
  }, 9900);
});
```

Apparently calling `$watch()` a second time on an object unregisters the event listener. I've never tried this.

The `context` second argument is undocumented. The `$watch(callback[, context])` array method has the same second argument but it's optional. It's not clear whether the second argument in the `$watch(callback, context)` object method is optional.

### $id To Return Key

`$id` a _return method_ (`$save()`, `$remove()`, and `$watch()` are _mutator methods_.) `$id` returns an object's key. This is essential, and can be tricky, when using child notation to set up an object accessor:

```html
<div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
  <a ng-href="/#/movies/{{movie.$id}}"><img class="largeMoviePoster" ng-src="{{movie.moviePoster}}" alt="{{movie.movieTitle}}"></a>
</div>
```

`movie.$id` gets the key of the selected movie object. We then pass the key into the URL for the `SHOW/EDIT` page view.

```js
$scope.movie = $firebaseObject(ref.child($routeParams.id));
```

In the `ShowController.js` we take the key from the URL to put the movie object on the `$scope`.

### $loaded()

[`$loaded()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-loaded) resolves a promise after a `$firebaseObject` has been downloaded from the remote Firebase:

```js
// Connect to Firebase
var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");

// Set up single movie object
$scope.movie = $firebaseObject(ref.child($routeParams.id));

$scope.movie.$loaded().then(function(movie) {
  console.log(movie);
});
```

When your movie object is downloaded from the remote Firebase it will be logged in your console window.

If you have problems with values being `null` or `undefined` check if you're running code synchronously after a query to the database. `$loaded()` should only be needed for testing and debugging async problems. If you use promises after every Firebase method you should never need to use `$loaded()`.

### $bindTo()

[`$bindTo()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-bindtoscope-varname) sets up a promise after an access link establishes three-way data binding to an object:

```js
// Set up movie object without a promise
$scope.movie = $firebaseObject(ref.child($routeParams.id));

// Set up movie object with a promise
$firebaseObject(ref.child($routeParams.id)).$bindTo($scope, 'movie').then(function() {
  console.log("On scope with three-way binding!");
});
```

I've never needed to use `$bindTo()`.

### $ref()

[`$ref()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-ref) returns the `ref` of an object. E.g., the `ref` of a movie object can be logged with:

```js
console.log($scope.movie.$ref());
```

What's returned is an object:

```js
X {
  catch: undefined
  k: Ji
  {
    Fd: oi
    {
      Qc: Object
      {
        af: (a,b,f,g)
      }
      ...
    }
  }
}
```

I have no idea what any of that means and I've never used `$ref()`.

### $destroy()

[`$destroy()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-destroy) cancels event listeners. Why it's not called `$unwatch()` or how it differs from calling `$watch()` a second time I don't know. I've never used it.

### $priority

[`$priority`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-priority) is a _return method_, and I've never used it. It returns the priority of an object. According to the [Angular documentation](https://docs.angularjs.org/api/ng/service/$compile):

> "When there are multiple directives defined on a single DOM element, sometimes it is necessary to specify the order in which the directives are applied. The priority is used to sort the directives before their compile functions get called. Priority is defined as a number. Directives with greater numerical priority are compiled first. Pre-link functions are also run in priority order, but post-link functions are run in reverse order. The order of directives with the same priority is undefined. The default priority is 0."

### $value

[`$value`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebaseobject-value) is the third and last _return method_ for Firebase objects. It returns the value of a property if the value is a primitive (boolean, string, or number).

## Firebase Array Methods

Firebase also has methods for arrays. Some of the array methods have the same names as the object methods but do different things.

### $add(newData)

[`$add(newData)`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-addnewdata) puts a new object into the array. It's like JavaScript's `push()` method. For example, we'll take form data that the user entered, make a movie object, add the movie object to our array of movies, and then execute a promise:

```js
$scope.addMovie = function (formData) {
  var movie = {
    actors: formData.actors,
    director: formData.director,
    plot: formData.plot,
    poster: formData.poster,
    title: formData.title,
    year: formData.year,
    dateAdded: Date.now()
  };
  $scope.movies.$add(movie).then(function() {
    console.log("Movie added!");
  });
});
};
```

### $save(recordOrIndex)

[`$save(recordOrIndex)`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-saverecordorindex) saves one record in an array.

This is one of the three methods that can take a record as an argument. Recall the section above explaining that objects and records are different.

If we try to save an object we get an error message:

```js
$scope.movie.$loaded().then(function() {
  console.log($scope.movie.title); // Dream.net
  $scope.movie.title = "Bad Movie"; // We change the movie's title to something more accurate
  $scope.movies.$save($scope.movie).then(function() {
    console.log("Saved!");
  }, function(error) {
    console.log("Error, not saved."); // Error, not saved.
    console.log(error); // Invalid record; could determine key for [object Object]
  });;
});
```

The error message is "Invalid record; could determine key for [object Object]".

If we change the object and save the record we get different movie titles on the `$scope` and in the Firebase Dashboard:

```js
$scope.movie.$loaded().then(function() {
  console.log($scope.movie.title); // Dream.net
  $scope.movie.title = "Bad Movie"; // We change the movie's title to something more accurate
  var record = $scope.movies.$getRecord($scope.movie.$id);
  console.log(record.title); // Dream.net
  $scope.movies.$save(record).then(function() {
    console.log("Saved!"); // Saved!
    console.log($scope.movie.title); // Bad Movie
    console.log(record.title); // Dream.net
  }, function(error) {
    console.log("Error, not saved.");
    console.log(error);
  });;
});
```

We started with the same title in both the object (`$scope.movie.title`) and the record (`record.title`). We changed the title in the object and then saved the record. The result was different titles in the object and the record, i.e., the local object and the remote object were out of sync.

Note that this doesn't produce an error. Firebase is doing what it's supposed to do.

Let's try changing the movie title in the record, but not in the object, and then saving the record:

```js
$scope.movie.$loaded().then(function() {
  console.log($scope.movie.title); // Dream.net
  var record = $scope.movies.$getRecord($scope.movie.$id);
  console.log(record.title); // Dream.net
  record.title = "Bad Movie" // We change the movie's title in the record
  $scope.movies.$save(record).then(function() {
    console.log("Saved!"); // Saved!
    console.log($scope.movie.title); // Bad Movie
    console.log(record.title); // Bad Movie
  }, function(error) {
    console.log("Error, not saved.");
    console.log(error);
  });;
});
```

Did you expect to get the opposite results of the last code block:

```js
console.log($scope.movie.title); // Dream.net
console.log(record.title); // Bad Movie
```

Instead we got the results we wanted:

```js
console.log($scope.movie.title); // Bad Movie
console.log(record.title); // Bad Movie
```

The title changed in both the object and the record. The local object and the remote object are now in sync.

We have to get the record, change the record, and save the record. This changes the `$scope` automatically. Changing the movie object doesn't change the record but changing the record (and saving the record) changes the movie object. Changes to the movie object can be saved to the remote Firebase by using the `$save()` object method but changes to the movie object can't be saved to the remote Firebase by using the `$save(recordOrIndex)` array method.

Given all this squirrelliness, my advice is to use the `$save()` object method if possible instead of the `$save(recordOrIndex)` array method.

### $keyAt(recordOrIndex)

[`$keyAt(recordOrIndex)`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-keyatrecordorindex) returns the key of an object in an array:

```js
$scope.movie.$loaded().then(function() {
  console.log($scope.movie); // d {$$conf: Object, $id: "-KHQMGal4h1HuME3dcLQ", $priority: null, movieActors: "Lilla Labanc, Kinga Czifra, Ádám Csernóczki, Attila Jankóczky", movieAwards: "N/A"…}
  console.log($scope.movie.$id); // -KHQMGal4h1HuME3dcLQ
  var record = $scope.movies.$getRecord($scope.movie.$id);
  console.log(record); // Object {movieActors: "Lilla Labanc, Kinga Czifra, Ádám Csernóczki, Attila Jankóczky", movieAwards: "N/A", movieCountry: "Hungary", movieDateAdded: 1462895450585, movieDirector: "Gábor Forgács"…}
  var key = $scope.movies.$keyAt(record);
  console.log(key); // -KHQMGal4h1HuME3dcLQ
});
```

Let's try that with an index instead of a record:

```js
$scope.movie.$loaded().then(function() {
  console.log($scope.movie); // d {$$conf: Object, $id: "-KHQMGal4h1HuME3dcLQ", $priority: null, movieActors: "Lilla Labanc, Kinga Czifra, Ádám Csernóczki, Attila Jankóczky", movieAwards: "N/A"…}
  console.log($scope.movie.$id); // -KHQMGal4h1HuME3dcLQ
  var index = $scope.movies.$indexFor($scope.movie.$id);
  console.log(index); // 92
  var key = $scope.movies.$keyAt(index);
  console.log(key); // -KHQMGal4h1HuME3dcLQ
});
```

Let's try this without knowing the key in advance. We can't get the index because `$indexFor(key)` requires the key. We can't get the record because `$getRecord(key)` requires the key. We'll try using the movie object instead of the record:

```js
$scope.movie.$loaded().then(function() {
  console.log($scope.movie); // d {$$conf: Object, $id: "-KHQMGal4h1HuME3dcLQ", $priority: null, movieActors: "Lilla Labanc, Kinga Czifra, Ádám Csernóczki, Attila Jankóczky", movieAwards: "N/A"…}
  var key = $scope.movies.$keyAt($scope.movie);
  console.log(key); // null
});
```

That returned `null`. It looks like you can use the record or the index, but not the object. I don't know how to get the record or index without having the key so this method seems useless.

### $remove(recordOrIndex)

[`$remove(recordOrIndex)`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-removerecordorindex) removes an object from an array, similar to the JavaScript method `$splice()`.

Note that there is `$firebaseArray` method with the same name. The array method removes an object from an array. The object method removes whatever it's chained to, which should be an object in an array if you wrote your code correctly, but could be the entire array if you weren't paying attention:

```js
$scope.comments = $firebaseArray(ref.child($routeParams.id).child('movieComments');
$scope.comments.$remove(comment));
```

removes one comment from the array of comments.

```js
$scope.comments = $firebaseObject(ref.child($routeParams.id).child('movieComments');
$scope.comments.$remove(comment));
```

removes the entire array of comments, i.e., all the comments.

#### Broken Code Quiz

What's wrong with this code? In the controller:

```js
$scope.movie = $firebaseObject(ref.child($routeParams.id));
```

In the view:

```html
<div ng-repeat="comment in movie.comments">
  <div class="commentText">{{comment.commentText}}</div>
  <form>
    <button ng-click="comments.$remove(comment)">Delete Comment</button>
  </form>
</div>
```

OK, it completely lacks styling, but there's something else wrong. `movie.comments` uses dot notation and `$remove()` can't chain to an array. You'll get the error message "$remove is not a function". The correct code is

```js
$scope.comments = $firebaseArray(ref.child($routeParams.id).child('movieComments'));
```

```html
<div ng-repeat="comment in comments">
  <div class="commentText">{{comment.commentText}}</div>
  <form>
    <button ng-click="comments.$remove(comment)">Delete Comment</button>
  </form>
</div>
```

### $getRecord(key)

[$getRecord(key)](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-getrecordkey) returns the record associated with an object in an array. Records and objects aren't the same thing:

```js
var ref = new Firebase("https://crudiest-firebase.firebaseio.com/");

$scope.movies = $firebaseArray(ref); // get array of movies

$scope.movies.$loaded().then(function() { // when the array is loaded
  $scope.movie = $firebaseObject(ref.child($routeParams.id)); // get the movie object
  var record = $scope.movies.$getRecord($scope.movie.$id); // get the record for the movie object
  console.log(record);
  console.log($scope.movie);
  console.log(record === $scope.movie); // false, the record object and the movie object are different
});
```

![Saved message](https://github.com/tdkehoe/Learn-To-Code-By-Breaking-Stuff/blob/master/media/crudfb_record_object.png)

The record and the object are almost identical except that the object includes a `$$conf` object. I googled "Firebase $$conf" but it couldn't find anything. The record appears to be the object minus Firebase metadata. There should be a Firebase method to return the record from an object, e.g., `movie.$record` (similar to `movie.$id`) but there isn't.

### $indexFor(key)

[$indexFor(key)](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-indexforkey) returns the index of an object, if you know the key.

There's no similar object method, e.g., `$index()` analogous to `$id()`. I.e., if you don't have the key there's no alternative method to use.

The documentation is incorrect. It shows that you can use the value of an element as the argument:

```js
// assume records "alpha", "bravo", and "charlie"
var list = $firebaseArray(ref);
list.$indexFor("alpha"); // 0
list.$indexFor("bravo"); // 1
list.$indexFor("zulu"); // -1
```

### $loaded()

[`$loaded()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-loaded) is similar to the object method of the same name.

### $ref()

[`$ref()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-ref) is similar to the object method of the same name.

### $watch(cb[, context])

[`$watch(cb[, context])`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-watchcb-context) is interchangeable with the `$watch(callback, content)` object method. However, the array method returns an object with three keys:

* `event` is the database event type which fired (`child_added`, `child_moved`, `child_removed`, or `child_changed`).
* `key` is the ID of the record that triggered the event.
* `prevChild` is provided if the event was `child_added` or `child_moved`, then this contains the previous record’s key or null if key belongs to the first record in the collection.

The `context` second argument is undocumented. It'd be nice if you could put in conditions, e.g., not to fire the watch on page load.

### $destroy()

[`$destroy()`](https://www.firebase.com/docs/web/libraries/angular/api.html#angularfire-firebasearray-destroy) cancels event listeners. It appears to be identical to the object method of the same name. I've never used it.
