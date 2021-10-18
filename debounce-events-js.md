### Debouncing in JS ###
> Sending a Request Only after the User Has Stopped Typing.  For example, waiting until a user stops typing to fetch typeahead search results. 

> Debouncing Example
> Let’s look at an example. We begin by creating an HTML input element to let the user type the search query:
```
<label for="searchInput">Search:</label>
<input type="search" id="searchInput">
```

> Following is our JavaScript code. We define a debounce function to control the amount of time our main function runs. This way, the function won’t send out a fetch request immediately; instead, it delays the execution by 300 milliseconds:

```
// debounce function
function debounce(callback, limit) {
  let timeout;
  return () => {
    clearTimeout(timeout);    
    timeout = setTimeout(() => {
      callback();
    }, limit);
  };
}
// main function
async function queryGoogle(e){
  // use your own API key in production
  const api = `https://www.googleapis.com/customsearch/v1?key=AIzaSyDDafor7FhfUGTvGpjVdE7Tomnt1Yg6XGg&cx=017576662512468239146:omuauf_lfve&q=${searchInput.value}`;
  
  const response = await fetch(api);
  
  // parse the body text as JSON
  const data = await response.json();
  
  // process the data
  console.log(data);
}
// pass our main function to the debounce function
// the second parameter defines the delay in milliseconds
const debouncedKeyPress = debounce(queryGoogle, 300);
const searchInput = document.getElementById('searchInput');
// call the debounced function when a key is released
searchInput.addEventListener('keyup', debouncedKeyPress);
```

#### Avoid repeated event firing ####
> Events that fire repeatedly such as scrolling, keypress, and mouse movement have the potential to negatively affect the browser performance— especially if your JavaScript code accomplishes taxing tasks. Make use of a debounce function to limit the rate at which your function can execute to improve the responsiveness of your program.


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

