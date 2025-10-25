---
title: "Golang: A Journey So Far"
date: "2019-01-01T17:28:42"
template: "post"
draft: false
slug: "/posts/golang-a-journey-so-far"
category: "Programming"
tags:
  - "go"
  - "golang"
  - "mock"
description: >
  I think Golang has a huge potential. People that come from Node, might feel easier to get started without investing too much learning curve. JVM people might benefits Golang for faster startup time. 
  In the past, I made a careful decision to implement HTTP serverless using Node, due to the cold start concerns with JVM. With AWS Lambda supports for Golang, this is just superb. If I’m were to break down a large Node serverless function, I wouldn’t mind using Golang."
---

## Mocking for Test

I am a bit annoyed with the amount of code that I need to write to mock sideeffects, especially if you have no control over it. For example, mocking filesystem operations, is pretty much this:

https://talks.golang.org/2012/10things.slide#8

It make sense. You don't mock what you don't own. But, the additional structs and interfaces just to support the mocks in test, is a bit annoying. I come from JVM and Node, so that might explain why. Javascript is pretty much dynamic. Mocking libraries on JVM usually rely on some sort of bytecode manipulations, so things are a bit easier.

Again, still, I heard people said you might be able to do some crazy linking when compiling Go, but that's just crazy.

While it's way harder to mock, it's not a bad thing at all. It makes things that should rarely happen, hard.

## Dependency Injection

I wasn't quite sure on how to implement DI with Go. The value of DI is tremendous when writing testable code. 

I was able to quickly write a bunch of functions and make simple code works. But, to make it more testable (especially if you're doing some level of TDD), you might want to do with DI pattern.

Blogpost from Go-Jet engineering really helps me: https://blog.gojekengineering.com/the-many-flavours-of-dependency-injection-in-go-25aa070d79a0.

But, more importantly, compile-time DI from golang.org is really exciting indeed! Check it out at https://blog.golang.org/wire. I haven't really read the entire thing, but this reminds me a lot on MacWire (compile-time DI for Scala that leverages the `macro` feature).

## Current Impression

I think Golang has a huge potential. People that come from Node, might feel easier to get started without investing too much learning curve. JVM people might benefits Golang for faster startup time.

In the past, I made a careful decision to implement HTTP serverless using Node, due to the cold start concerns with JVM. With AWS Lambda supports for Golang, this is just superb. If I'm were to break down a large Node serverless function, I wouldn't mind using Golang.

