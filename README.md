# My Project Name

##### What is It?

#### By Cheryl Frankenfield, 1/4/18

## Description

_Create a game/app/website that..._

## Setup/Installation Requirements

* Navigate to your terminal.
* Perform Git clone command on the repo [here.]()
* Perform Git atom . command to open all files in repo.
* View index.html in browser.
* No other setup or install necessary.
* Or [click here]() to view in browser now.

## Planning

1. Configuration/dependencies
  * This should include ALL dependencies.
  * It should also include WHERE they are defined and used in the project
  * It could include a short description of what each does for you

2. Specs
_The game should ._
* _Input:_
* _Output:_

3. Integration
  * Initial routes or index pages with all dependencies in Controller/index.html head
  * Template/html page for user story...
  * Template/html page for user story...
  * Template/html page for user story...
  * Display...
  * Integrate feature that...

4. UX/UI
    * Include and modify bootstrap/materialize/Sass etc.
    * Develop custom style

## Steps for Asset Pipeline / npm runtime / Gulp tasks
_Manner of efficiently preparing dev files (HTML, CSS, JS) for the browser. Node.js — a runtime environment — lets us run JS from command line and allows for file system access. Runtime is collection of software/hardware that enable a program to be executed._

_On root directory. Creates a manifest file - package.json_
1. npm init

_Runs dev tasks. JS package. Node_modules folder is created. --save-dev only used for dev process, not final dist or build_
2. npm install gulp --save-dev

_Package that collects code that will make sense to the browser. Uses exports and require to translate code into JS that broswer will understand._
3. npm install browserify --save-dev

4. Create a .gitignore file.
_Include:
.DS_Store
node_modules/
sass-cache/
*.map
bower_components/
build/
tmp/_

_Create gulpfile.js in root directory._
5. const gulp = require('gulp');

_Package responsible for putting the browserified source code into a new file._
6. npm install vinyl-source-stream --save-dev

_Add to gulpfile.js_
7.
const browserify = require('browserify');
const source = require('vinyl-source-stream');

_Add browserify function. Key = entries, value = array of file names. Run task gulp jsBrowserify and dist folder is created._
8.
gulp.task('jsBrowserify', ['concatInterface'], function() {
  return browserify({ entries: ['./tmp/allConcat.js'] })
    .bundle()
    .pipe(source('app.js'))
    .pipe(gulp.dest('./dist/js'));
});

_Package to concat multiple JS files into one. In gulpfile.js, put it before browserify task, since this concat task will happen before browserify task. *globbing - make sure to name files with ext -interface.js_
9. npm install gulp-concat --save-dev

const concat = require('gulp-concat');

_gulp.task('concatInterface', function() {
  return gulp.src(['./js/*-interface.js'])
    .pipe(concat('allConcat.js'))
    .pipe(gulp.dest('./tmp'));
});_

_Minifying removes white space and optimizes code. Add more code to .gulpfile.js_
10. npm install gulp-uglify --save-dev
const uglify = require('gulp-uglify');

gulp.task('minifyScripts', ['jsBrowserify'], function(){
  return gulp.src("./dist/js/app.js")
    .pipe(uglify())
    .pipe(gulp.dest("./dist/js"));
});

_Add Build or Dist task to end of file._
11.
gulp.task('dist', function(){
  if (distProduction) {
    gulp.start('minifyScripts');
  } else {
    gulp.start('jsBrowserify');
  }
});

_Packages to manage environmental variables. Indicates which dev or production is being referenced. Run either gulp build or gulp build --production_
12. npm install gulp-util --save-dev
const utilities = require('gulp-util');
const distProduction = utilities.env.production;

_Clean tasks package. Deletes dist/build and tmp folders. Delete older version and then run dist again._
13. npm install del --save-dev
const del = require('del');

gulp.task('clean', function(){
  return del(['dist', 'tmp']);
});

_see above and edit to include clean task to run before dist_
gulp.task('dist', ['clean'], function(){
  if (distProduction) {
    gulp.start('minifyScripts');
  } else {
    gulp.start('jsBrowserify');
  }
});

_Linter is a tool to analyze code and warn of style or bugs. JSHint. Put at bottom of gulpfile. Run periodically to help with debugging. (gulp jshint)_
14.
npm install jshint --save-dev
npm install gulp-jshint --save-dev

const jshint = require('gulp-jshint');

_gulp.task('jshint', function(){
  return gulp.src(['js/*.js'])
    .pipe(jshint())
    .pipe(jshint.reporter('default'));
});_

_Create a file in root directory called .jshintrc_
15.
{
  "esversion": 6
}

## Known Bugs

_Text._

## Support and contact details

_If you have any issues, questions, ideas or concerns, please contact us._

## Technologies Used

* HTML
* CSS
* SASS
* Javascript
* jQuery
* Gulp
* Node.js
* Jasmine
* Karma

### License

*This is open source so feel free to grab and use.*

Copyright (c) 2018 **_Cheryl Frankenfield_**
