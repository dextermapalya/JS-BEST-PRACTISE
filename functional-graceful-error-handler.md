### Functional Error Handler using composition technique ###

#### Exceptions are useful when they eliminate if…else statements from calling functions, ####
```
let chatbot = (message) => {
  return viewHike(message);
};

chatbot('view hike mirror lake');
// => 'Details about <mirror lake>'
chatbot('view hike lost lake');
// => 💥 NotFound: lost lake
chatbot('show hike blue ridge');
// => 💥 ValidationError: show hike blue ridge
```
> our chatbot is programmed to respond only to mirror lake, any thing else passed will cause it to crash, which is not how it should respond

### Sprinkle the default try/catch  ###
> Lets refactor with try/catch
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
> All errors return same result, no such hike and also increases the boiler plate code. To make sure we only rescued a particular error type, we introduced a simple utility
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
// => 💥 ReferenceError: viewHIke is not defined
```
> Lets handle not found exception
```
let chatbot = (message) => {
   try {
   return viewHike(message);
   } catch (error) {
     rescue(error, NotFound);
     return `No such hike.`;
   }
 };
 chatbot('view hike mirror lake');
// => 'Details about <mirror lake>'
chatbot('view hike lost lake');
// => 'No such hike.'
chatbot('show hike blue ridge');
// => 💥 ValidationError: show hike blue ridge
```
> The problem with rescue() is that it only helps us catch one type of error at a time. So how do we handle both a NotFound error and ValidationError? So do we have to give up the rescue() utility altogether ?

> Let’s extract this formula into a function called swallow(), which takes the error type to swallow, a fallback function, and a function that will potentially throw an error.
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
> Lets remove cryptic code

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

> 
```
let safeViewHike =
  swallow(NotFound)(() => `No such hike.`)(
    viewHike
  );
```
> Lets create a Higher-Order Function: it takes in an  💥 unsafe function that throws a particular kind of error, and returns a safe version of the function.
```
let safeViewHike =
  swallow(ValidationError)(() => `Invalid format.`)(
    swallow(NotFound)(() => `No such hike.`)(
      viewHike
    )
  );
chatbot('view hike mirror lake');
// => 'Details about <mirror lake>'
chatbot('view hike lost lake');
// => 'No such hike.'
chatbot('show hike blue ridge');
// => 'Invalid format.'
```

#### Finally the compose comes to our rescue ####
```
let compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);
let safeViewHike = compose(
  swallow(ValidationError)(() => `Invalid format.`),
  swallow(NotFound)(() => `No such hike.`),
)(viewHike);
```
>This style of creating functions without first gathering and passing around all their arguments is called Point-free style, and it’s a big part of what makes functional programming so elegant.

##### Complete Sample code is listed below #####
```
'use strict';

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

function _possibleConstructorReturn(self, call) { if (!self) { throw new ReferenceError("this hasn't been initialised - super() hasn't been called"); } return call && (typeof call === "object" || typeof call === "function") ? call : self; }

function _inherits(subClass, superClass) { if (typeof superClass !== "function" && superClass !== null) { throw new TypeError("Super expression must either be null or a function, not " + typeof superClass); } subClass.prototype = Object.create(superClass && superClass.prototype, { constructor: { value: subClass, enumerable: false, writable: true, configurable: true } }); if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass; }

var rescue = function rescue(error, type) {
  return error instanceof type ? error : raise(error);
};

var raise = function raise(error) {
  throw error;
};

var viewHike = function viewHike(message) {
  var match = /^view hike (.+)$/.exec(message);
  var hike = match && match[1];

  return !hike ? raise(new ValidationError(message)) : hike.includes('lost') ? raise(new NotFound(hike)) : 'Details about <' + hike + '>';
};

var NotFound = function (_Error) {
  _inherits(NotFound, _Error);

  function NotFound(message) {
    _classCallCheck(this, NotFound);

    var _this = _possibleConstructorReturn(this, (NotFound.__proto__ || Object.getPrototypeOf(NotFound)).call(this, message));

    _this.name = 'NotFound';
    return _this;
  }

  return NotFound;
}(Error);

var ValidationError = function (_Error2) {
  _inherits(ValidationError, _Error2);

  function ValidationError(message) {
    _classCallCheck(this, ValidationError);

    var _this2 = _possibleConstructorReturn(this, (ValidationError.__proto__ || Object.getPrototypeOf(ValidationError)).call(this, message));

    _this2.name = 'ValidationError';
    return _this2;
  }

  return ValidationError;
}(Error);

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

var safeViewHike = swallow(ValidationError)(function () {
  return 'Invalid format.';
})(swallow(NotFound)(function () {
  return 'No such hike.';
})(viewHike));

var chatbot = safeViewHike;

var PropertyRequiredError = function (_ValidationError) {
  _inherits(PropertyRequiredError, _ValidationError);

  function PropertyRequiredError(property) {
    _classCallCheck(this, PropertyRequiredError);

    var _this3 = _possibleConstructorReturn(this, (PropertyRequiredError.__proto__ || Object.getPrototypeOf(PropertyRequiredError)).call(this, "Not a property: " + property));

    _this3.name = "PropertyRequiredError";
    _this3.property = property;
    return _this3;
  }

  return PropertyRequiredError;
}(ValidationError);

var res = chatbot('view hike mirror lake');
// => 'Details about <mirror lake>'
console.log("rrrrr", res)

var res1 = chatbot('view hike lost lake');
// => 'No such hike.'
console.log("rrrrr1", res1)

var res2 = chatbot('show hike blue ridge');
// => 'Invalid format.'
console.log("rrrrr2", res2)
```


