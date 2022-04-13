
### How to Make a JavaScript Function Wait Until an Element Exists Before Running it? ###

> Sometimes, we want to wait until an element exists before running a JavaScript function.
#### Using the MutationObserver to Watch for Element to be Added to the DOM ####
> We can use the MutationObserver constructor available with modern browsers to watch for an element appearing. We can use the MutationObserver constructor to easily watch for changes to the DOM. We can use it to wait for an element to appear before running any code.


> For instance, if we have the following HTML:

```
<div id='container'>

</div>
```

> Then we can append a child element to it and watch it appear by writing:

```

const container = document.getElementById('container')
setTimeout(() => {
  const div = document.createElement('div')
  div.textContent = 'hello'
  div.id = 'hello'
  container.appendChild(div)
}, 2000)const observer = new MutationObserver((mutations, obs) => {
  const hello = document.getElementById('hello');
  if (hello) {
    console.log(hello.innerText)
    obs.disconnect();
    return;
  }
});observer.observe(document, {
  childList: true,
  subtree: true
});

```

> We call setTimeout with a callback that appends a div as the child of the div with ID container . 
> Then we invoke the MutationObserver constructor with a callback that has the mutation and obs parameters.
> obs is the returned observer.
> In the callback, we try to get the element with ID hello .
> If it exists, then we get the innerText property value of the element.
> And then we call disconnect stop watching for changes in the DOM.
> Then we call observer.observe to watch for changes in the document .
> childList is set to true to watch for adding or removing of the elements.
> subtree is also set to true to watch for child element changes.
