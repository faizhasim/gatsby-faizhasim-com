= faizhasim.com is now hosted from Github using Hubpress

:hp-tags: github, faizhasim.com

I would like to announce that I have migrate my personal blog which was hosted on awesome https://ghost.org[Ghost] platform on https://www.digitalocean.com[Digital Ocean], to http://hubpress.io/[Hubpress] platform on Github.

You can find the Github repo at https://github.com/faizhasim/faizhasim.github.io[github.com/faizhasim/faizhasim.github.io].

Let me address a few motivations/reasons for this migration:

== I had trouble with Ghost configuration during the migration process

- Previously, when Docker wasn't a thing, I just spun up a pre-baked DigitalOcean instance to run Ghost blogging platform.
- When I started to use Docker extensively a year ago, I had an idea to migrate the blog as a Docker container and mount the data on the host. Thus, upgrading the Ghost platform itself will be a matter of updating the Docker container itself.
- It sounds great, except the official Ghost Docker image is cumbersome to configure and I was having some configuration issues during migration process.
- It was annoying enough and I decided to probably easier to spend time migrating the content from Ghost to Hubpress.


== Maintenance Cost

- Hubpress is just a Github repository, forked from https://github.com/HubPress/hubpress.io[hubpress.io] Github repository.
- Upgrading Hubpress is just a matter of pulling down the changes from the upstream.
- The store itself is Github and for each changes (for example making a blogpost), is just another commit to my repository.
- So, if things screw up for some reason, I can always rebase the repo to earlier commit. I did something nasty back then, I actually just rebased the blog. The whole blog have a proper versioning. Pretty neat!
- I no longer need to SSH into the server and maintain the blog.

== Inspired by Ghost

- They are really expired by Ghost blogging platform itself.
- The themes are effectively ports from popular Ghost themes.

== Additional stuffs that's included with Hubpress

- Disqus integration.
- Default links to common social medias / services.