---
title: "For-loop, to ++i or i++ or not?"
date: "2013-01-06T17:28:42"
template: "post"
draft: false
slug: "/posts/for-loop"
category: "Programming"
tags:
  - java
  - c/c++
description: "I just recalled an interesting theory mentioned by my C lecturer back in uni days, use ++i instead of i++ in a for-loop."
---

I just recalled an interesting theory mentioned by my C lecturer back in uni days, use `++i` instead of `i++` in a for-loop.

```c
for (int i = 0; i < len; ++i) {
    // whatever code here....    
}
```

Theoretically (or without compiler optimization), `++i` is faster than `i++` because `i++` need to return the un-incremented value and store it first, before performing the increment. But, `++i` can return the incremented value without storing it first.

While the claim is valid, for most languages and modern compilers, it doesn't really matter. In Java, it will produce the same bytecode. I think in modern C / C++ compilers, they did some optimization to that.

I still have the old habit of using `++i` instead of `i++` when the rest of my team use `i++`. Need to take a note on this. 

> Code Consistency
