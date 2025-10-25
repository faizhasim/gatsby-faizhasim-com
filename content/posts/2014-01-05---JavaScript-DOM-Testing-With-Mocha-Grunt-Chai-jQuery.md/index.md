---
title: "JavaScript DOM Testing With Mocha + Grunt + Chai jQuery"
date: "2014-01-05T17:28:42"
template: "post"
draft: false
slug: "/posts/javascript-dom-testing-with-mocha-grunt-chai-jquery"
category: "Programming"
tags:
  - functional programming
  - javascript
  - scala
description: "I'd like to share my work on writing JavaScript DOM unit testing on a existing codebase in one of our Confluence plugins."
---

I'd like to share my work on writing JavaScript DOM unit testing on a existing codebase in one of our Confluence plugins. 

The goals of this unit testing are as following:

- Simulate JavaScript and DOM as close as possible to actual browser environment
- Being able to easily integrate this to CI services

## Libraries/Frameworks/Tools that We Use ##

- [Mocha](http://visionmedia.github.io/mocha/) - JavaScript Test Framework
- [SinonJS](http://sinonjs.org) - JavaScript Spies/Stubs/Mocks Framework
- [NPM](https://npmjs.org) - Node Package Manager
- [Bower](http://bower.io) - Package Manager for the Web. It doesn't dictate you to use CommonJS module system. Just include some JS/CSS/whatever.
- [PhantomJS](http://phantomjs.org) - Headless Webkit Browser
- [Grunt](http://gruntjs.com) - Task-based JavaScript build tool
- [Chai](http://chaijs.com) - JavaScript BDD-style assertion library
- [chai-jquery](https://github.com/chaijs/chai-jquery) - Chai extension that provides a set of jQuery assertions (for DOM testing)

# Getting things done from nothing... #

## Environment setup ##

I'd like to document these steps for our own reference and install and configure [NPM](https://npmjs.org) on your machine. For OSX users that use [HomeBrew](http://brew.sh), just do `sudo brew install npm`.

Also, make sure to install `bower` and `grunt-cli` globally on your node:

    npm install -g grunt-cli bower

## Project Setup ##

### NPM Project ###

For our project, this was a "traditional" javascript codebase, probably written before things like NPM or Bower become more mainstream today. So, a new nodejs setup can be configured by calling `npm init`, which will guide you to create `package.json`.

My final `package.json` looks something like this:

```json
{
  "name": "confluence-scaffolding",
  "version": "5.0.9-SNAPSHOT",
  "devDependencies": {
    "grunt": "~0.4.2",
    "grunt-mocha": "~0.4.7",
    "grunt-jsmin-sourcemap": "~1.10.0",
    "grunt-contrib-watch": "~0.5.3",
    "grunt-contrib-handlebars": "~0.6.0",
    "grunt-contrib-clean": "~0.5.0"
  },
  "description": "Currently used only for BDD test using Mocha",
  "scripts": {
    "test": "grunt test",
    "install": "bower install"
  }
}
```

All these `grunt-**` dependencies are Grunt task runners. Notice the `"script"` part? That means, if I type `npm install`, it will trigger `bower install` too. Make sure to run `npm install` to install those dependencies locally (you might want to disable the `bower install` part temporarily for the first time).

### Bower Project ###

Bower can be initiated in similar manner. Just type `bower init` to get bower to help you to initialised `bower.json`. See below for mine:

```json
{
  "name": "confluence-scaffolding-test",
  "version": "5.0.9-SNAPSHOT",
  "dependencies": {},
  "devDependencies": {
    "jquery": "~1.7.2",
    "require-handlebars-plugin": "~0.7.0",
    "requirejs": "~2.1.9",
    "mocha": "latest",
    "chai": "~1.8.1",
    "chai-jquery": "~1.1.2",
    "aui": "https://bitbucket.org/atlassian/aui-dist/get/master.zip",
    "sinonjs": "~1.7.3"
  }
}
```

I want to control where Bower install these dependencies, instead of putting them into `bower_components` directory. So, I've also created `.bowerrc` and specify the location as following:

```json
{
  "directory": "lib"
}
```

You should probably run `bower install` after writing those. It will pull out all the assets to the corresponding directories.

### Configuring Grunt ###

Grunt can be configured within a file called `Gruntfile.js`. Here's an example of mine:

```json
module.exports = function(grunt) {
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        'jsmin-sourcemap': {
            all: {
                src: [
                    'forms/**/*.js'
                ],
                dest: 'tests/scaffold-combined.min.js',
                destMap: 'tests/scaffold-combined.min.js.map',
                srcRoot: '../'
            }
        },
        mocha: {
            test: {
                src: [
                    'tests/index.html'
                ],
                options: {
                    log: false,
                    run: false
                }
            }
        },
        handlebars: {
            options: {
                amd: true,
                processName: function(filePath) {
                    return filePath.replace(/^templates\//, '').replace(/\.hbs$/, '');
                }
            },
            all: {
                files: {
                    "lib/require-handlebars-plugin/hbs/templates.js": ["tests/**/*.hbs"]
                }
            }
        },
        watch: {
            compileJs: {
                files: ['forms/**/*.js'],
                tasks: ['jsmin-sourcemap']
            },
            compileHandlebars: {
                files: ["tests/**/*.hbs"],
                tasks: ['handlebars']
            }
        },
        clean: {
            test: {
                src: [
                    "tests/repeatingComponentTest/scaffold-combined.min.js",
                    "tests/repeatingComponentTest/scaffold-combined.min.js.map",
                    "lib/**",
                    "node_modules/**"
                ]
            }
        }
    });
    grunt.loadNpmTasks('grunt-mocha');
    grunt.loadNpmTasks('grunt-jsmin-sourcemap');
    grunt.loadNpmTasks('grunt-contrib-watch');
    grunt.loadNpmTasks('grunt-contrib-handlebars');
    grunt.loadNpmTasks('grunt-contrib-clean');

    grunt.registerTask('compile', ['jsmin-sourcemap', 'handlebars']);
    grunt.registerTask('default', ['test']);
    grunt.registerTask('test', ['compile', 'mocha']);
};
```

On my initial version, I wrote that using [Coffeescript](http://coffeescript.org). Honestly, I like it. It was more readable, but I think it's better to keep it as JS for now. http://gruntjs.com/getting-started has excellent documentation on how to use Grunt, I would strongly suggest for you to read that. But, I just want to highlight some of the reasons or explanations on the task configuration.

- `jsmin-sourcemap` – Used to compile all of existing production source code into a single JS file, to make test easier and replicate what Atlassian do with the YUI compressor. One nice thing about this task is, it produces source map! See this [blog](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/) on what is source map and why it's so damn cool.
- `mocha` – Our mocha configuration. Note: This `grunt-mocha` **is not** for running mocha on NPM environment. It basically run mocha test within PhantomJS. We purposedly set the `run: false` due to the fact that we will trigger this on our [AMD](http://requirejs.org/docs/whyamd.html) loading in the code.
- `handlebars` – [Handlebars](http://handlebarsjs.com) is a variant of MustacheJS, but with helpers and additional features. I use this to separate the mocked DOM, instead of bundling them into the JS code. I don't want to keep pulling my hair later for unmanage codebase. Honestly, I can just interpret the mocked DOM as plaintext, but I might as well configure it as template because I thought that we might use that for future test anyway.
- `watch` - A file watcher. It watched over on group of files. On any changes, it will trigger the selected Grunt task. I used this when writing the test. Just run `grunt watch`.

# Writing the tests #

## Directory structure ##

See the directory structure here:

- `index.html` - The main page entry for the Mocha test. I think I can probably make a single html entry for the test, and multiple JavaScript sources loaded via AMD to run the test. It's more managable imo. As long as the JavaScript source does not pollute global context and override each other, it's fine. 
- `index.js` - Main entry for `requirejs` AMD loading. This is where I define the configuration the require js too.
- `repeatingComponentTest` - This is an example for a test suite. I bundled all the assets (mainly Handlebar templates and Mocha test in JavaScript) within their own directory. So, if I want to test featureB in the future, I'll just create a directory called featureB and let AMD call the test from `index.js`.

## Configuring AMD using require js ##

### index.html ###

I tried to load everything using AMD loading, but I was unable to load Atlassian's AUI, sinonjs and mocha. While AUI and sinonjs are not written as modules, I am not exactly understand why I can't load mocha using AMD. Some people in the community reports the same thing. So, for those libraries, I load them manually via `<script>` tag within `index.html`.

In the `index.html` file, I've also introduced a DOM element for all my test: `<div id="test"></div>`.

### index.js ###

See my requireJs configuration below. I think, you probably need to refer [RequireJS Documentation](http://requirejs.org) itself if you're not familiar with AMD or RequireJS.

```javascript
require.config({
    paths: {
        'jquery'        : '../lib/jquery/jquery',
        'underscore'    : '../lib/underscore/underscore',
        'chai'          : '../lib/chai/chai',
        'chai-jquery'   : '../lib/chai-jquery/chai-jquery',
        'handlebars'    : '../lib/require-handlebars-plugin/hbs/handlebars',
        'tpl'           : '../lib/require-handlebars-plugin/hbs/templates',
        'main-src'    : '../'
    },
    shim: {
        'underscore': {
            exports: '_'
        },
        'jquery': {
            exports: '$'
        },
        'chai-jquery': ['jquery', 'chai']
    }
});

require(['require', 'chai', 'chai-jquery', 'jquery', 'tpl'], function(require, chai, chaiJquery, $, tpl){
    // Globals mocha
    mocha.setup({
        ui: 'bdd'
    });

    require(['repeatingComponentTest/list-field.js'], function(require) {
        mocha.run();
    });

});
```

Two interesting things I want to highlight here.

- `tpl` – is the template module for the Handlebars template. It is compiled by Grunt.
- `require(['repeatingComponentTest/list-field.js'], function(require) {` – This block of code is where I can control on which test module I want to load for mocha test. The following `mocha.run()` is the reason why I disabled automatic `mocha.run()` from Grunt task.

### Sample Test ###

```javascript
define(['require'], function(require){
    "use strict";
    var chai = require('chai'),
        chaiJquery = require('chai-jquery'),
        tpl = require('tpl'),
        expect = chai.expect;

    chai.use(chaiJquery);

    beforeEach(function(){
        $('#test').empty();
    });

    after(function(){
        $('#test').empty();
    });

    describe('Checkboxes within {repeating-data}', function(){
        describe('With fresh Scaffolding metadata', function() {

            it('Should have single <ul> element within the first checkbox collection', function() {
                $('#test').append(tpl['tests/repeatingComponentTest/checkboxesWithinRepeatingData-withoutExistingData']);

                var repeatingField = AJS.$('.scaffold-data-add').closest(".scaffold-data-looping").scaffold();
                var spyOnAddLinkClick = sinon.spy(repeatingField, "onAddLinkClick");
                var spyInitScaffoldOnElement = sinon.spy(repeatingField, "initScaffoldOnElement");
                AJS.$('.scaffold-data-add').click();

                expect(spyOnAddLinkClick.calledOnce).to.be.true;
                expect(spyInitScaffoldOnElement.calledOnce).to.be.true;
                expect(
                    $('.scaffold-data-content span[sd-parent="REP.0"] ul').size()
                ).to.equal(1);

            });
        });

    });

});
```

You can read more on the excellent [Mocha Documentation](http://visionmedia.github.io/mocha/) itself. A few things that I want to highlight from this:

- Always use `"use strict";` whenever possible in your JavaScript code. Enabling strict mode should be a default thing to do, unless you're writing some hacky stuff and know what you're doing. From my experience, global variable pollution is one of the biggest problem I had so far on maintaining legacy code. Easily avoidable with strict mode.
- I used CommonJS style within RequireJS. For eg: `var chai = require('chai')`

### Writing Test • Some Tips ###

- Test it on Google Chrome browser, before running `grunt test` to test it on PhantomJS. Make debugging a hell lot easier.
- If you're making actual AJAX call from `file:` location, Chrome won't like it. Start chrome with `-allow-file-access-from-files` parameter if you need to override that.
- Use inline `debugger` and `console.log(...)` for debugging.
- Need to make trivial changes on the JS file without keep switching application? Just write it in Google Chrome debugger itself. They have live code / html editing.

----------

> ps: I'm on leave writing this blog post from my hotel. The actual code base is still on pull request for peer review. I will probably update this blog post again.



