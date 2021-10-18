### Make Your Own Promise in JavaScript ###

#### 1. Define the constructor ####
> A closure is just a function having access to the parent scope, even after 
the parent function has closed. Moreover, we use closures mainly for the 
implementation of encapsulation, iterators, and singleton in JavaScript. 
This was just a simple introduction to understanding the function closures, 
you need to learn more from other resources.

```
class BasicPromise {
  constructor(mainCallback) {
    // invoke the callback with resolve and reject functions as params
    try {
      mainCallback(this.resolve, this.reject);
    } catch (err) {
      this.reject(err);
    }
  }

  resolve = (result) => {};

  reject = (result) => {};
}
```


#### 2. Create an instance of Promise ####

```
// Creating the BasicPromise
const basicPromise = new BasicPromise((resolve, reject) => {
  setTimeout(() => {
    if (new Date().valueOf() % 2) {
      // call resolve if even
      resolve('even');
    } else {
      // call reject if odd
      reject('odd');
    }
  }, 1000);
});
```
#### Lets enhance the BasicPromise to store the result and state. ####

```
class BasicPromise {
  constructor(mainCallback) {
    // initialize state
    this.state = 'pending';
    
    // invoke the callback with resolve and reject functions as params
    try {
      mainCallback(this.resolve, this.reject);
    } catch (err) {
      this.reject(err);
    }
  }
  
  resolve = (result) => {
    this.settle('fulfilled', result);
  }
  
  reject = (result) => {
    this.settle('rejected', result);
  }
  
  settle(state, result) {
    // promise can be settled only once
    if (this.state !== 'pending') {
      return;
    }

    // store state and the result
    this.state = state;
    this.result = result;
  }
}
```

#### 3. Handle method calls ####

```
class BasicPromise {
  constructor(mainCallback) {
    // initialize state
    this.state = 'pending';

    // initialize callback list
    this.callbackList = [];

    // invoke the callback with resolve and reject functions as params
    try {
      mainCallback(this.resolve, this.reject);
    } catch (err) {
      this.reject(err);
    }
  }

  resolve = (result) => {
    this.settle('fulfilled', result);
  };

  reject = (result) => {
    this.settle('rejected', result);
  };

  settle(state, result) {
    // promise can be settled only once
    if (this.state !== 'pending') {
      return;
    }

    // if the result is BasicPromise we need to wait for it to be settled
    if (result instanceof BasicPromise) {
      return result.then(this.resolve, this.reject);
    }

    // store state and the result
    this.state = state;
    this.result = result;

    // make calls to stored callbacks
    this.invokeCallbacks();
  }

  then(resolveCallback, rejectCallback) {
    return new BasicPromise((resolve, reject) => {
      this.callbackList.push((result, state) => {
        try {
          if (state === 'fulfilled') {
            resolveCallback ? resolve(resolveCallback(result)) : resolve(result);
          } else {
            rejectCallback ? resolve(rejectCallback(result)) : reject(result);
          }
        } catch (err) {
          reject(err);
        }
      });
      // immedieately call the callbacks if promise is already settled 
      if (this.state !== 'pending') {
        this.invokeCallbacks();
      }
    });
  }

  catch(rejectCallback) {
    // catch is just a then() without a resolveCallback
    return this.then(null, rejectCallback);
  }

  finally(finalCallback) {
    return new BasicPromise((resolve, reject) => {
      this.callbackList.push((result, state) => {
        try {
          finalCallback();
          state === 'fulfilled' ? resolve(result) : reject(result);
        } catch (err) {
          reject(err);
        }
      });
      // immedieately call the callbacks if promise is already settled
      if (this.state !== 'pending') {
        this.invokeCallbacks();
      }
    });
  }

  invokeCallbacks() {
    while (this.callbackList.length) {
      // take out one function at a time from the list and invoke
      const callback = this.callbackList.shift();
      callback(this.result, this.state);
    }
  }
}
```

#### 4. Consume the Promise ####
```
// Consuming the BasicPromise
basicPromise
  .then((result) => {
    console.log('resolved with', result);
    return result;
  })
  .catch((result) => {
    console.log('rejected with', result);
    return result;
  })
  .finally(() => console.log('finally'));
  ```
  