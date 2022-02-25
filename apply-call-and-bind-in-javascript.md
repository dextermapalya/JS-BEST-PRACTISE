# Awesome explanation of apply, call and bind in JavaScript with real life example #

> After reading this article, if you haven't used apply, call and bind yet, you screwed my head off!

Suppose we have such an example:
    - Cats eat fish
    - Dogs eat meat

What we want to achieve in the end is:

    - Cats eat meat
    - Dogs eat fish

### Preface ###
> First, prepare two objects: cat, dog:

```
let cat = {
  name: "🐱",
  eatFish() {
    console.log(`${this.name} eat fish！`);
  },
};

let dog = {
  name: "🐶",
  eatMeat() {
    console.log(`${this.name} eat meal！`);
  },
};
```

> After we are ready, let's implement the call first.

### call ###

> If the dog eats fish, it needs to be used like this: cat.eatFish.call(dog), it can be seen that the call is called the method of eating fish on the cat, and the parameters are dog and fish, so it should be used like this:

```
cat.eatFish.call(dog);
```

> For the call method, the approximate logic is as follows:
    1. The first parameter passed in is used as the context; here is the dog.
    2. The dog add a eatFish method, pointing to the cat's eatFish, which is the cat's this.
    3. Of course dog can eat all kinds of fish too
    4. After eating, the dog deletes the method of eatFish, because this does not belong to it, it is just borrowed.

> According to the above logic, we can write:    

    ```
    Function.prototype.defineCall = function (context, ...args) {
    // If there is no parameter dog, the default is window
    var context = context || window;
    // The dog add a method to point to the cat's method of eatFish, which is this
    context.fn = this;
    // Dog can eat all kinds of fish, that is, there may be multiple parameters
    let result = context.fn(...args);
    // Delete the way the dog borrows the cat's way of eatFish
    delete context.fn;

    return result;
    };
    ```
> In this way, a custom call is completed! Let's test it now:

```
cat.eatFish.defineCall(dog);
dog.eatMeat.defineCall(cat);// output:
// 🐶 eat fish！
// 🐱 eat meal！
```

> 🎉 Now the dog can eat fish, and the cat can eat meat!

> Now let's let the dog eat more kinds of fish, let's briefly change the cat's eatFish(This dog is a bit greedy):

```
let cat = {
  name: "🐱",
  eatFish(...args) {
    console.log(`${this.name} eat fish！what it eats：${args}`);
  },
};
```
> Then we use it again like this:

```
cat.eatFish.defineCall(dog, "salmon", "tuna", "shark");
// output：
// 🐶 eat fish！what it eats：salmon,tuna,shark
```

> In this way, dog can eat all kinds of fish. Of course, it is also possible to use arguments to manipulate parameters.

### apply ###
> The usage of apply and call is similar. The difference is that the second parameter is an array, we can write it like this:

```
Function.prototype.defineApply = function (context, arr) {
  var context = context || window;
  let result;
  context.fn = this;
  if (!arr) {
    // If no parameters are passed, execute directly
    result = context.fn();
  } else {
    // Execute if there are parameters
    result = context.fn(...arr);
  }
  delete context.fn;
  return result;
};
```
> Now use it again and see if it is written correctly: 

```
cat.eatFish.defineApply(dog, ["salmon", "tuna", "shark"]);// output:
// 🐶 eat fish！what it eats：salmon,tuna,shark
```

### bind ###
> Now that call and apply have been implemented, the slightly more difficult bind can also be implemented. After all, they are friends. 

> Let's first take a look at what bind has:

1. bind is also used to convert this pointer.
2. bind does not execute immediately like the two of them but returns a new function bound to this that needs to be called again to execute.
3. bind supports function currying.
4. The this of the new function returned by bind cannot be changed, nor can call and apply.

> Let's write it step by step, starting with the simplest one:

```
Function.prototype.defineBind = function (obj) {
  // If this does not exist, this may point to window during execution
  let fn = this;  return function () {
    fn.apply(obj);
  };
};
```
> Then add the function of passing parameters to it, which becomes like this:

    ```
    Function.prototype.defineBind = function (obj) {
    // The first parameter is this, so it has to be cropped from the first
    let args = Array.prototype.slice.call(arguments, 1);
    // If this does not exist, this may point to window during execution
    let fn = this;
    return function () {
        fn.apply(obj, args);
    };
    };
    ```
> Then add currying to it:

```
Function.prototype.defineBind = function (obj) {
  // The first parameter is this, so it has to be cropped from the first
  let args = Array.prototype.slice.call(arguments, 1);
  let fn = this;
  return function () {
    // In the second call, we also grab the arguments object
    let params = Array.prototype.slice.call(arguments);
    // Pay attention to the order of concat
    fn.apply(obj, args.concat(params));
  };
};
```
> Now defineBind has almost taken shape, let it be upgraded to a real bind, and there is one more detail:

