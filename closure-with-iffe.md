### Closure with IFFE ###

#### Closures simplified ####
> A closure is just a function having access to the parent scope, even after 
the parent function has closed. Moreover, we use closures mainly for the 
implementation of encapsulation, iterators, and singleton in JavaScript. 
This was just a simple introduction to understanding the function closures, 
you need to learn more from other resources.



```
var add = (function () {

  var counter = 0;
  return function () {counter += 1; return counter}
})();

add();
add();
add();

// the counter is now 3
```

