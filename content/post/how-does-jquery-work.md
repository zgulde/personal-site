+++
date = "2016-08-15T21:24:28-05:00"
draft = false
title = "Why all the $s in my JavaScript?"
+++

In case you've been living under a rock for the past the past 9 years,
[jquery](http://jquery.com) is what all those `$`s are.

I want to investigate the `$` a little further and explore the syntax of
this thing.

# How does jQuery work?

I don't mean how does jQuery traverse the DOM, I don't mean how does jQuery
send ajax requests, I don't even mean how does jQuery allow for the chaining of
methods, I mean what's up with this syntax?

```js
$('#some-id').css('color', 'salmon');

$.get('http://someurl.com');
```

We call `$` with parentheses after it like a function, but also access the
`get` method on it with a `.`.

It looks like `$` is both a function and a object, what's going on?

## Wait, what's up with the `$`?

Firstly, let's note that all our usage of jQuery uses the `$`. There is no
special meaning behind `$` as far as JavaScript is concerned, it just another
identifier. In simple terms,

**identifier**: something that can be used as a name for a variable or a function

So the following code is valid...

```js
var someNumber = 42;
var someString = 'So long and thanks for all the fish.';
var someFunction = function () { alert('hey!'); };
```

Because `someNumber`, `someString`, and `someFunction` are all valid
identifiers.

JavaScript is actually quite permissive in what it allows as an
identifier. Not only can you use alphanumeric characters, but also many
different special characters, including `_`, `$`, and even unicode emojis
(please don't do this).

So the following is absolutely valid JavaScript:

```js
var $ = 'The Holy Handgranade Of Antioch';
var _ = function () {
    alert('hey!');
};
```

But please don't write code like this, as those are not very descriptive variable names.

`$` is just a identifier we can use to refer to the `jQuery` object. There are
several popular libraries that use the convention of using a special character
for an object other than jQuery, notably
[underscore.js](http://underscorejs.org/). In fact, jQuery even has a
[`noConflict` method](https://api.jquery.com/jquery.noconflict/) that can be
used if you are pulling in another library that defines `$`.  

## Okay, so you didn't answer the original question..

I will! Short answer: in JavaScript, (almost) everything is an object,
including functions. 

Long answer:

In JavaScript (almost) everyting is an object. The [primitive
types](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) are the
only exceptions. A function is not one of our primitive types, so you could
treat a function just like any other JavaScript object. That is,

```js
var myObject = {};
myObject.foo = 'bar';
```

Assigns the value of `'bar'` to the property `foo` of `myObject`. Just like we
can assign additional properties to `myObject`, we could also assign additional
properties to a function object.

```js
function myFunction(){
    console.log('JavaScript is Awesome!');
}

myFunction.someProperty = 'some value';

myFunction(); // outputs 'JavaScript is Awesome!'
console.log(myFunction.someProperty); // outpus 'some value'
```

Or even

```js
function myFunction(){
    console.log('JavaScript is Awesome!');
}

myFunction.someOtherFunction = function () {
    console.log('Nested functions, what!?!?');
}
    
myFunction(); // outputs 'JavaScript is Awesome!'
myFunction.someOtherFunction(); // outputs 'Nested functions, what!?!?'
```

So we can see that `jQuery`, or `$`, can be both a function (`$('#some-id')`),
and an object that has methods (`$.ajax(...)`).

[Whoa.](https://www.youtube.com/watch?v=imamcajBEJs)

