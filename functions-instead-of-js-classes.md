### Use functions instead of classes ###

> As you probably guessed, the Car (line 2) and SportsCar (line 18) functions are constructor functions. The properties are defined using this keyword and the objects themselves are created with new. If you are not familiar with prototype, this is a special property that every JS object has to delegate common behavior. For example, the prototype for array objects has the functions you probably know well: map, forEach, find, etc. The prototype for strings has functions replace, substr, etc.

> After the Car object is created on line 33, you can access its properties and methods. The call to start on line 34 results in the following actions:

>   The JS engine asks the car object for a value with the key start.
    The object responds that it has no such value
    The JS engine asks the car.prototype object for a value with the key start.
    the car.prototype returns the start function, that the JS engine executes immediately.

> Accessing the make and model properties are performed similarly, except they are defined on the car object directly, instead of the prototype.

> Inheritance is a bit trickier. It is handled on lines 24–25. The most important function here is the Object.create function. It accepts an object and returns a brand new one, with its prototype set to whatever was passed as an argument. Now, if the JS engine does not find a value on the sportsCar object or sportsCar.prototype, it will consult sportsCar.prototype.prototype which is the prototype of the Car object.



```
// Do  ✅
// "class" declaration
function Car(make, model) {
  this.make = make;
  this.model = model;
}

// the start method
Car.prototype.start = function() {
  console.log('vroom');
}

// overriding the toString method
Car.prototype.toString = function() {
  console.log('Car - ' + this.make + ' - ' + this.model);
}

// inheritance example
function SportsCar(make, model, turbocharged) {
  Car.call(this, make, model);
  this.turbocharged = turbocharged;
}

// actual inheritance logic
SportsCar.prototype = Object.create(Car.prototype);
SportsCar.prototype.constructor = SportsCar;

// overriding the start method
SportsCar.prototype.start = function() {
  console.log('VROOOOM');
}

// Now testing the classes
var car = new Car('Nissan', 'Sunny');
car.start(); // vroom
console.log(car.make); // Nissan

var sportsCar = new SportsCar('Subaru', 'BRZ', true);
sportsCar.start(); // VROOOOM
console.log(car.turbocharged); // true

```

