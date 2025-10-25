---
title: "How Developers Can Educate Stakeholders during Software Development?"
date: "2016-10-27T17:28:42"
template: "post"
draft: true
slug: "/posts/how-developers-can-educate-stakeholders-during-software-development"
category: "Programming"
tags:
  - practice
description: "As a developer, all sort of funny requirements being thrown at you by the stakeholders, for example product owners. How often you heard questions or requirements like: \"The system must be fast.\" or \"This system must provide instant update to be viewed by thousands of users.\" and yet \"We have SLA for our service to keep it up at 99.9999% uptime.\""
---

As a developer, all sort of funny requirements being thrown at you by the stakeholders, for example product owners. How often you heard questions or requirements like: "The system must be fast." or "This system must provide instant update to be viewed by thousands of users." and yet "We have SLA for our service to keep it up at 99.9999% uptime."

Or even worse, you will only get functional requirements from the stackholders and engineering team just implement the functional requirements without educating the stakeholders on the non-functional requirements. Maybe some organizations are really top-down in decision making and engineering team have no choice but to blindly concur with crazy requirements for the quick win. As a result, they had to take shortcuts and deliver crappy results which won't last long.

Requirements are often proposed by product owners and it's very important for developers to challenge the proposal during refinement process.

Sometimes people said they want to get very dynamically consistent system that can serve thousands of concurrent sessions. Most of the time after studying their requirement, you don't really need a strictly consistent system. For example, typical content management system have a very different non-functional requirement from messaging system. 
