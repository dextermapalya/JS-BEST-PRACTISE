### Writing Efficient 'Big O' notation in JS ###

#### Worst method O(n) ####

> Worst-case scenario, this code is checking every IF statement .

```
// Don't ❌
function myFunction(myValue) {
  
    if(myValue == 1) {
      return doOneThing();
    }
    
    if(myValue == 3) {
      return doAnotherThing();
    }
    
    if(myValue == 4) {
      return doYetAnotherThing();
    }
    
    //default behavior
    return doTheDefaultThing();
    
    
  };
```
> This can be improved to O(1)  "worst-case scenario" it's first checking for the index's existence in 
logic and then calling doTheDefaultThing , that would be a Big O notation of O(2), which again, 
it's a constant number for potentially millions of calls, so we can safely disregard 
that 2 and call it an O(1)

```
// Do O(1) ✅
let logicBehavior = {
    1: doOneThing,
    3: doAnotherThing,
    4: doYetAnotherThing
  }
  
  function myFunction(myValue, logic) {
    try {
      logic[myValue]();
    } catch(e) {
      doTheDefaultThing();
    }
  }
```

