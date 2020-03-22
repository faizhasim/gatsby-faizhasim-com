# Load Balancing Web Services

:published_at: 2014-12-25
:hp-tags: 


# Load Balancing Web Services

## Motivation

- I like Docker and to explore the possibilities of scaling the web architecture with Docker is fun.
  - Previously, I totally skip projects like Zookeeper because I’m just too lazy to read and configure a whole lot of configuration stuffs without losing context on what I’m trying to achieve.
  - Then, AWS become really popular. It does give a bunch of really cool stuff to play around. But, it’s not something that I can just quickly configure and play around with it.
- I wanted to scale my personal and professional project.
  - My personal blog is hosted using very simple Digital Ocean droplet and everything is pretty much hardcoded.
  - Upgrading the blog is hard, thus I wanted to Dockerize everything (including nginx frontend, Ghost app and possibly migrate from SQLite to PostgreSQL/MySQL).
  - As part of the migration process, I thought, “Would it be cool to have a automated load balancing architecture?”

## Consul - Service Discovery tailored for Docker

### Why Consul?

I just started playing with the service discovery stuff. But, again, compared to Zookeeper, I think this is way easier. Consul is designed for Docker. While `etcd` has been here relatively longer than Consul, I feel that it’s really tailored for `CoreOS`, not for your typical Linux distro. I could be wrong and someone should correct me here.

### The Idea

I want to shamelessly plug https://github.com/bellycard/docker-loadbalancer/blob/master/fig.yml[Docker Loadbalancer] project in GitHub. It really inspires me to adopt such architecture.

Assume that you want to scale an immutable `app`  service during runtime, the objective of such architecture is to make admin job easier to just run `fig scale app=«num_of_app_instances»` and all the load balancing from nginx part will done in real time, without downtime.

### How does it work?

1. Consul is run as server mode as one of the Docker container. 
  -  Consul is the guy who tell nginx on service location of our apps.

2. Registrator as the Consul registrator which directly hook to your local `/var/run/docker.sock`
  - On any changes on local Docker containers (app created or destroyed), Registrator will know and immediately relay the message to Consul.
  - Then, Consul will relay the changes to other services (in this case Nginx).

3. Nginx with live configuration refresh using `consul-template`
  - Nginx will serve as our load balancer with live configuration refresh.
  - `nginx.conf` is not configured as a static configuration, but rather as a template for `consul-template`.
  - `consul-template` is configured to listen to our consul service published at default 8500 port.

      exec consul-template \
           -consul=consul:8500 \
           -template "/etc/consul-templates/nginx.conf:/etc/nginx/conf.d/app.conf:sv hup nginx"
  - On any changes from Consul, it will refresh the nginx configuration, thus newly discovered app can be configured to be load balanced.

## Immutability is awesome

In the last few rounds of functional programming talks, we explored the motivation on how immutability in your code really encourage clean and scallable code.

Projects like Docker really contributes to immutable architecture. Because it’s so lightweight and sometimes composable, it evolve the way on how I think when I write any app, specifically web services. From now on, I should ensure that the app is written in such a way that it can be vertically scalled easily.

If Docker is treated as the first class citizen in our code, I reckon it make our production pipeline easier and more efficient. How can you execute functional testing? Spin up that Docker instance and run your test. Do you need MySQL for your integration testing? Fire up MySQL container that link to your app.

Since app and architecture are both immutable, I think we can reduce problems where “… but our staging system works fine”.

