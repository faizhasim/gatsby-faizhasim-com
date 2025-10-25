= Learndot @ Docker User Group

:hp-tags: docker, learndot


Last night, I had the opportunity to share our experience in using Docker for the last one year with https://learndot.com[learndot.com] platform.


I began by telling a story on how it all began.


++++
<iframe src="//docs.google.com/viewerng/viewer?url=https://github.com/faizhasim/faizhasim.github.io/files/18122/learndot-and-docker.pdf&embedded=true" style="width: 600px;height: 460px;border: #333333 solid 1px;"></iframe>
++++

[NOTE]
Docker was depicted as the "Beast"; strong but gentleman. Learndot was depicted as the "Beauty"; beautiful, young and sweet LMS.


Then, I started to share our principles in developing software and the importance of effecient deployment pipeline. I have shown our DevOps architecture to the audience on how we decompose the services and recompose them all back.

++++
<iframe src="//docs.google.com/viewerng/viewer?url=https://github.com/faizhasim/faizhasim.github.io/files/18120/Docker.in.Learndot.pdf&embedded=true" style="width: 600px;height: 460px;border: #333333 solid 1px;" ></iframe>
++++

---

[qanda]

.There are some interesting questions and feedbacks from audience, and I would like to share some of them:

How does http://kubernetes.io/[Kubernetes] compared to https://www.docker.com/docker-swarm[Docker Swarm] ? ::

It is not a fair, apple-to-apple, comparison. In short, http://kubernetes.io/[Kubernetes] is more like a Cloud OS, build for containers and it shuold be compared to https://aws.amazon.com/ecs/[ECS] or http://mesos.apache.org/[Mesos].
+
It is probably better to compare https://www.docker.com/docker-swarm[Docker Swarm] with https://coreos.com/etcd/[etcd] by CoreOS for example, although they are not exactly the same. https://www.docker.com/docker-swarm[Docker Swarm] is more low level tool to manage your Docker cluster via discovery service from Docker Hub, whereas http://kubernetes.io/[Kubernetes] is more full-fledged ecosystem to run your compute unit on the cloud on Google's Omega paper best practices.


What is the recommended way to run Docker for Windows/Mac development machine? ::

We mainly use http://boot2docker.io/[boot2docker], although some of us actually tried to run it on Vagrant. Now, personally, I just run https://www.docker.com/docker-toolbox[Docker Toolbox], which contains everything including boot2docker.

How do we secure the internal services (like LDAP) from the internet access? ::

While typically you might be able to configure VPN access to LDAP, we simply do not expose it at all. Everything runs on local Docker network. Only the containers can see each other.

Now, the services is composed into a single host machine. What could be your strategy to move to multiple machine? ::

We have been evaluating and considering https://aws.amazon.com/ecs/[ECS] as our main cloud computing strategy. But, we also think project like http://kubernetes.io/[Kubernetes] is really cool. We like ECS due to the security features and tight integration with AWS services, even though it's proprietry. We like Kubernetes being open sources and really make sense and easy to use.




