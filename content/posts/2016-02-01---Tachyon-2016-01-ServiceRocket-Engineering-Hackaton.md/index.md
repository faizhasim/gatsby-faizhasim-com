---
title: "Tachyon - 2016-01 ServiceRocket Engineering Hackaton"
date: "2016-02-01T17:28:42"
template: "post"
draft: false
slug: "/posts/tachyon-2016-01-serviceRocket-engineering-hackaton"
category: "Programming"
tags:
  - hackaton
  - docker
  - servicerocket
description: "We started new year 2016 with biannual ServiceRocket Engineering hackaton event, which is really awesome week for us! We would spend a week to turn an idea into a working prototype in a week time."
---


## A brief intro to hackaton and my team

We started new year 2016 with biannual ServiceRocket Engineering hackaton event, which is really awesome week for us! We would spend a week to turn an idea into a working prototype in a week time.

I was lucky enough to participate along with my teammates, [Long](http://www.longyc.com) and [Helena](http://herrowna.xyz).
We called ourselves, the Team Rocket.

![team rocket](https://cloud.githubusercontent.com/assets/898384/12717569/aa52200a-c923-11e5-98fb-0700da961daa.png)

We really had a tough time getting the right idea for the hackaton and ultimately, we came out with Tachyon – a service that complements [Learndot](https://www.learndot.com) LMS to make invitation to your academy as easy as capturing business card from your smartphone. It is not just a product, it is a production-quality service, which would allow buck scanning of business cards (or any documents) for Learndot academy invites.

## Be smart, do less coding

I knew [Tesseract](https://github.com/tesseract-ocr/tesseract) and I was absolutely certain that I could just find some Docker image from the Dockerhub. So, I can just spin up a container and write another service that consume the Tessaract API. But then, I stumble upon [OpenOCR](http://www.openocr.net/), which build more goodness on top of Tessaract. I really need to give the credit to those guys for open sourcing their work. We were using their example as docker-compose setup according the diagram below.

![architecture](https://cloud.githubusercontent.com/assets/898384/12717784/2ec63c62-c925-11e5-9917-88c9c948f223.png)

As you can see, they have proper RabbitMQ in place, with Tessaract workers and expose everything as REST-ful API for my consumption. Additionally, we also use additional iamge preprocessing by them for stroke width transformation to enchance the image quality for OCR purposes. Without doing too much, more than 50% of the work has been completed for us by choosing the right solution and technology. I think that's very important for a hackaton project.

## Actual Coding is Minimal

The main service that we need is just another REST API on top of the existing OpenOCR API. It would accept valid multipart-encoded image, submit to the OpenOCR backend, then call Learndot API to send an invitation if emails are found and finally give a response back to the caller. We wrote it in NodeJS, although we can pretty much do with anything. We called this Luxon as illustrated below:

![img](https://cloud.githubusercontent.com/assets/898384/12719667/84050ecc-c931-11e5-86e2-68928573d50a.png)

The rest of the works are writing client app that consume the main API and do fancy stuff to impress the audience. We planned for 3 different type of clients; Web Client optimised for mobile, Native Client for Android/iOS and simple CLI for batch processing. We delievered two out of three as we are having some technical issues with the native client. Regardless, we are pretty happy with the work done.

![img](https://cloud.githubusercontent.com/assets/898384/12719699/cd5dc8ca-c931-11e5-9d50-f5ddc13e3a24.png)

## Preparing for the demo

Our slide during the demo can be found [here](https://github.com/faizhasim/faizhasim.github.io/files/112357/2016-02-01.pdf). Our aim is focussed on impressing people that come from different background, both techies and non-techies. Hence, the objectives of our presentation is to ensure that we are selling the idea well and capture the technical details in a simplest manner.

Tachyon is also hosted on Digital Ocean cloud, to ensure that my machine won't do anything funny during the presentation. I also configured [learndot.xyz](http://www.learndot.xyz) for vanity factor 😀. We wanted to show the ability of our service works well for batch processing, so we begun collecting business cards from almost everyone in Level 5 the day before. We'll use those for live demo on the smartphone and ScanSnap scanner for batch processing.

## Presentation and Live Demo

We are the runner up, but I think we nail the presentation regardless. The live demo on my iPhone works great. I was able to invite a bunch of people to Learndot simply by dumping their business cards to the scanner.

