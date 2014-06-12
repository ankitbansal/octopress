---
layout: post
title: "Understanding this keyword in Javascript"
date: 2013-11-08 19:35:59 +0530
comments: true
categories:

keywords: "javascript, this keyword, eval, execution context"
description: "Understanding this keyword in javascript"
---

Many developers find it difficult to understand *this* keyword in javascript. Its more often with developers coming from java background as the way *this* works in java is quiet different from that in javascript. While in java, *this* refers to the object whose method is being called. In javascript, *this* refers to the object in whose context method is invoked also called ‘current execution context’. In this article, I will try to clear up some of the confusions by going through various examples. Before we dive into how *this* is determined, let's understand what is execution context.

### What is Execution Context: ###
When a javascript code run, the environment in which it is evaluated is called execution context.
All javascript code is guaranteed to run in some execution context. By default, execution context
is global.

When a javascript function is called, a new execution context is created and is pushed
to the top of the execution stack. If another function is called inside current function, it
will create a new execution context and  push it to the top of execution stack. Once the
function completes execution, its execution context is popped off making new execution
context to the context below it.

The thing to understand is, every time execution context is changed, *this* gets reassigned to the object in whose
execution context is invoked.

Let’s go through some of the function invocations and figure out what this will resolve to:

### Global Inovcation ###
``` javascript
function hello() {
	console.log(this);
}

hello(); // this points to window

var someRef = hello;
someRef(); // this points to window
```
'hello' method over here is not associated with any object. So, it runs in global execution context. Thus, *this* over here points to 'window' which is the default global object in the case of browser.
The same goes true for method 'someRef'.

### Object Invocation ###
``` javascript
var foo = {
   bar: hello
}

foo.bar(); //this points to foo
```
In this example, we have created an object called foo and calling method of bar which refers to our previously defined hello method. Since, bar method is the current execution context, *this* refers to its object which is foo.

Another way to define an object is via constructor:
``` javascript
var baz = function() {
  this.bar = hello;
};

var someRef = new baz();
someRef.bar(); //this points to baz
```
As you might have guessed, *this* over here will refer to baz object.

How about if we add a prototype method:
```
baz.prototype = {
  barPrototype: hello
};

var someRef = new baz();
someRef.barPrototype(); // this refers to baz object
```
Again, over here *this* will refer to baz object.

### Invocation using apply, call and bind ###

The next set is of functions allows you to explicitly specify *this* value. These methods are Call, Apply and Bind Methods.
```
var foo = {};
//apply
hello.apply(foo); //this points to foo
//call
hello.call(foo); //this points to foo
//bind
var someRef = hello.bind(foo);
someRef(); //this points to foo
```

Call and apply immediately invokes the function. While bind allow you to set the function context that can be invoked at later point of time.

### Invocation using eval ###
The eval function provides capability of dynamic execution of JavaScript code represented as a string.
```
var foo = {
  bar: function() {
  eval(“hello()”);
}
}
foo.bar(); //this points to global object
```
In this case, *this* will refer to Window. Let’s see why. When we invoke bar *this* was called with foo,
but inside bar function, eval invokes hello without referring an object, so it goes through global scope.

### Conclusion ###

*this* is very powerful in javascript and many libraries utilize it at its core. I hope this blog have helped you in clearing some of the doubts and gaining a better understanding of how *this* evaluates in javascript.
