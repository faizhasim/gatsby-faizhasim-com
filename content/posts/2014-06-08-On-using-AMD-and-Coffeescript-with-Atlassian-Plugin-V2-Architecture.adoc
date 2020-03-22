# On using AMD and Coffeescript with Atlassian Plugin V2 Architecture

:published_at: 2014-06-08
:hp-tags: 

Over the past few months, we have adopted AMD module pattern in some of our frontend codebase for some of our plugins. In addition to that, we have decided to write in Coffeescript, which will be transpiled into uglified Javascript. I wanted to share our story why we do this, how we implement this and how does this help to improve our code maintenance.

## Motivation

1. Global namespace/scope pollution
  - For example, some variables were unnecessary declared as globals. So, when we wanted to implement new features which affect our frontend codebase, we got to be super careful not to override these globals. Code scalability is hard.

2. Poor organization of code
  - One of the project make an attempt to organize the code into a prototypal class design pattern. But, in my professional opinion, it only make it worse due to some antipatterns.
      - Some parts of the code share some states in global scope, mostly by using closures. For example, we have something like: 

          var aGlobalState = null;
          DummyClass.prototype.doSomething = function() {
              if (aGlobalState == null) {
                  aGlobalState = initTheGlobalState();
              }
          }



  - Code coupling between prototypal classes is still high. Refer above.
        
3. Dangerous code style
  - Some conditioning were made based on `falsy` value. For example, when you wrote `if (content == "") { doSomething();}`, `doSomething()` will be evaluated when `content` is falsy too (`0` or `false`).
    - We omitted semicolons. I hate this pattern, because it's easy to write the following statement and cause a subtle bug in your application.
      
          var calculateNumber = function() {
              return
                1 + 1
            }
            
  - Code should be more readable. Things like `if ( $element == null || AJS.$($element).offset() == null )` should at least be rewritten to `if ($element || AJS.$($element).offset())`

4. Code testability

  - We wanted our code base to be unit testable. AMD module pattern will help us to do this better.

## Architecture

### We use http://gruntjs.com/[Grunt] build system

- Tools such as Grunt and https://github.com/gulpjs/gulp[Gulp] will be such a really useful build tool.
- Typically, we use Grunt for JavaScript uglification, compiling Coffeescript to Javascript, file watchers for developer productivity, AMD module compilation using RequireJS, to run our http://visionmedia.github.io/mocha/[Mocha]-based test suites and also compile `less` to `css` resources.
- In one of the projects, we configured this using Coffeescript. Personally, I found it way easier to read. Almost like using YAML for configuration.
- I wanted to give a shoutout to these Grunt plugins:
  - grunt-contrib-coffee
    - grunt-contrib-watch
    - grunt-contrib-requirejs
    - grunt-contrib-less
    - load-grunt-tasks (not really a Grunt plugin, but it simplify the Grunt configuration)
    
### We use http://bower.io/[Bower] package manager to manage our frontend assets

- We typically put those inside `/lib` directory instead of default `/bower_components`.
- I also want to give a shoutout to these JS libraries:
  - bluebird - Performant Promise A+ library.
      - Ps: Don't use jQuery promise, Domenic Denicola would hate you for that!
    - https://bitbucket.org/atlassian/aui-dist.git#5.4.3[AUI] - For our test.

### File structure

image::https://cloud.githubusercontent.com/assets/898384/10412767/c96bc26c-6fc2-11e5-8fd1-390f0101ef96.png[]

- `/.tmp/` is where we compile our resources before final distribution. So, when we run the test suite, we're referring to the modules within this folder.
- `/dist` is where the final resources, ready to be shipped to production. Our `atlassian-plugin.xml` actually points to the resources within this folder. We also configured our Java project to skip YUI compression step.
- `/lib` is where we store third party frontend libraries
- `/node_modules` contains all `node` modules.
- `/src` is where we store all our `Coffeescript` sources. AMD modules are organized within `/src/modules`
- `/style` is where we store the all `less` stylesheets and related resources such as images.
- `/test` is where we wrote all the unit tests. We still wrote the integration test in Java mainly due to atlassian pageobject framework.

### Build Process

This is effectively what our Grunt build system.

image:https://cloud.githubusercontent.com/assets/898384/10412766/c9697ee4-6fc2-11e5-8d51-01eb1089f4c1.png[]

As you can see, our build process are quite linear. We do not have the use case for more complex build process. Regardless, in the future, we are looking forward to play with Gulp, mainly for 2 reasons. Build process is streaming and everything is in-memory, thus faster build process. Unlike Grunt, Gulp is just yet-another-node process, so it's programming over configuration.


## What we gain

- More managable codebase
- Easier to unit test our frondend codebase
  - Unit test will be done on modules.
    - Dependencies to the tested module can be mocked more easily.
    - In saying that, the Dependency Injection pattern by the AngularJS folks looks like a better architecture for testable code.
- Coffeescript helps us:
  - On code readability (although some would argue on this). But, I like some of these patterns, especially something like:
    
        renderDialogBox = () ->
              return ($ '#id').html @templates.emptyDialogBox if status is empty
                ($ '#id').html @templates.dialogBox
                  title: "#{@title}"
                    description: "Hello #{@user.name}!"
            
    - Prevent some Javascript quirks. For examples:
      - Explicit `var` variable hoisting.
        - `is` keyword actually transpiled to `===`. Dauglas Crockford will like this. :)
        - The problem described above can be avoided, without liting.

 










