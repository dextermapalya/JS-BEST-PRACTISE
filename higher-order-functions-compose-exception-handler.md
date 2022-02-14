### Exception Composition ###

#### Excpetion handling for higher order functions (also called function composition) ####

##### Compose #####
> In algebra, function composition allows you to apply one function to the output of another function.
```
g(f(x))
```
> The output of f(x) is passed as input to function g() 

##### Solve a problem without compose (or higher order functions) #####
```
function getUsernameAndConvertToUpperCase(user) {
  var username = user.name;
  var useruppercase = username.toUpperCase();
}
```
> Dissecting above code, you will find two operations are performed in a single function, without any exception handling

##### Solve th above problem by decoupling ##### 
```
var user = { name: 'Gianmarco', password: 1234 };
//logic separation for reusability or decouple
var getUserName = function getUserName(user) {
  return user.name;
};
//logic separation for reusability
var upperCase = function upperCase(string) {
  return string.toUpperCase();
};

upperCase( getUserName(user) );
```

##### Apply compose to above decoupled functions #####
```
const compose = (g,f) => x => g( f(x) ) //ES6 format

or using vanilla JS

var compose = function compose(g, f) {
  return function (x) {
    return g(f(x));
  };
};
// 'x' in compose() 

var user = { name: 'Gianmarco', password: 1234 };
// function 'f' in compose()
// function 'g' in compose()

//assign a variable (getUserNameAndUpperCase) that points to compose but the function is not yet invoked
var getUserNameAndUpperCase = compose(upperCase, getUserName);

getUserNameAndUpperCase(user);  //invoke the function with an argument
```
> The problem with this implementation of compose() is that it takes as parameters just two functions (in our example, upperCase()and getUserName()).

Let's suppose we want to add another function that returns the first four characters of a string:
```
var firstFour = function firstFour(string) {
  return string.substring(0, 4);
};
```
##### How can we compose more than two functions at a time? #####
> We can use Compose and the reduceRight() method:

```
const compose = (...functions) => x => functions.reduceRight((acc, fn) => fn(acc), x); // ES 6
or vanilla js

var compose = function compose() {
  for (var _len2 = arguments.length, functions = Array(_len2), _key2 = 0; _key2 < _len2; _key2++) {
    functions[_key2] = arguments[_key2];
  }

  return function (x) {
    return functions.reduceRight(function (acc, fn) {
      return fn(acc);
    }, x);
  };
};
```
> With revised implementation, we are not restricted by number of input arguments, the revised compose function can accept, 1, 2 ,3 or n arguments
> Now using compose the above functions can be reused or even mixed up

```
var user = { name: 'Gianmarco', password: 1234 };

ex:1 var getUserNameAndUpperCase = compose(upperCase, getUserName);
//invoke it  getUserNameAndUpperCase(user)
ex:2 var getFirstFourUserName = compose(firstFour, getUserName);
//invoke it  getFirstFourUserName(user)

ex:3 var getFirstFourUserNameAndUpperCase = compose(upperCase, firstFour, getUserName);
//invoke it  getFirstFourUserNameAndUpperCase(user)
```
### Handle Higher Order Function Exceptions ###
> How do you handle runtime errors without a mess of try…catch and if…else 

### Exception Composition to the rescue ###
> Let's dissection a chatbot using function compose and exception handlers
```
let viewHike = (message) => {
  let match = /^view hike (.+)$/.exec(message);
  let hike = match && match[1];

  return (
    !hike ?
      raise(new ValidationError(message))
  : hike.includes('lost') ?
      raise(new NotFound(hike))
  :
      `Details about <${hike}>`
  );
};

let chatbot = (message) => {
  return viewHike(message);
};
chatbot('view hike mirror lake');
should result in // => 'Details about <mirror lake>'

chatbot('view hike lost lake');
// => 💥 NotFound: lost lake

chatbot('show hike blue ridge');
// => 💥 ValidationError: show hike blue ridge
```
> The chatbot only understands one command, `view hike` Our function viewHike() replies with details about the hike, but when users ask for a hike that isn’t in the database or their syntax is a bit off, the viewHike() function will throw a custom exception like a NotFound error or a ValidationError. 

>In either case, the chatbot shouldn’t blow up and stop running, lets enclose viewHike within try…catch statement 
```
let chatbot = (message) => {
  try {
    return viewHIke(message);
  } catch (error) {
    return `No such hike.`;
  }
};

chatbot('view hike mirror lake');
// => 'No such hike.'
chatbot('view hike lost lake');
// => 'No such hike.'
chatbot('show hike blue ridge');
// => 'No such hike.'
```
> By introducting try...catch all invocations are handled gracefully without crashing. On close inspection, you will find that there is substantial amount of boilerplate code for every type of exception. Let's introduce a resuce() function within the try...catch
```
let chatbot = (message) => {
  try {
    return viewHIke(message);
  } catch (error) {
    rescue(error, NotFound);
    return `No such hike.`;
  }
};

chatbot('view hike mirror lake');
// => 💥 ReferenceError: viewHIke is not defined because viewHIke() was invoked with a capital HI viewHIke(), but our function is called viewHike()
```

