### JS Utility Functions ###

#### Check if an element is visible in the viewport ####
> IntersectionObserver is a great way to check if an element is visible in the viewport.

```
var callback = function callback(entries) {
  entries.forEach(function (entry) {
    if (entry.isIntersecting) {
      // `entry.target` is the dom element
      console.log(entry.target.id + ' is visible');
    }
  });
};
var options = {
  threshold: 1.0
};
var observer = new IntersectionObserver(callback, options);
var btn = document.getElementById("btn");
var bottomBtn = document.getElementById("bottom-btn");
observer.observe(btn);
observer.observe(bottomBtn);
```
> You can customize the behavior of the observer using the option parameter. threshold is the most useful attribute, it defines the percentage of the element that needs to be visible in the viewport for the observer to trigger.

##### Deep copy an object with ease #####
> You can deep copy any object by converting it to a string and back to an object.

```
var deepCopy = function deepCopy(obj) {
  return JSON.parse(JSON.stringify(obj));
};
```

##### wait function #####
> JavaScript does ship with a setTimeout function, but it does not return a Promise object, making it hard to use in async functions. So we have to write our own wait/sleep function.

```
function _asyncToGenerator(fn) { return function () { var gen = fn.apply(this, arguments); return new Promise(function (resolve, reject) { function step(key, arg) { try { var info = gen[key](arg); var value = info.value; } catch (error) { reject(error); return; } if (info.done) { resolve(value); } else { return Promise.resolve(value).then(function (value) { return step("next", value); }, function (err) { return step("throw", err); }); } } return step("next"); }); }; }

var _marked = [type].map(regeneratorRuntime.mark);

var wait = function wait(ms) {
  return new Promise(function (resolve) {
    return setTimeout(resolve, ms);
  });
};

var asyncFunc = function () {
  var ref = _asyncToGenerator(regeneratorRuntime.mark(function _callee2() {
    return regeneratorRuntime.wrap(function _callee2$(_context3) {
      while (1) {
        switch (_context3.prev = _context3.next) {
          case 0:
            _context3.next = 2;
            return wait(1000);

          case 2:
            console.log("async");

          case 3:
          case 'end':
            return _context3.stop();
        }
      }
    }, _callee2, undefined);
  }));

  return function asyncFunc() {
    return ref.apply(this, arguments);
  };
}();

asyncFunc();

```
