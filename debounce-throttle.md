### Debounce and Throttle for Rate limiting the function call ###

> Purpose of these two concepts are, 
> 1)Performance optimization
> 2) Rate limiting (Minimizing no. of function calls)

#### Debouncing ####
> Problem Statement. 
> Implement a Amazon search box where we can search products. How can we optimize as well as give good user experience.
> Our strategy is if the user keeps on typing continuously, anyway making API call for every keypress is inefficient right. Instead of that, we can make API request whenever the user pauses the typing.

```
var debounce = function(func,delay) {
    let timer = null
    return function(){
        let context = this;
        let args = arguments;
        clearTimeout(timer);
        timer = setTimeout(function(){
            func.apply(context,args);
        },delay);
    }
}

var getProducts = function(search){
  //API call will happen
}

var fetchProducts = debounce(getProducts,300);
```

> In the above code, getProducts function will call the API request, so we want to debounce this function.
> After creating debounce function we are passing getProducts function as an argument with interval time. So that it won’t call the function until the difference between two keypress event is greater than 300 milliseconds.
> Whenever a new keypress event occurs it erases the old pending API call which is enclosed with setTimeout function and creates a new setTimeout function with a new function call.

#### Throttling ####
> Problem Statement

> On every scroll event, we need to store the coordinates, and later when we come to the page we should automatically scroll to last stored coordinates.

> For this situation If we call the API call on every onScroll event, we will be calling function many times unnecessarily.
Our idea is for the first time we allow the function to store the coordinates, after that we should call another function only if the time difference between a previous function call and new function call exceeds more than the delay (ex: 300 milliseconds). So that when we scroll the page fast, even if we are about to make 100 requests, we are calling API very less time.
Let’s look at the solution

```
var throttling = function(func,delay){
    let flag = true;
    return function(){
        if(flag){
            let context = this;
            let args = arguments;
            flag = false;
            setTimeout(function(){
                func.apply(context,args);
                flag = true;
            },delay);
        }
    }
}

var onScroll = throttling(getData,300);
```

#### Difference between Debouncing and Throttling ####

> In Throttling we are initially allowing the function to trigger API request then later we are checking whether the previous time limit finishes or not

> But in Debouncing case we are clearing old-timer every time and creating a new timer. The function call will happen only if the time limit gets to finish.

