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

_Bower is a node module and package manager for the front end. On init, creates a bower.json file._
16.
npm install bower -g
bower init (when starting new project)

_install jquery._
17.
bower install jquery --save
<script src="bower_components/jquery/dist/jquery.min.js"></script>

_install bootstrap_
18.
bower install bootstrap --save

<script src="bower_components/jquery/dist/jquery.min.js"></script>
<link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.min.css">
<script src="bower_components/bootstrap/dist/js/bootstrap.min.js"></script>
<script type="text/javascript" src="dist/js/app.js"></script>

_install Moment.js - good for working with dates and times in apps._
19.
bower install moment --save
<script src="bower_components/moment/min/moment.min.js"></script>

_Gulp to join front end dependencies with another package. Edit Head html. concates one minified file for relevant vendor files._
20.
npm install bower-files --save-dev

const lib = require('bower-files')();

gulp.task('bowerJS', function () {
  return gulp.src(lib.ext('js').files)
    .pipe(concat('vendor.min.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./dist/js'));
});

<script src="dist/js/vendor.min.js"></script>
<script type="text/javascript" src="dist/js/app.js"></script>

gulp.task('bowerCSS', function () {
  return gulp.src(lib.ext('css').files)
    .pipe(concat('vendor.css'))
    .pipe(gulp.dest('./dist/css'));
});

_(replace earlier require statement with this to include bootstrap. Where to find the files we want.)_
const lib = require('bower-files')({
  "overrides":{
    "bootstrap" : {
      "main": [
        "less/bootstrap.less",
        "dist/css/bootstrap.css",
        "dist/js/bootstrap.js"
      ]
    }
  }
});

_(combine two bower tasks into one so they run in parallel. )_
gulp.task('bower', ['bowerJS', 'bowerCSS']);

_(replace earlier task. run start bower each time we make a build no matter whether it's prod or dist.)_
gulp.task('build', ['clean'], function(){
  if (distProduction) {
    gulp.start('minifyScripts');
  } else {
    gulp.start('jsBrowserify');
  }
  gulp.start('bower');
});

_BrowserSync package to reload browser using local dev server. gulp.watch() -pass in 2 arguments. The first is an array of file names gulp watches. The second is an array of tasks to run whenever any of the aforementioned files change._
21.
npm install browser-sync --save-dev

const browserSync = require('browser-sync').create();


_gulp.task('serve', function() {
  browserSync.init({
    server: {
      baseDir: "./",
      index: "index.html"
    }
  });

  gulp.watch(['js/*.js'], ['jsDist']);
  gulp.watch(['bower.json'], ['bowerDist']);
});_

_Above is watching bower.json file and when changes are made, browser will be reloaded when serve task runs_
22.
gulp.task('jsDist', ['jsBrowserify', 'jshint'], function(){
  browserSync.reload();
});

23.
gulp.task('bowerDist, ['bower'], function(){
  browserSync.reload();
});

_Install Jasmine, JS testing framework. init it. (npm test)_
24.
npm install jasmine --save-dev
./node_modules/.bin/jasmine init

_(update package.json file)_
"scripts": {
  "test": "jasmine"
}

_To update any source file and your browserify bundle will be recompiled on the spot._
25.
npm install watchify --save-dev

_Install Karma - test runner (karma init)_
26.
npm install karma --save-dev
npm install karma-jasmine jasmine-core --save-dev
npm install karma-chrome-launcher --save-dev
(npm install karma-cli --save-dev)
npm install karma-browserify --save-dev
npm install karma-jquery --save-dev
npm install karma-jasmine-html-reporter --save-dev

_update karma.conf.js file_
27.
module.exports = function(config) {
  config.set({
    basePath: '',
    frameworks: ['jquery-3.2.1', 'jasmine', 'browserify'],
    files: [
      'js/*.js',
      'spec/*-spec.js'
    ],
    exclude: [
    ],
    preprocessors: {
      'js/*.js': [ 'browserify'],
      'spec/*.js': ['browserify']
    },
    plugins: [
      'karma-jquery',
      'karma-browserify',
      'karma-jasmine',
      'karma-chrome-launcher',
      'karma-jasmine-html-reporter'
    ],

    reporters: ['progress', 'kjhtml'],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ['Chrome'],
    singleRun: false,
    concurrency: Infinity
  })
}

_update package.json file_
28.
"scripts": {
    "test": "karma start karma.conf.js"
  },

_Concatenates CSS_
29.
_gulp.task('cssDist', function() {
  gulp.src(['css/*.css'])
  .pipe(concat('vendor.css'))
  .pipe(gulp.dest('./dist/css'))
});_

<link href="build/css/vendor.css" rel="stylesheet" type="text/css">

_Install Babel - transpiler. version to version compiling code or one langugae to another_
30.
npm install babelify babel-core babel-preset-es2015 --save-dev
const babelify = require('babelify');

_(update gulpfile)_
gulp.task('jsBrowserify', ['concatInterface'], function() {
  return browserify({ entries: ['./tmp/allConcat.js']})
    .transform(babelify.configure({
      presets: ["es2015"]
    }))
    .bundle()
    .pipe(source('app.js'))
    .pipe(gulp.dest('./build/js'))
});

_update karma.conf.js file. PLEASE NOTE: you may have to install babel-core and change below from es2015 to env_
31.
browserify: {
      debug: true,
      transform: [ [ 'babelify', {presets: ["es2015"]} ] ]
    },

32. 
install SASS     

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
