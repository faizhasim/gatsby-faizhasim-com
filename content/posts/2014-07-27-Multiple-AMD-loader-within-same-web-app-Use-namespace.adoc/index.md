# Multiple AMD loader within same web app - Use namespace

:published_at: 2014-07-27
:hp-tags: 


Recently, we discovered an frontend issue with one of our plugins which was written in AMD architecture. We were using default `require` and `define` function in global JavaScript context. Turns out that one of the Atlassian addons uses different AMD loader on global too. Upon further inspection, later versions of Confluence also ship with their own AMD loader loader by default (and also register the functions on the global context).

Initially, we tackle this problem by having a custom loader for the module leader itself. In the commit message, we call it `loaderception`. Turns out `loaderception` is quite error prone. `loaderception` means taking care of racing condition. I thought, meta-loader is definitely not the way to solve this.

I was doing more reading on requirejs website, and found out advance option to introduce namespace for `r.js` optimizer at http://requirejs.org/docs/faq-optimization.html#namespace[RequireJS - Optimization - Namespace]. 

Great!

## How did we implement this?

From http://requirejs.org/docs/faq-advanced.html[RequireJS advance page], they recommended 2 different ways of implementing the namespacing. I will explain my thoughts on both approach and why we choose a slightly different approach to implement requirejs namespace.

### First suggestion from RJS website

- This will compile all required modules along with the modified version requirejs loader.
- Modified AMD module will be included on each of the `r.js` optimization step.
- This is absolutely fine if you want to bundle up all modules into a single entry point.
- If you have multiple entry points, multiple AMD modules will be unnecessarily bundled up.
- So, I do not quite like this idea.

### Second suggestion from RJS website

- Second suggestion does not involve `r.js` optimizer at all.
- Two things should be done:
  - Wrap immediately-invoked function expression or `IIFE` over the AMD loader to give different name to the `require` function.
  - Then, modify required modules wrap it with `IIFE` to provide "altervative" `require` function.
- I am curious whether this actually works. I didn't see `define` function being handled. But, I supposed this article is to give a working idea, not a working implementation.
- My main concern with this approach is: **Build process**
  - `r.js` itself is relatively slow (few seconds at the moment on my machine)
    - If we need to modify each and every single modules prior to that, that will take times.
    - Once modified and wraped with `IIFE`, is `r.js` optimizer still going to work? I do not think so.
    
### Our approach

- Basically a modified version of first suggestion.
- We still tell `r.js` optimizer to provide a namespace.
- But, we don't tell it to be clever and modify the module loader itself.
- Using something like https://www.npmjs.org/package/grunt-surround[grunt-surround], we wrap the AMD loader with something like:

    var AMD_LOADER_NAMESPACE = typeof AMD_LOADER_NAMESPACE === 'undefined'? {} : AMD_LOADER_NAMESPACE;
        (function(undefined) {
          // original 
            // almond.js
            // code
          AMD_LOADER_NAMESPACE.requirejs = AMD_LOADER_NAMESPACE.require = require;
          AMD_LOADER_NAMESPACE.define = define;
        })(void 0);

- We load this modified AMD loader once per-addon.
- Our build time is minimally affected.
- It all good.





