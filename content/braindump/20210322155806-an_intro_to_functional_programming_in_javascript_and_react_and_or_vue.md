+++
title = "An intro to Functional Programming in JavaScript~ (and React and/or Vue)"
author = [7696122]
lastmod = 2022-01-19T10:37:10+09:00
tags = ["번역", "javascript"]
draft = false
+++

[An intro to Functional Programming in JavaScript~ (and React and/or Vue) | by...](https://agm1984.medium.com/an-overview-of-functional-programming-in-javascript-and-react-part-one-10d75b509e9e)  

JavaScript(React, Vue)에서 함수형 프로그래밍 하는 법 개요  

This article is worth your retinal zoom-in time, but remember that good is subjective to the observer. This is about Functional Programming and data flow over time. This article is for people that haven’t looked that close at Functional Programming yet—or for those whom wish to build intuition about how to approach data flow through apps.  

> **Bonus Note:** patterns described here should be applicable in React, Vue, Angular, and really any other programming language where the main focus is data or a user — while actions and events are involved. Imagine for a moment, if something is going from somewhere-to-somewhere, we can start to sample ideas from Mathematics and Physics. We can create well-characterized patterns to solve well-characterized scenarios, and we can also discover interesting patterns.  

The most important take away from this article is that immutability and maintenance of the integrity of your application’s state are among the most important, and those ideas will lead you to effective function composition.  

> Functions can act as labels… labels of function throughout your application. Sometimes people label too much and create a lot of granular functions. It is important to note that the inlet and outlet of each function represent a jump in dimension. **Abstraction can cause confusion when paired with granularity because you might be comparing two functions not directly connected together; you also lose some freedom of access to local data if you move upstream.**