>> The returned callback function can also be constructed in the form of new, but during the construction process, its this will be ignored, and the returned instance can still inherit the constructor properties and prototype properties of the constructor, and can receive properties normally (also It's just that this is lost, everything else is normal).

>> This means that we can customize the judgment and prototypal inheritance of this, so it is more difficult, let's understand one thing first: the constructor of the instance of the constructor points to the constructor itself:

```
function Fn(){};
let o = new Fn();console.log(o.constructor === Fn); // true
```
> And when the constructor is running, the internal this points to the instance (who calls, this points to), so this.constructor points to the constructor:

```
function Fn() {
  console.log(this.constructor === Fn); // true
};let o = new Fn();
console.log(o.constructor === Fn); // true
```
> Is it possible to change prototypal inheritance by changing the point of this.contructor?

> Of course, the answer is right! When the return function is used as a constructor, this should point to the instance, and when the return function is used as a normal function, this should point to the current context:

```
Function.prototype.defineBind = function (obj) {
  let args = Array.prototype.slice.call(arguments, 1);
  let fn = this;
  let bound = function () {
    let params = Array.prototype.slice.call(arguments);
    // Judging the calling method by constructor, if true this points to the instance, otherwise it is obj
    fn.apply(this.constructor === fn ? this : obj, args.concat(params));
  };
  // Prototype chain inheritance
  bound.prototype = fn.prototype;
  return bound;
};
```
> This way, a bind is over, and the instance produced by the returned constructor doesn't affect the constructor.

> **but! Modifying the instance prototype directly affects the constructor!**

> ***What about this? It would be nice if there was nothing in the prototype of the constructor so it wouldn't affect each other… blablablablabla…***

> Write a small example, using a mediator, so that the prototype of the constructor can only affect the instance and nothing else:
```
function Fn() {
  this.name = "Nick";
  this.sayAge = function () {
    console.log(this.age);
  };
}
Fn.prototype.age = 26;
// Create a blank function Fn1, simply copy the prototype of Fn
let Fn1 = function () {};
Fn1.prototype = Fn.prototype;
let Fn2 = function () {};
Fn2.prototype = new Fn1();
```

> A layer of __proto__ is added to Fn2, so that the prototype of Fn2 points to an instance and the prototype of the instance is Fn so that changes in Fn2 will not affect Fn (of course, it can still be modified through __proto__.__proto__)!
```
Function.prototype.defineBind = function (obj) {
  let args = Array.prototype.slice.call(arguments, 1);
  let fn = this;
  // Create a mediator function
  let fn_ = function () {};
  // The Fn2 mentioned above is the bound here
  let bound = function () {
    let params = Array.prototype.slice.call(arguments);
    // Judging the calling method by constructor, if true this points to the instance, otherwise it is obj
    fn.apply(this.constructor === fn ? this : obj, args.concat(params));
  };
  fn_.prototype = fn.prototype;
  bound.prototype = new fn_();
  return bound;
};
```

> Then, use an error report to polish it up:

```
Function.prototype.defineBind = function (obj) {
  if (typeof this !== "function") {
    throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
  };
  let args = Array.prototype.slice.call(arguments, 1);
  let fn = this;
  // Create a mediator function
  let fn_ = function () {};
  // The Fn2 mentioned above is the bound here
  let bound = function () {
    let params = Array.prototype.slice.call(arguments);
    // Judging the calling method by constructor, if true this points to the instance, otherwise it is obj
    fn.apply(this.constructor === fn ? this : obj, args.concat(params));
  };
  fn_.prototype = fn.prototype;
  bound.prototype = new fn_();
  return bound;
};
```

***Handwritten bind is complete!***

***Finally, use a dog to eatFish to verify:***

```
let cat = {
  name: "🐱",
  eatFish(...args) {
    console.log(`${this.name} eat fish！what it eats：${args}`);
  }
};
let dog = {
  name: "🐶"
};
cat.eatFish.defineBind(dog, "salmon", "tuna")("shark");
// output:
// 🐶 eat fish！what it eats：salmon,tuna,shark
```
> Finally, a handwritten bind of the es6 version is attached, you can go through it, it is still relatively clear:
```
Function.prototype.defineBind = function (context, ...rest) {
  if (typeof this !== "function") {
   throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
  }
  var self = this;
  return function F(...args) {
    if (this instanceof F) {
      return new self(...rest, ...args);
    }
    return self.apply(context, rest.concat(args));
  };
};
```
