# Homepage Styling

In this chapter you'll add features and style the homepage.

An overview and directory for the entire project is in the [README.md](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/README.md).

## Table of Contents

* [Title Font](## Title Font)
* [Styling](## Styling)
* [Responsive Views](## Responsive Views)
* [Order By Options](## Order By Options)
* [Tech Notes and Resume](## Tech Notes and Resume)
* [Highlight Movies](## Highlight Movies)

## Title Font

First, we need a better font! Google "movie fonts" and you'll see many choices. I like the "Terminator" font so I downloaded `Terminator.ttf` to the `fonts` directory (in the `css` directory).

Next, put the font at the top of `styles.css`, and then use the font in the `h1` header:

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

I've added a text shadow to the `<h1>` header.

![Terminator font](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_terminator.png)

## Styling

In `home.html`, let's make the placeholder font a little bigger in the `Add a Movie` search form, in `styles.css`:

```css
.addMovie {
  font-size: larger;
}
```

The `glyphicon-search` icon needs help:

```css
.glyphicon-search {
  padding-right: 40px;
  color: silver;
  font-size: larger;
}
```

The movie posters are too big and are slightly different sizes. Let's fix this:

```css
.largeposter {
  width: 16.3%;
  height: 285px;
  padding-top: 3px;
}
```

![Large movie view](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_largeMovie.png)

## Responsive Views

Six movies in a row looks good on my 15" laptop screen, but what if users have smaller screens? We'll set up Bootstrap responsive views. In `home.html`, replace:

```html
<div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
  <a ng-href="/#/movies/{{movie.$id}}"><img class="largeposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
</div>
```

with:

```html
<!-- Responsive views -->
<div class="row visible-xs-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="extraSmallposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
  </div>
</div>

<div class="row visible-sm-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="smallposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
  </div>
</div>

<div class="row visible-md-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="mediumposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
  </div>
</div>

<div class="row visible-lg-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="largeposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
  </div>
</div>
```

Then in `styles.css` we'll set five movies in a row on medium screens, four movies in a row on small screens, and two movies in a row on mobile devices. Add these styles above `.largeposter`:

```css
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
```

Now you can make you browser window bigger and smaller and see the number of movies in each row change.

## Order By Options

Perhaps users would like some options for ordering the movies? Let's give users a choice of ordering by date added (the default), by IMDb rating, or by year. We'll have forward and reverse ordering for each, so a total of six buttons. In `home.html`, below the "add movie form" and above the responsive views:

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
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = true">Order By Recently Added</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = false">Order By First Added</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = false">Order By Worst First</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = true">Order By Best First</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = true">Order By Newest</button>
  </div>
  <div class="col-xs-12">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = false">Order By Oldest</button>
  </div>
</div>

<!-- orderBy buttons row, small, medium, and large screens -->
<div class="row visible-sm-block visible-md-block visible-lg-block">
  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = true">Recent</button>
  </div>
  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = false">First</button>
  </div>
  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = false">Worst</button>
  </div>
  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = true">Best</button>
  </div>
  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = true">Newest</button>
  </div>
  <div class="col-sm-2 col-md-2 col-lg-2">
    <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = false">Oldest</button>
  </div>
</div>
```

![Order by buttons](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_order_by.png)

Make your screen bigger and smaller to check the responsive views. On mobile devices the labels row is hidden. The buttons are full-width and have more verbose labels.

This code block is almost all Bootstrap so I'll try to explain it. Bootstrap views are always twelve columns wide. We have six buttons so, on small, medium, and large screens, the buttons are each two columns, with labels above the buttons.

`<button type="button">` makes a button.

`class="btn"` styles the button with Bootstrap's button CSS.

`btn-success` adds a color, based on Bootstrap's color standards:

* `default` is white.
* `primary` is dark blue.
* `success` is green.
* `info` is light blue.
* `warning` is yellow-orange.
* `danger` is red.

`btn-block` makes the button the full width of the container, in this case, two columns.

Angular's `ng-click` sets what the buttons do when clicked. Each button sets two variables, `order` and `reverse`. There are three options for `order`: `dateAdded`, `imdbRating`, or `year`. We could use `$id` instead of `dateAdded` as keys should be consecutive but the latter is more reliable.

Note that there's no handler in the controller for ordering the movies, i.e., we're keeping the logic in the view. Angular best practice is to put logic in a directive or a service. This app is small so I'm putting the logic in the the view or the controller.

## Tech Notes and Resume

I'm looking for a job so I want to tell hiring managers about how I built this project, and I want them to download my resume. Let's make two buttons. The `Add a Movie` field is wider than necessary so let's make that half the window width, and make the buttons quarter-width each. As we're using columns now we'll wrap all this in a `<div class="row">`. Replace this:

```html
<!-- Add movie form -->
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

with this:

```html
<div class="row">

  <!-- "Add a Movie" form -->
  <div class="col-sm-6 col-md-6 col-lg-6">
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
  </div>

  <div class="col-sm-3 col-md-3 col-lg-3">
    <button type="button" class="btn btn-danger btn-block" ng-click="techNotes = !techNotes">Tech Notes</button>
  </div>

  <div class="col-sm-3 col-md-3 col-lg-3">
    <a target="_self" href="../../resume/my-resume.pdf" download="T-D-Kehoe.pdf">
      <button type="button" class="btn btn-danger btn-block">Résumé</button>
    </a>
  </div>

</div>
```

![Tech notes and resume](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_tech_resume.png)

### Tech Notes

The `Tech Notes` button switches the variable `techNotes` on or off. Make a Tech Notes row below the "Add a Movie", tech notes button, and resume button row:

```HTML
<!-- Tech Notes row -->
<div class="row well well-lg" ng-show="techNotes">
  <p class="lead text-justify">AngularFire CRUD app with AngularJS, Firebase, Bootstrap, and async typeahead.</p>
  <p class="text-justify">This web app feels like a native app, i.e., the movies database feels like it's on your computer. In contrast, the <a ng-href="http://www.imdb.com">Internet Movies Database (IMDb)</a> feels like a website developed a long time ago in a galaxy far, far away...</p>
  <p class="text-justify">AngularJS binds data to Firebase, a NoSQL cloud database. There's no back end and no HTTP requests (except for the typeahead). Compared to a conventional MEAN stack web app with a Node/Express/MongoDB back-end, Firebase synchronizes the user's edits with the remote database without the user clicking a "Submit" button. When multiple users use the app at the same time, movies added by one user, or edits made by one user, appear instantly in the other users' apps.</p>
  <p class="text-justify">The "Add a Movie" data entry form uses the UI Bootstrap asynchronous typeahead. Each keystroke makes an HTTP request to the Open Movie Database, which returns ten movie titles. onSelect (clicking a movie title) fires an HTTP GET request for a single movie to the OMDB which returns the movie data. This movie is then pushed locally to the $scope.movies array. This updates the INDEX view almost instantly. Then, running in the background, an HTTP POST adds the movie to the CRUDiest Movies Firebase. Lastly, an HTTP GET request to the CRUDiest Movies Firebase gets all the movies and syncs to $scope. This is the slowest step but runs in the background while the user is doing other things.</p>
  <p class="text-justify">Users can order the movies by date added, rating, and year. The "Order By" buttons have ng-click set up with two values, <i>order</i> and <i>reverse</i>. These values are passed to the $scope and then to ng-repeat.  Adding a new movie resets the order back to date added so that the new movie always appears in the upper left position.</p>
  <p class="text-justify">The country filter shows which country has the most cruddy movies. USA! USA! And in second place, Turkey makes the most non-English turkeys. Angular instantly filters the movies. The flags are downloaded on the fly from Wikipedia.</p>
  <p class="text-justify">The app is responsive. The number of movies displayed per row changes from five in the large view, to four in the medium view, three in the small view, and then two on mobile devices.</p>

  <p class="text-justify">The <a ng-href="https://github.com/tdkehoe/CRUDiest-Firebase">repository</a> is on GitHub. There's also a tutorial if you want to make your own <a ng-href="https://github.com/tdkehoe/Firebase-Tutorial/blob/master/Firebase.md">CRUDiest Movies Firebase</a>.</p>
</div>
```

The `Tech Notes` are styled with a Bootstrap well, and the text is justified with Bootstrap.

![Tech notes well](https://github.com/tdkehoe/Firebase-Tutorial/blob/master/media/crudfb_tech_notes.png)

### Download Resume

We added a button to `home.html` for downloading a resume:

```html
<div class="col-sm-3 col-md-3 col-lg-3">
  <a target="_self" href="../../resume/my-resume.pdf" download="T-D-Kehoe.pdf">
    <button type="button" class="btn btn-danger btn-block">Résumé</button>
  </a>
</div>
```

You can put your resume in the directory `resume`, change the filename in the `<a>` anchor, and it should download. Note that the downloading is done by an HTML `<a>` anchor, not by Angular or Bootstrap or Firebase.

I don't like the underline when you hover over the button. Let's get rid of it, in `styles.css`:

```css
.resume:link {
  text-decoration: none;
}
```

## Highlight Movies

Users may not know that they can click movie posters to go to the `SHOW/EDIT` page. Let's highlight each movie in blue on mouse hover, in `styles.css`:

```css
.extraSmallposter:hover, .smallposter:hover, .mediumposter:hover, .largeposter:hover {
  outline: 5px solid blue;
}
```

We're using the _group selector_ or comma to apply this style to more than one class. The colon makes these _pseudo-classes_, e.g., `.largeposter` is a class and `.largeposter:hover` is a pseudo-class.

## Filter By Country

Hiring managers like clicking buttons, and flags are colorful. And you did want to know which country leads the world in the cruddy movies? USA! USA! But which country is second? Turkey makes the most non-English turkeys!

Adding a country filter will make you love Angular, if you don't already. Start by adding a row of flags, below the `orderby` buttons row:

```html
<!-- flags row -->
<div class="row flags">
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = undefined " ng-src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/2f/Flag_of_the_United_Nations.svg/1200px-Flag_of_the_United_Nations.svg.png" alt="United Nations flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'USA' "ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/a/a4/Flag_of_the_United_States.svg/1235px-Flag_of_the_United_States.svg.png" alt="United States flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'UK' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/a/ae/Flag_of_the_United_Kingdom.svg/1200px-Flag_of_the_United_Kingdom.svg.png" alt="UK flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'India' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/4/41/Flag_of_India.svg/1350px-Flag_of_India.svg.png" alt="India flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'Turkey' " ng-src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/Flag_of_Turkey.svg/1200px-Flag_of_Turkey.svg.png" alt="Turkey flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'Germany' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/b/ba/Flag_of_Germany.svg/250px-Flag_of_Germany.svg.png" alt="Germamy flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'France' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/c/c3/Flag_of_France.svg/900px-Flag_of_France.svg.png" alt="France flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'Israel' " ng-src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Flag_of_Israel.svg/660px-Flag_of_Israel.svg.png" alt="Israel flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'Italy' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/0/03/Flag_of_Italy.svg/1500px-Flag_of_Italy.svg.png" alt="Italy flag" />
  </div>
  <div class="col-sm-1 col-md-1 col-lg-1">
    <img class="img-responsive center-block" ng-click="countryFilter = 'Brazil' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/0/05/Flag_of_Brazil.svg/720px-Flag_of_Brazil.svg.png" alt="Brazil flag" />
  </div>

</div>
```

Bootstrap is designed for twelve columns. We'll make ten flags, each one column wide, and leave two columns for a text entry form to enter other countries. You don't need to specify width in pixels, Bootstrap takes care of that.

The flags are downloaded from Wikipedia. We use `ng-src` instead of `src` because we're using Angular.

`ng-click` sets the value of the variable `countryFilter`. Let's use that variable in the movie poster display. Start with the large responsive view. Add `| filter: { country: countryFilter }`:

```html
<div class="row visible-lg-block">
  <div ng-repeat="movie in movies | orderBy : order : reverse | filter: { country: countryFilter }" class="movieIndex">
    <a ng-href="/#/movies/{{movie.$id}}"><img class="largeposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
  </div>
</div>
```

The syntax of `filter` is tricky. Here is appears to be an object with a single key and value. We're telling it to look at the `country` property in `movie` (`movie.country`), where the value is the same as the variable `countryFilter`.

The flags should work now.

We introduced the variable `order`. Set its default value in `HomeController.js`:

```js
// Set variables
$scope.loading = false;
$scope.reverse = true;
$scope.order = 'dateAdded';
$scope.countryFilter = undefined;
```

Try setting the variable as `null` and see what happens.

The flags are too close to the `Order by` buttons. Let's give them a little breathing room, in `style.css`:

```css
.flags {
  padding-top: 5px;
}
```

### Country Filter Text Entry Form

Now we'll add the text entry form for users to filter by any country. We'll start with a form for the small, medium, and large responsive views:

```html
<div class="col-sm-2 col-md-2 col-lg-2 visible-sm-block visible-md-block visible-lg-block">
  <form>
    <div class="form-group">
      <input type="text" class="form-control" ng-model="countryFilter" placeholder="Country">
    </div>
  </form>
</div>
```

This is mostly Bootstrap, setting the `div` as two columns wide and hiding it on mobile devices. Classes inside the `<form>` styles it as a Bootstrap form. We use `ng-model` instead of `ng-click` because it's a form, not a button.

If that works, add the mobile text entry form:

```html
<div class="col-xs-5 visible-xs-block">
  <h4 class="text-right">Filter by country:</h4>
</div>

<div class="col-xs-6 visible-xs-block">
  <form>
    <div class="form-group">
      <input type="text" class="form-control" ng-model="countryFilter" id="countryForm">
    </div>
  </form>
</div>
```

We're making a label in a left column and the text entry form in the right form. Otherwise it's similar to the larger views.

We did the countries filter entirely in the view (except setting the default for one variable), using only Angular

### Save and Commit Your Work

Files changed in this chapter:

```
home.html
HomeController.js
style.css
```

Your `home.html` should now look like this:

```html
<div class="row">

  <div class="row">

    <!-- "Add a Movie" form -->
    <div class="col-sm-6 col-md-6 col-lg-6">
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

  <!-- Tech Notes row -->
  <div class="row well well-lg" ng-show="techNotes">
    <p class="lead text-justify">AngularFire CRUD app with AngularJS, Firebase, Bootstrap, and async typeahead.</p>
    <p class="text-justify">This web app feels like a native app, i.e., the movies database feels like it's on your computer. In contrast, the <a ng-href="http://www.imdb.com">Internet Movies Database (IMDb)</a> feels like a website developed a long time ago in a galaxy far, far away...</p>
    <p class="text-justify">AngularJS binds data to Firebase, a NoSQL cloud database. There's no back end and no HTTP requests (except for the typeahead). Compared to a conventional MEAN stack web app with a Node/Express/MongoDB back-end, Firebase synchronizes the user's edits with the remote database without the user clicking a "Submit" button. When multiple users use the app at the same time, movies added by one user, or edits made by one user, appear instantly in the other users' apps.</p>
    <p class="text-justify">The "Add a Movie" data entry form uses the UI Bootstrap asynchronous typeahead. Each keystroke makes an HTTP request to the Open Movie Database, which returns ten movie titles. onSelect (clicking a movie title) fires an HTTP GET request for a single movie to the OMDB which returns the movie data. This movie is then pushed locally to the $scope.movies array. This updates the INDEX view almost instantly. Then, running in the background, an HTTP POST adds the movie to the CRUDiest Movies Firebase. Lastly, an HTTP GET request to the CRUDiest Movies Firebase gets all the movies and syncs to $scope. This is the slowest step but runs in the background while the user is doing other things.</p>
    <p class="text-justify">Users can order the movies by date added, rating, and year. The "Order By" buttons have ng-click set up with two values, <i>order</i> and <i>reverse</i>. These values are passed to the $scope and then to ng-repeat.  Adding a new movie resets the order back to date added so that the new movie always appears in the upper left position.</p>
    <p class="text-justify">The country filter shows which country has the most cruddy movies. USA! USA! And in second place, Turkey makes the most non-English turkeys. Angular instantly filters the movies. The flags are downloaded on the fly from Wikipedia.</p>
    <p class="text-justify">The app is responsive. The number of movies displayed per row changes from five in the large view, to four in the medium view, three in the small view, and then two on mobile devices.</p>
    <p class="text-justify">The <a ng-href="https://github.com/tdkehoe/CRUDiest-Firebase">repository</a> is on GitHub. There's also a tutorial if you want to make your own <a ng-href="https://github.com/tdkehoe/Firebase-Tutorial/blob/master/Firebase.md">CRUDiest Movies Firebase</a>.</p>
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
    <div class="col-xs-12">
      <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = true">Order By Recently Added</button>
    </div>
    <div class="col-xs-12">
      <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = false">Order By First Added</button>
    </div>
    <div class="col-xs-12">
      <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = false">Order By Worst First</button>
    </div>
    <div class="col-xs-12">
      <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = true">Order By Best First</button>
    </div>
    <div class="col-xs-12">
      <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = true">Order By Newest</button>
    </div>
    <div class="col-xs-12">
      <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = false">Order By Oldest</button>
    </div>
  </div>

  <!-- orderBy buttons row, small, medium, and large screens -->
  <div class="row visible-sm-block visible-md-block visible-lg-block">
    <div class="col-sm-2 col-md-2 col-lg-2">
      <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = true">Recent</button>
    </div>
    <div class="col-sm-2 col-md-2 col-lg-2">
      <button type="button" class="btn btn-primary btn-block" ng-click="order = 'dateAdded'; reverse = false">First</button>
    </div>
    <div class="col-sm-2 col-md-2 col-lg-2">
      <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = false">Worst</button>
    </div>
    <div class="col-sm-2 col-md-2 col-lg-2">
      <button type="button" class="btn btn-warning btn-block" ng-click="order = 'imdbRating'; reverse = true">Best</button>
    </div>
    <div class="col-sm-2 col-md-2 col-lg-2">
      <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = true">Newest</button>
    </div>
    <div class="col-sm-2 col-md-2 col-lg-2">
      <button type="button" class="btn btn-success btn-block" ng-click="order = 'year'; reverse = false">Oldest</button>
    </div>
  </div>

  <!-- flags row -->
  <div class="row flags">
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = undefined " ng-src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/2f/Flag_of_the_United_Nations.svg/1200px-Flag_of_the_United_Nations.svg.png" alt="United Nations flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'USA' "ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/a/a4/Flag_of_the_United_States.svg/1235px-Flag_of_the_United_States.svg.png" alt="United States flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'UK' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/a/ae/Flag_of_the_United_Kingdom.svg/1200px-Flag_of_the_United_Kingdom.svg.png" alt="UK flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'India' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/4/41/Flag_of_India.svg/1350px-Flag_of_India.svg.png" alt="India flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'Turkey' " ng-src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/Flag_of_Turkey.svg/1200px-Flag_of_Turkey.svg.png" alt="Turkey flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'Germany' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/b/ba/Flag_of_Germany.svg/250px-Flag_of_Germany.svg.png" alt="Germamy flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'France' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/c/c3/Flag_of_France.svg/900px-Flag_of_France.svg.png" alt="France flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'Israel' " ng-src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Flag_of_Israel.svg/660px-Flag_of_Israel.svg.png" alt="Israel flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'Italy' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/0/03/Flag_of_Italy.svg/1500px-Flag_of_Italy.svg.png" alt="Italy flag" />
    </div>
    <div class="col-sm-1 col-md-1 col-lg-1 visible-sm-block visible-md-block visible-lg-block">
      <img class="img-responsive center-block" ng-click="countryFilter = 'Brazil' " ng-src="https://upload.wikimedia.org/wikipedia/en/thumb/0/05/Flag_of_Brazil.svg/720px-Flag_of_Brazil.svg.png" alt="Brazil flag" />
    </div>

    <div class="col-sm-2 col-md-2 col-lg-2 visible-sm-block visible-md-block visible-lg-block">
      <form>
        <div class="form-group">
          <input type="text" class="form-control" ng-model="countryFilter"placeholder="Country">
        </div>
      </form>
    </div>

    <div class="col-xs-5 visible-xs-block">
      <h4 class="text-right">Filter by country:</h4>
    </div>

    <div class="col-xs-6 visible-xs-block">
      <form>
        <div class="form-group">
          <input type="text" class="form-control" ng-model="countryFilter" id="countryForm">
        </div>
      </form>
    </div>

  </div>

  <!-- Responsive views -->
  <div class="row visible-xs-block">
    <div ng-repeat="movie in movies | orderBy : order : reverse | filter: { country: countryFilter }" class="movieIndex">
      <a ng-href="/#/movies/{{movie.$id}}"><img class="extraSmallposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
    </div>
  </div>

  <div class="row visible-sm-block">
    <div ng-repeat="movie in movies | orderBy : order : reverse | filter: { country: countryFilter }" class="movieIndex">
      <a ng-href="/#/movies/{{movie.$id}}"><img class="smallposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
    </div>
  </div>

  <div class="row visible-md-block">
    <div ng-repeat="movie in movies | orderBy : order : reverse | filter: { country: countryFilter }" class="movieIndex">
      <a ng-href="/#/movies/{{movie.$id}}"><img class="mediumposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
    </div>
  </div>

  <div class="row visible-lg-block">
    <div ng-repeat="movie in movies | orderBy : order : reverse | filter: { country: countryFilter }" class="movieIndex">
      <a ng-href="/#/movies/{{movie.$id}}"><img class="largeposter" ng-src="{{movie.poster}}" alt="{{movie.title}}"></a>
    </div>
  </div>

</div>
```

Your `HomeController.js` should now look like this:

```js
app.controller('HomeController', ['$scope', `$http`, '$firebaseArray', function($scope, $http, $firebaseArray) {
  console.log("Home controller.");

  var ref = new Firebase("https://crudiest-movies-fire.firebaseio.com/");
  $scope.movies = $firebaseArray(ref);

  // Set variables
  $scope.order = 'dateAdded';
  $scope.countryFilter = undefined;

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
  $scope.loading = true; // switch on the glyphicon to indicate that the data is loading
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

.flags {
  padding-top: 5px;
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
git commit -m "Home page updated."
git push origin master
```