> The problem with rescue() is that it only helps us catch one type of error at a time. So how do we handle both a NotFound error and ValidationError? either by using an if..else or switch, but this will become difficult to maintain when the number of errors increase, we can't keep altering our if...else statements for every new error created

> Let’s refactor this formula into a function called swallow(), which takes the error type to swallow, a fallback function, and a function that will potentially throw an error.
```
let swallow = (type, fail, fn) => {
  try {
    return fn();
  } catch (error) {
    rescue(error, type);
    return fail(error);
  }
};
```
> Now we’ll use swallow() to create a new version of viewHike() that is safe from NotFound errors.
```
let safeViewHike = (message) =>
  swallow(NotFound, () => `No such hike.`,
    () => viewHike(message)
  )
;

let chatbot = safeViewHike;
```
> The swallow() function can be further refactored/optimised to take advantage of currying, we can eliminate a lot of the extra function calls and argument gathering.
```
let swallow = (type) => (fail) => (fn) => (...args) => {
   try {
    return fn(...args);
   } catch (error) {
     rescue(error, type);
     return fail(error);
   }
 };

```
> Now! swallow() is a Higher-Order Function: it takes in an  ❌unsafe function that throws a particular kind of error, and returns a ✅SAFE version of the function. Because swallow() returns a function that is ✅safe from the NotFound error type, there’s no reason we can’t pass that function into swallow() again to make it safe from a ValidationError too!
```
let safeViewHike =
  swallow(ValidationError)(() => `Invalid format.`)(
    swallow(NotFound)(() => `No such hike.`)(
      viewHike
    )
  );
```
> The refactored safeViewHike() now accepts nested swallow() i.e swallow(swallow('Some Error') ), this code is now getting nasty to maintain

> Instead of nesting swallow()s inside each other, we can list them out from top to bottom and feed the original viewHike() function at the very end. 

```
let safeViewHike = compose(
  swallow(ValidationError)(() => `Invalid format.`),
  swallow(NotFound)(() => `No such hike.`),
)(viewHike);
```
> This style of creating functions without first gathering and passing around all their arguments is called Point-free style, and it’s a big part of what makes functional programming so elegant.


> All of the above code refactoring has been reduced to a final acceptable / reusable sample code

```
<script>

'use strict';


class NotFound extends Error {
  constructor(message) {
    super(message);
    this.name = 'NotFound';
  }
}

class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = 'ValidationError';
  }
}

var viewHike = function viewHike(message) {
  var match = /^view hike (.+)$/.exec(message);
  var hike = match && match[1];

  return !hike ? raise(new ValidationError(message)) : hike.includes('lost') ? raise(new NotFound(hike)) : 'Details about <' + hike + '>';
};

/* exception handler */
var swallow = function swallow(type) {
  return function (fail) {
    return function (fn) {
      return function () {
        try {
          return fn.apply(undefined, arguments);
        } catch (error) {
          rescue(error, type);
          return fail(error);
        }
      };
    };
  };
};


/* higher order function composition */
var compose = function compose() {
  for (var _len = arguments.length, fns = Array(_len), _key = 0; _key < _len; _key++) {
    fns[_key] = arguments[_key];
  }

  return function (x) {
    return fns.reduceRight(function (v, f) {
      return f(v);
    }, x);
  };
};

/* Exception handler */
var raise = function raise(error) {
  throw error;
};

/* function rescue */
var rescue = function rescue(error, type) {
  return error instanceof type ? error : raise(error);
};

/* higher order composable error handler */
var safeViewHike = compose(swallow(ValidationError)(function () {
  return 'Invalid format.!';
}), swallow(NotFound)(function () {
  return 'No such hike.';
}))(viewHike);

var chatbot = safeViewHike; //assign to variable
//invoke it
ex 1: chatbot('view hike mirror lake')  // no error
ex 2: chatbot('view hike lost lake') );// must print error gracefully
ex 3: chatbot('show hike blue ridge') );// must print error  gracefully

ex 4 console.log('testing js function compose error handler', chatbot('view hike mirror lake') ) // no error
ex 5 console.log('testing js function compose error handler', chatbot('view hike lost lake') );// must print error
ex 6 console.log('testing js function compose error handler', chatbot('show hike blue ridge') );// must print error

</script>

```