
### Useful JavaScript Front end Coding techniques ###


#### Scroll to the top of the page ####

```
const scrollToTop = () => {
  window.scrollTo({ top: 0, left: 0, behavior: "smooth" });
};
```
#### Scroll to the bottom of the page ####
> Of course, if you know the height of the document, you can also scroll smoothly to the bottom of the page.

```
const scrollToBottom = () => {
  window.scrollTo({
    top: document.documentElement.offsetHeight,
    left: 0,
    behavior: "smooth",
  });
};
```

#### Scroll elements to the visible area ####
```
const smoothScroll = (element) => {
  element.scrollIntoView({
    behavior: "smooth",
  });
};
```

#### Display the element in fullscreen mode ####
> You must have encountered such a scenario, where you need to play the video in full screen and open the page in full screen in the browser.

```
const goToFullScreen = (element) => {
  element = element || document.body;
  if (element.requestFullscreen) {
    element.requestFullscreen();
  } else if (element.mozRequestFullScreen) {
    element.mozRequestFullScreen();
  } else if (element.msRequestFullscreen) {
    element.msRequestFullscreen();
  } else if (element.webkitRequestFullscreen) {
    element.webkitRequestFullScreen();
  }
};
```
#### Exit browser full-screen state ####
>Yes, this is used with the fourth point, you will also have the scenario of exiting the full-screen state of the browser.
```
const goExitFullscreen = () => {
  if (document.exitFullscreen) {
    document.exitFullscreen();
  } else if (document.msExitFullscreen) {
    document.msExitFullscreen();
  } else if (document.mozCancelFullScreen) {
    document.mozCancelFullScreen();
  } else if (document.webkitExitFullscreen) {
    document.webkitExitFullscreen();
  }
};
```

#### Get the type of data ####
> How to get the data type of a variable by a function?
```
const getType = (value) => {
  const match = Object.prototype.toString.call(value).match(/ (\w+)]/)
  return match[1].toLocaleLowerCase()
}

getType() // undefined
getType({}}) // object
getType([]) // array
getType(1) // number
getType('fatfish') // string
getType(true) // boolean
getType(/fatfish/) // regexp
```

#### Stop bubbling events ####
> A method for preventing event bubbling that works on all platforms is necessary.
```
const stopPropagation = (event) => {
  event = event || window.event;
  if (event.stopPropagation) {
    event.stopPropagation();
  } else {
    event.cancelBubble = true;
  }
};
```




