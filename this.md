# What does `this` mean in JavaScript?

JavaScript has many keywords, one of which is `this`.  When I first started using JavaScript, the concept of `this` and the overall concept of scoping seemed a bit confusing to me.  I really didn't take the time to absorb what `this` meant or how the meaning of `this` changes.  I looked at the usual resources (i.e. Google) and found a lot of great sources but they were all written in a very technical manner.  After reading through several online blogs and I definitely had a better, but sort of hazy, understanding of scoping.  Therefore, I figured it was time to dig in and ["rubber-duck"](https://en.wikipedia.org/wiki/Rubber_duck_debugging) `this` once and for all.

What makes `this` sometimes hard to understand at first is the unique way it behaves and what it is referring to. The value of this depends upon how its function is invoked.  There are also "workarounds" which can be used to make `this` refer to what you want it to, such as binding.  I will try to explain all of these as best I can in the next sections.  A lot of this summary will follow the outline of the Mozilla Developer Network (MDN) [documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) on `this`, as well as borrow some code snippets to show examples of `this` in use.

## Global Context

The `this` keyword can exist outside of functions.  When it does, it is referring to the global object. The global object in a web browser is also the window object.

```JavaScript
// the Mozilla documentation as always is very helpful--especially for example code snippets:

console.log(this.document === document); // this would log "true" because the global object contains the document.

console.log(this === window); // this would also log true because the global object in the browser is the "window" object.
```

In the above example, `this` is used in a global context, which means that it is always referring to the global object or browser window. Where it gets confusing is when `this` is used in a function.  It gets even more confusing when `this` is used in nested functions.

## Function Context

When `this` is used inside a function, its value depends upon how that function is called.  In this section, we will look at various types of functions and their invocations:
* Default (simple function calls)
* Object Methods
* .call(), and .apply(), and .bind()
* Constructors

### Default (simple function calls)

By default, `this` refers to the object that contains the function it is called in.  The `this` keyword is a reference to the object that owns the currently executing code.  So for simple examples like the one below, `this` refers to the global object:

```JavaScript
function simpleFunction () {
  return this;
};

// calling this function in the browser
simpleFunction(); // returns "window"

// in Node
simpleFunction(); // returns "global"

// in strict mode, this function call will return "undefined"
```

##### A quick note about "strict mode"
In __strict mode__ `this` refers to whatever value it was set to when execution began.  That will become clearer in a minute when we talk about scope.  In the above example, the value of `this` was never defined when it was invoked.

### Object Methods
When a function is contained within an object, that function is referred to as an object method.  In this instance, `this` still behaves as expected--sometimes.  Remember, `this` has to do with how the function is called.  A method call will operate on the object that contains it.  Let's make an object to demonstrate:

```JavaScript
var carObject = {
  make: "Subaru",
  model: "Outback",
  getModel: function () {
    return this.model;
  }
};
// In the getModel method, "this" refers to the "carObject" object containing the method.  
console.log(carObject.getModel()); // logs "Outback" since this refers to the "carObject" object.
```

Here's where `this` starts to get weird:
```JavaScript
var myMethod = function() {
  return this.model;
};

var carObject = {
  make: "Subaru",
  model: "Outback",
  getModel: myMethod
};

// If we call the myMethod function as a method of the carObject
carObject.getModel(); // "this" refers to the carObject

// If we call the myMethod functionn on its own, outside of the object
myMethod(); // "this" refers to the global object
```
### .call(), and .apply(), and .bind()

So now that we have a sense of how `this` behaves, let's control what it refers to through binding. We cam bind the value of `this` to a particular object by passing that object in the function call.  This is done via the `call()` and `apply()` methods.

#### .call()
When using `.call()`, we pass the object that we would like to bind `this` to as the first argument, followed by whatever other parameters the function requires as additional arguments.

```JavaScript
var myMethod = function(string1, string2) {
  console.log(this.make + ' ' + this.model + ' ' + string1 ' ' + string2);
};

var carObject = {
  make: "Subaru",
  model: "Outback",
};

myMethod.call(carObject, "is", "a car."); // "this" is bound to carObject
```

#### .apply()

`.apply()` is used in the same way as `.call()`, passing the object that we would like to bind `this` to as the first argument.  The second argument is an array whose members contain the remaining arguments for the function call.

```JavaScript
var myMethod = function(string1, string2) {
  console.log(this.make + ' ' + this.model + ' ' + string1 ' ' + string2);
};

var carObject = {
  make: "Subaru",
  model: "Outback",
};

myMethod.apply(carObject, ["is", "a car."])
```
##### Another quick note:
Call and apply expect the first argument to be an object.  If something other than an object is passed to call or apply as the first argument, it will be converted to an object using that value's constructor.

#### .bind()

The `.bind()` method creates a new function with the same body as the original function that `.bind()` is being used on, with `this` permanently bound to the first argument of `.bind()`.

```JavaScript
function f1() {
  return this.name;
};

var me = f1.bind({name: "John-Mike"});

var obj = {
  name: "Not Me"
  f1: f1,
  me: me
};

console.log(obj.f1(), obj.me()); // returns "Not Me, John-Mike"
```

### Constructors

When a function is used as a constructor with the `new` keyword, it generates a new object.  The constructor's `this` is bound to the new object (instance).

## Conclusion

The `this` keyword, when used in a global context refers to the global object.  When a method is called, its `this` is set to the object that the method is called on. The value that `this` refers to can change dependin upon how a function is called.  We can bind `this` by using various methods from the Function.Prototype such as `.call()`, `.apply()`, and `.bind()`.  
