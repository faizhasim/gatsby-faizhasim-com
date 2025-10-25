# Relaxing with CouchDB, More Hapi with Joi

:published_at: 2014-07-16
:hp-tags: 


Since last two sprints, we have further optimised our Sprint rituals (specifically, review, retro and plannings schedule) to ensure that developers will have dedicated 20% of the time for play/innovation/learning. So, yup, I have dedicated 2 days for my play days. 

Initially, my plan was to discover and make a simple mini-project for Atlassian Connect using Atlassian own https://bitbucket.org/atlassian/atlassian-connect-express[Atlassian Connect on Express framework] project. But, over the time, my plan evolved to also learn more on discovering http://hapijs.com[Hapi]  web framework and http://couchdb.apache.org[CouchDB] document-based NoSQL database.

## Finding my work and track the plan and progress

### Private GIT Repo

- Hosted at http://nebu.servicerocket.local/git/users/faiz.hasim/repos/play-connect-nodejs-express/browse[Private GIT repo]
- I'm not sure whether you can access our nebu.servicerocket.local, but if you're interested to clone the project, drop me a comment below.

### Personal Board

- Hosted publically at my https://trello.com/b/RgQSkdPz/play-connect-nodejs-express[Trello board]


## Being Hapi with Joi

The intention of this blog post is not to give tutorials. There are some good articles and posts out there. I want to shamelessly plug some of them, which motivates and help me to learn more on Hapi framework:

- NodeUP Podcast Episode 56 - http://nodeup.com/fiftysix[a team walmart show]. Transcription at https://github.com/nodeup/transcriptions/blob/master/56-a-team-walmart-show-nodebf.md[their Github].
- JavaScript Jabber Podcast Episode 99 - http://javascriptjabber.com/099-jsj-npm-inc-with-isaac-schlueter-laurie-voss-and-rod-boothby/[JSJ npm, Inc. with Isaac Schlueter, Laurie Voss, and Rod Boothby]
- http://hapijs.com/[Hapi.js] own website, especially at the very helpful API documentation
- A quickstart guide at http://blog.modulus.io/nodejs-and-hapi-create-rest-api[Node.js and Hapi - Creating a REST API]

### Cut to the chase, here are some of my personal opinions on Hapi:

