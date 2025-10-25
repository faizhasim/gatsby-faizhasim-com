---
title: "Revamping the background services"
date: "2012-08-12T17:28:42"
template: "post"
draft: false
slug: "/posts/revamping-the-background-services"
category: "Programming"
tags:
  - concurrency
  - java
description: "I have been assigned a job to improve the way the background services work. Most of them are background schedulers and all of them are implemented using Java."
---


I have been assigned a job to improve the way the background services work. Most of them are background schedulers and all of them are implemented using Java.

I have not found a proper solution yet, but I'm listing down all the challenges that I encounter when designing the solution for it:

1. **Multi-tenanted services**
In which the configuration can change dynamically during runtime. For example, one of the email notification services may consists of different repository settings.
At the moment, an ExecutorService is assigned to a particular email notification repository. Thus, if the settings for a  particular repository is updated, the corresponding ExecutorService is restarted. (At that time we think it makes sense not to manually handle all the Futures and use a single ExecutorService. Moreover, if you don't manually purge the ThreadPoolExecutor, you will have memory leaks.)

2. Different **behaviour** on the services. Some of them simply do some processing on a fix interval, in which past jobs are processed. There jobs that need more accuracy, so we actually manually planned for the execution in the future.

3. At the moment, it's possible for multiple services on multiple repositories to be scheduled at the same time. This could easily starve the CPU when that happens.

### Just throwing out some ideas for the solution.
* Should 1 service is managed by 1 ExecutorService instead of 1 ExecutorService per repository?
        
    If yes, Futures must be manually managed.
        
* Assume 1 ExecutorService per repository, we can reduce the impact of issue 3, by "pausing" certain ExecutorServices to give a breathing room for the CPU to process others. I think this is not gonna work. I can't figure out when to "pause"  on which ExecutorService, and likewise for "resuming" the ExecutorService.
* Should we rely on a master ExecutorService, similar to Grand Central Dispatch in OSX? We can archive this using Hazelcast in JavaWorld. Problem is, there's no ScheduledExecutorService for Hazelcast. Going this path probably means, create a local ScheduledExecutorService just to submit the jobs to Hazelcast.

