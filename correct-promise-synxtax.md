### CORRECT PROMISE SYNTAX FOR DAILY USE ###

#### Promises rule JavaScript ####
> Even nowadays, with introduction of async/await, they are still an obligatory knowledge for any JS developer.

#### Promise Mistake ####
> Whenever you use a promise and link the response to a function call:

```
fetch("url").then(function(res){
    processResponse(res);
});
```

#### Seems legit so far and also pretty common, right? ####
>In fact, there is a mistake in this piece of code. Examine the order of execution again.
>You access a remote URL.
>You wait for the response.
>You process the response.
>You call another function to process the response.
>In point 4 lies the problem. It is redundant. You already process the response.

#### The Solution ####
```
fetch("url").then(processResponse);
```
 > Working this way is called point-free or tacit. Its main feature is that you never specify the arguments for each function application.

 #### Be Aware When Working With Methods ####

 > A case where you should be in alarm mode is whenever you call an object’s method. If your original code looks similar to this:
 
```
fetch("url").then(function(data) {
    anObject.process(data);
});
```
> You can’t just transform the code to the following and expect it to work:
```
fetch("url").then(anObject.process);
```

#### The solution is to use .bind() ####
```
fetch("url").then(anObject.process.bind(anObject));
```
```
function doSome(data) {
    return anObject.process(data);
}
```
> Must be changed to:
```
const doSome = anObject.process.bind(anObject);
```

#### Syntax and programmatically correct but still wrong ####
```
const createdPromise = new Promise(resolve => {
  somePreviousPromise.then(result => {
    // do something with the result
    resolve(result);
  });
});
```

> The promise is wrapped within a new Promise making it slower

#### Fix for above problem #### 
```
const createdPromise = new Promise((resolve, reject) => {
  somePreviousPromise.then(result => {
    // do something with the result
    resolve(result);
  }).catch(reject);
});
```
> This catches a possible error