- If you just learning to do web development on node.js, Connect or Express would probably be a good start. Express framework is easier and more straightforword with the middlewares and nice routing controller. Hapi routing and their API in general, is also beautifully designed. It's just that it's preloaded with lots of other configurational stuffs, so it might looks a bit daunting to know all at once.
- Express is certainly less opionated on how you should organize your project, what kind of middlewares that you want to use and also how you would you scale you architecture (which is not a bad thing). Some of the great, successful and well-written web application is written on Express. Checkout https://github.com/TryGhost/Ghost[Ghost Blogging Platform] source code, for example.
- But, it would have been nicer to also have healty competitions, especially the ones that have been battle-tested on a highly scalable architecture. Hapi is created by those folks at Walmart labs to power up their mobile stack which interacts with another Java-based service. They are so proud on how their application survives Black Friday traffic. Checkout a video on their presentation at http://nodejs.org/video/.
- Some of the features that I really like:
  - http://hapijs.com/api#plugin-interface[Plugin interface]
      - One philosophy of unix and node.js is to do one thing and do it right.
        - Plugin interface fully supports this and I really like it.
        - Architectually speaking, developers should  be shipping fully-tested plugins which will support the main application architecture.
        - For example, we could be writing different set of plugins to generate different sets of Confluence macro views, but not worrying too much on premature optimizations.
        - Then, we could batch all the requests using third party plugins like (https://github.com/spumko/bassmaster[Bassmaster].
        - Possibly for staging server, just configure your server to include https://github.com/spumko/lout[Lout] plugin to generate your REST endpoint documentations.
    - http://hapijs.com/api#hapipack[Pack] feature.
      - A `pack` is basically a collection of servers running on different ports, to perform as a single operating unit.
        - So, you can start/stop a pack of server in sync, which maintaining the isolation and responsibility on each server.
        - For example, you might have a dedicated server for customer facing and another server for administrative purposes.
  - Authentication is built-in.
      - Atlassian Connect uses http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html[JSON Web Token] for authenticating web request from their product and from third party addons.
        - You can solve this by implementing (or re-using) JWT authentication middleware with framework like Express.
        - But, for me, having a nice clean interface that support this on the framework level would actually make more sense.
        - Because, you will be configuring the authentication on your framework, rather than configuring the middleware to produce the same result.
  - Power by https://github.com/spumko/joi[Joi] validation
      - For example, how I validate payload on atlassian-connect installation handshake, I just create a reusable module to validate with Hapi and other part of the code.
        
                module.exports.installationInfo = ->
                  key: Joi.string().required().example 'my-plugin-name'
                  clientKey: Joi.string().required().example 'Confluence:1234564'
                  publicKey: Joi.string().required().example 'MIG...AQAB'
                  sharedSecret: Joi.string().required().example '9b85405d-8ca8-4909-bfff-a3078d7b13a6'
                  serverVersion: Joi.string().required().example '5617'
                  pluginsVersion: Joi.string().example '1.0.0'
                  baseUrl: Joi.string().required().example 'http://localhost:1990/confluence'
                  productType: Joi.string().required().valid('confluence').example 'confluence'
                  description: Joi.string().example 'host.consumer.default.description'
                  eventType: Joi.string().required().valid('installed').example 'installed'
        
### Why all this? Why not just re-use that Atlassian's own project?

- I think understanding Atlassian Connect is very important. 
  - "Playing" with pre-scaffolded project is probably okay to kick start your learning. 
    - But I think I will appreciate and learn more by trying to really understand their architecture and build them from scratch.
    - For example, initially I misunderstood http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html[JWT] to be quite similar with OAuth 2.0. Turns out JWT is just some intermediatary verification token build using JSON.
  
- While Atlassian produces high quality code and products, I do not think that their `atlassian-connect-express` node.js project is "preferable" for me.
  - I do not agree with the direction of their node project. They tends to bundle up all the modules as part of their node "sdk". See how they pollutes their "`addon`" by exposing `underscore` and `RSVP` promise library. Note that this is not a frontend framework, so it is really unnecessary when you are developing in Node land.

    image::https://pbs.twimg.com/media/BrsU3-LCUAARmQy.png[]

  - `atlassian-connect-express` dictates certain configuration **must** be done their way. Otherwise, you will not make it work. For examples:
      - `atlassian-plugin.json` must exist on the root level of your project whereas developers might want to dynamically generate  this.
        - The build system is part of the production codebase. If the application is running in `dev` mode, the `atlassian-connect-express` will do additional stuffs like watching for default server port to automatically install your addon. In my opinion, similar thing can be elegantly implemented using proper build tools like `Grunt` or `Gulp`.
        - After all, even if you use `atlassian-connect-express`, I think you still want to implement some sort of build system. You will probably need to compile and optimize your frontend assets. You might want to easily configure a file watcher to help your development workflow etc.
        - I am not saying all these to bash their project. I think it is a very good project to demonstrate one of the possible architectures on Node. But, if we are really serious about on developing highly scalable plugin, we should really understand the consequences and what to be expected when using their "default" project.
        - I also had a feeling that they will improvise their project.

## CouchDB

### Summarizing CouchDB

- Document-based database that stores everything in JSON.
- Javascript is sort of first class citizen in `CouchDB` when it comes to transforming your data into `CouchDB` view.
- Written in `Erlang`.

### Interesting (and some shocking to me) reads for me

- CouchDB provides http://en.wikipedia.org/wiki/Atomicity,_consistency,_isolation,_durability[ACID] semantics by implementing http://en.wikipedia.org/wiki/Multi-Version_Concurrency_Control[Multi Version Currency Control].
  - Huh? I was under impression that CouchDB is non-ACID compliant till I read their technical specification. That's awesome!
    - http://en.wikipedia.org/wiki/Multi-Version_Concurrency_Control[MVCC] is like Git.
    - CouchDB is eventually consistent.
    
    image::http://guide.couchdb.org/editions/1/en/consistency/01.png[]
    
    - I think conceptually speaking, this is similar to http://en.wikipedia.org/wiki/Fifth_normal_form[Fifth Normal Form] implementation on RDBMS (Need to credit my ex-boss for sharing his idea/implementation on 5th level form with me). Well, in short, the idea is, if you can prevent UPDATE operation, you can reduce the amount of database locks to ensure the atomicity.
    - Correct me if I'm wrong. But, in term of ACID, CouchDB is better than MongoDB. From their documentation and answers in StackOverflow, MongoDB will **only** ensure consistency on the document level, in which people advise solution architects to implements things like two-phase commit, enforce document level transaction design with background process clean ups or use RDBMS in conjunction with MongoDB. For me, that's just crazy. Again, I am not an expert in this field, so comments are welcomed!
- Thou shall `Denormalise` as much as possible with CouchDB (or any other document-based database)
  - Let me give you an example on this. If you're saving Employee info with their respective Departments, you would save the whole information in a single document (or perhaps, redundantly on different document if needed).
    - But, wait, how do I perform a database join? No, you don't need to do that. See my next point.
- `CouchDB` views using `map-reduce` functions is powerful, natively implemented in Javascript.
  - When I wrote a simple `map-reduce` sample on CouchDB plays, I felt like a big data expert writing some crazy awesome stuffs on Hadoop. Nope, I never did Hadoop... 😛
  - Anyway, the idea is you use `map` function to transform your raw documents into a view. For example, in order to sort Employee name based on joining date, you would map based on date of joining key with name as the emitted value.
    - The idea of `reduce` function is to filter the data according to a certain group. Typically, used to count the grouped data. 
    - I do not feel absolutely confident with this map-reduce thingy, but I think it need to be practiced, not just reading.
- RESTful HTTP API interaction
  - This would appeal lots of developer and I'm one of them.
    - To "ping" your server, just make a GET request to your CouchDB server. For eg: `curl http://127.0.0.1:5984/` would return `{"couchdb":"Welcome","uuid":"d1e85425fb1b68e45056866dc3ca5432","version":"1.5.0","vendor":{"version":"1.5.0","name":"The Apache Software Foundation"}}` on my machine.
    - Some other examples:
      - `curl -X PUT http://127.0.0.1:5984/dbName` to create new database
        - `curl -X DELETE http://127.0.0.1:5984/dbName/document-123` to delete `document-123`.
  - This means, in node.js land, HTTP libraries like `request` from Mikeal Rogers is good enough. Or, slightly more comprehensive `nano` library is not bad at all. I do wish somebody would come out with promisified version of `nano` though.

### Things that I want to discover more related to CouchDB

- http://pouchdb.com[pouchdb]: Javascript on-browser implementation of CouchDB API.
  - That means you can save your data offline (I think saved to local storage/IndexedDB/or some sort of browser storage).
    - Then, when user re-connected to the Internet, data will be re-sync to backend CouchDB instance. Crazy stuff!
    - However, I do not think that we can use this technology for Atlassian Connect. Atlassian keep the architecture sandboxed via iframe cross-origin restriction. 😓
- CouchDB-level authentication and authorization design.
- More hand-ons with `map-reduce`.

## What do I have so far

Honestly, not much to present to anyone. But, what I archived in my recent project:

- Coffeescript-centric project, even for build system.
- Project scaffolded with Gulp build system.
- Test-driven development on most part of the project.
  - Not all of are test-driven. I'd like to think test-driven deevlopment is like building your specs and write your code according to that specs.
    - But, at the moment, I am exploring new ideas and new technologies. So, some part of the code was not test-driven.
    - I forsee that, once I get a better view on what I want, I would re-write certain modules from scratch with TDD.
- Save tenant info on plugin installation.





ps: I could use some proof-reading help. If you're on intranet, edit my blog post as needed. Thanks!


