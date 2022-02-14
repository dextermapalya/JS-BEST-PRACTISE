### RxJS for handling memory intensive apps ###


```
// Don't ❌
var users = new Array(1 million records)
users.map(function(item) {
  //do some operation on this record
})
```

```
// Do  ✅
For example, if your array is really really big, then the above will cause a fair amount of memory pressure (because it is creating N observables for a really large N). Here is an alternative that uses interval to lazily walk the array:
var usernames = ['Sharon', 'Sue', 'Sally', 'Sally', 'Steve']
var delayedStream = rx.Observable
    .interval(7000)
    .take(usernames.length) // end the observable after it pulses N times
    .map(function (i) { return usernames[i]; 
    })
    .distinctUntilChanged()
    .subscribe(function(i) {
      console.log("delayedStream", i);
     }); 
This one will yield the next value from the array every 2 seconds until it has iterated over the entire array.
It picks a value from array using index, instead of iterating over every record in the array
```

> Or 
> //Solution 2
> Here is a reusable function to generate an interval stream for an array 
> Below solution does same operation as above but is reusable for any array and duration
```
 function yieldByInterval(items, time) {
      return rx.Observable.from(items).zip(
        rx.Observable.interval(time),
        function(item, index) { return "yieldBy Interval " + item; }
      );
    }
    
    // test
    yieldByInterval(['A', 'B', 'C', 'C', 'D', 'E', 'F', 'G', 'H', 'I'], 5000)
      .distinctUntilChanged()
      .subscribe(console.log.bind(console));
```
