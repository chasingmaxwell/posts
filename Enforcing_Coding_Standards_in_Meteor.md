# Enforcing coding standards in Meteor

This post is the third in a series of posts about the new JavaScript framework, Meteor. If you are unfamiliar with Meteor, check out the first ([introduction post](http://fourword.fourkitchens.com/article/getting-started-meteor)) in this series, or go through the demos available on [Meteor's project website](http://meteor.com).

In this post, I'm going to discuss the importance of enforcing a strict and clear coding standard in your Meteor applications, and then provide you with some tools that will make this process easier.

## What are coding standards?
Coding standards are a collection of guidelines for a language that enforce a specific programming style. These guidelines generally apply to naming conventions, comments, indentation, white space, and even source tree structure. 

For instance, in your JavaScript coding standard, you might mandate that all function declarations should have a space after the function name, and before the parenthesis that hold your arguments. In addition to that, you might require your function arguments to be separated not only by a comma, but also by a space. And, you might want all of your functions to have documentation.

In this example, this would be incorrect:

```
function buildHugeTaco(tortillaType,meatTypes,hasQueso) {
  return new Taco();
};
```

And this would be correct:

```
/**
 * Builds a huge taco.
 *
 * @param String tortillaType
 *   Type of tortilla in which taco ingredients should be placed.
 *   Allowed values are 'flour', 'corn', and 'wheat'.
 * @param Array meatType
 *   Types of meat that should be placed in this taco. Should be an array
 *   that contains any of the following values: 'beef', 'chicken', 'pork'.
 * @param Boolean hasQueso
 *   Indicates whether or not this taco should be smothered with queso.
 *
 * @return Taco
 *   Object of type taco, ready for consumption by objects of type Mouth.
 */
function buildHugeTaco (tortillaType, meatTypes, hasQueso) {
  return new Taco();
};
```

In this example, following a few guidelines made the `buildHugeTaco` function a lot more readable, and provided relevant and important context.


## Why are coding standards important?
Following a coding standard is critical for succesfully developing a codebase that is mantainable, and scaleable. It ensures that code is formatted consistently, and documented properly, which makes the project a lot more pleasent to work with as it grows. In some cases following a standard can even help developers discover and squash bugs, improving the stability of the application. Most importantly, coding standards enable teams of developers to create consistently-formatted codebases regardless of the fact that many different people are adding and changing code.


## Coding standards in Meteor applications
I've read through a large number of the open source Meteor-based applications that are floating around. People have done some really cool work! But one thing that I've noticed is that most Meteor-based applications don't seem to have a defined or enforced coding standard. Often times the formatting and documentation patterns are inconsistent, especially if multiple people have worked on the project. I recently started working on a Meteor-based application and realized why this is the case... there aren't really any Meteor-specific tools available for defining  and enforcing these standards. So as a part of this project, I put together a tool, and then added it to the [boilerplate](https://github.com/patrickocoffeyo/meteor-boilerplate) I mantain. Here's how it works!

### Jscs and JSHint
[Jscs](https://www.npmjs.com/package/jscs) is a wonderful node module that allows one to define a style guide, and then enforce it by programatically scanning the codebase for code blocks that don't fit the guideline.

[JSHint](http://jshint.com/) is a tool that detects and reports errors and potential problems in the codebase, like syntax errors, implicit type conversions, and leaky variables.

These are the tools that many people use to keep their codebases consistent, clean, and error-free. And so, why not use them within Meteor projects? Here's how you do it.


### Using Gulp to run jscs and jshint
[Gulpjs](http://gulpjs.com/) is an automation tool with which one can define tasks that do useful things with files. In this instance, we're going to use gulp to run jscs, and jshint on certain files within our Meteor application. Here's an example gulpfile that will do just that:

```
/**
 * @file
 * Portable Gulp tool that checks a Meteor installation for js syntax errors.
 */
/* globals require */

var gulp = require('gulp'),
    jshint = require('gulp-jshint'),
    jscs = require('gulp-jscs');

/**
 * @task JavaScript lint.
 *   Runs jscs and JSHint on server, client, lib, and gulp files.
 */
gulp.task('lintjs', function () {
  return gulp.src([
    'gulpfile.js',
    'src/**/*.js',
    '!src/.meteor/**/*.js',
    '!src/packages/**/*.js'
  ])
  .pipe(jshint())
  .pipe(jshint.reporter('default'))
  .pipe(jscs());
});

/**
 * @task JavaScript/JSON watch.
 *   Watches changes on relevant js and json files and reports accordingly.
 */
gulp.task('watch', function () {
  gulp.watch([
    'gulpfile.js',
    'src/**/*.js',
    '!src/.meteor/**/*.js',
    '!src/packages/**/*.js'
  ], ['lintjs']);
});
```

The `lintjs` task runs jscs and JSHint on the JavaScript files within it's project, and excludes the Meteor core, and package JavaScript files so that you don't get error reports on other people's code. Jscs and JSHint are configured by [.jscsrc](https://github.com/patrickocoffeyo/meteor-boilerplate/blob/master/.jscsrc) and [.jshintrc](https://github.com/patrickocoffeyo/meteor-boilerplate/blob/master/.jshintrc), which should live in the same directory as the gulp file.

The `watch` task watches the codebase for changes, and when a change is made to a file, it will run the jscs and JSHint tasks so that as you are developing, you will get a real-time report on any errors you might have made.

For a more full example, see the `.jscsrc`, `.jshintrc`, `package.json`, and `gulpfile.js` in the [Meteor boilerplate](https://github.com/patrickocoffeyo/meteor-boilerplate).





