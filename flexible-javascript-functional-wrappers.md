### Flexible JavaScript Code With Pure Functional Wrappers ###

#### Flexible JavaScript Code With Pure Functional Wrappers ####
```
function yourFunction(arg1, arg2, arg3) {
    console.log("entering yourFunction: ", arg1, arg2, arg3);
    // do a little bit here
    // do a little bit there
    // and not too much over here
    const auxiliaryVar = your expression;
    console.log("exiting yourFunction: ", auxiliaryVar);
    return auxiliaryVar;
}
```
> Implement this procedure, and you will come up with this possible code as a solution:

### Sample Logger Code  ###
```
const loggingWrap = fn => (...args) => {
    console.log(`entering ${fn.name}: ${args})`);
    const returnValue = fn(...args);
    console.log(`exiting ${fn.name}: ${returnValue}`);
    return returnValue;
};
```
> The function returned by loggingWrap() has the following characteristics:
> console.log() shows the original function’s name and its arguments.
> The original function (fn()) gets called, and the returned value is stored.
> The secondconsole.log() line shows the function name and its returned value.
> The value that fn() calculated is returned.

##### Doomed by Exceptions #####
```
const loggingWrapCatcher = fn => (...args) => {
    console.log(`entering ${fn.name}: ${args}`);
    try {
        const returnValue = fn(...args);
        console.log(`exiting ${fn.name}: ${returnValue}`);
        return returnValue;
    } catch (error) {
        console.log(`exiting ${fn.name}: threw ${error}`);
        throw error;
    }
};
```

### Create a Pure Wrapper ###

```
const loggingWrapPure = (fn, loggingFn = console.log) => (...args) => {
    loggingFn(`entering ${fn.name}: ${args}`);
    try {
        const returnValue = fn(...args);
        loggingFn(`exiting ${fn.name}: ${returnValue}`);
        return returnValue;
    } catch (error) {
        loggingFn(`exiting ${fn.name}: threw ${error}`);
        throw error;
    }
};
```
