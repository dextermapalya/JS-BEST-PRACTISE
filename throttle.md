### Throttling in JS ###
> A throttle is a common technique used in the browser to improve an application’s performance by limiting the number of events your code needs to handle.  For example, waiting until a user stops typing to fetch typeahead search results. A throttle is best used when you want to handle all intermediate states but at a controlled rate

> Imagine you have a scroll event handler where you want to display new content to a user as they move down the page. If we executed the callback every time the user scrolls a single pixel, we would quickly get clogged with events if they rapidly scroll because it would send hundreds or thousands of events in quick succession. Instead, we throttle it so that we only check the amount scrolled every 100ms so that we’re getting only 10 callbacks per second. The response will still feel instantaneous to the user but be much more efficient computationally.

> Let’s look at an example. We begin by creating an HTML input element to let the user type the search query:
```
// Pass in the callback that we want to throttle and the delay between throttled events
const throttle = (callback, delay) => {
  // Create a closure around these variables.
  // They will be shared among all events handled by the throttle.
  let throttleTimeout = null;
  let storedEvent = null;

  // This is the function that will handle events and throttle callbacks when the throttle is active.
  const throttledEventHandler = event => {
    // Update the stored event every iteration
    storedEvent = event;

    // We execute the callback with our event if our throttle is not active
    const shouldHandleEvent = !throttleTimeout;

    // If there isn't a throttle active, we execute the callback and create a new throttle.
    if (shouldHandleEvent) {
      // Handle our event
      callback(storedEvent);

      // Since we have used our stored event, we null it out.
      storedEvent = null;

      // Create a new throttle by setting a timeout to prevent handling events during the delay.
      // Once the timeout finishes, we execute our throttle if we have a stored event.
      throttleTimeout = setTimeout(() => {
        // We immediately null out the throttleTimeout since the throttle time has expired.
        throttleTimeout = null;

        // If we have a stored event, recursively call this function.
        // The recursion is what allows us to run continusously while events are present.
        // If events stop coming in, our throttle will end. It will then execute immediately if a new event ever comes.
        if (storedEvent) {
          // Since our timeout finishes:
          // 1. This recursive call will execute `callback` immediately since throttleTimeout is now null
          // 2. It will restart the throttle timer, allowing us to repeat the throttle process
          throttledEventHandler(storedEvent);
        }
      }, delay);
    }
  };

  // Return our throttled event handler as a closure
  return throttledEventHandler;
};
```

#### How to use above function ####
```
const handler = config => e => {
        const { offset, color, text } = config;
        const top = window.pageYOffset;
        const div = document.createElement('div');
        div.style = `position: absolute; top: ${top +
          600}px; left: ${offset}%; color: ${color}; font-size: 32px`;
        div.innerText = text;
        document.body.appendChild(div);
};

const normalHandler = handler({ offset: 5, color: '#FF695E', text: 'Normal Event' });
      const throttledHandler = throttle(
        handler({ offset: 60, color: '#EED639', text: 'Throttled Event' }),
        1000,
);

window.addEventListener('scroll', normalHandler); //without throttle
window.addEventListener('scroll', throttledHandler); //with throttle

```      




