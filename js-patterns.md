### Design Patterns Categories ###

#### Creational Design Patterns ####
> This patterns deal with object creation on a more optimized way than the basic object creation. When the regular object creation manner would cause more complexity or bring problems to the code, creational patterns can solve the problem.

##### Constructor Pattern #####
> We define a constructor for Person
```
function Person(name, age, isDeveloper) {
    this.name = name;
    this.age = age;
    this.isDeveloper = isDeveloper || false;
}
 
// Then we extend the prototype, this way we make JavaScript
// point to this function when we call it on a Person
Person.protype.writesCode = function() {
        console.log(this.isDeveloper ? 'this person writes code' : 'this person does not writes code')
    }
}
 
// Create a person with: name = Ana, age = 32,
// isDeveloper = true and a method writesCode
let person1 = new Person('Ana', 32, true);
 
// Create a person with: name = Bob, age = 36,
// isDeveloper = false and a method writesCode
let person2 = new Person('Bob', 36);
 
// prints this person writes code
person1.writesCode();
// prints this person does not writes code
person2.writesCode();
```

##### Module Pattern #####
> Before we get into the details about module pattern, let’s take a better look at closures. A closure is a function with access to it’s parent’s scope, even after the parent has finished

```
// We use a immediately invoked function to create
// a private variable counter
var counterIncrementer = (() => {
    let counter = 0;
    return function() {
        return ++counter;
    };
})(); // these () in the end make this a immediately invoked function

// prints: 1
console.log(counterIncrementer());
// prints: 2
console.log(counterIncrementer());
// prints: 3
console.log(counterIncrementer());
```

> As you can see, we tied the variable counter to a function that was called and closed, but we still can access it trough the child function that increments counter. Note that the inner function is returned from counterIncrementer(). We can not access counter from outside the function, essentialy, we created a private variable in vanilla JavaScript through scope manipulation.
Using closures we can even create objects with public and private parts. This is very helpful when we want to hide some parts of an object and only present a nice interface for the module’s user. The example:

```
// Using a closure we will expose an object
// as part of a public API that manages its
// private parts
let fruitsCollection = (() => {
    // private
    let objects = [];
 
    // public
    return {
        addObject: (object) => {
            objects.push(object);
        },
        removeObject: (object) => {
            let index = objects.indexOf(object);
            if (index >= 0) {
                objects.splice(index, 1);
            }
        },
        getObjects: () => JSON.parse(JSON.stringify(objects))
    };
})(); // notice the execution
 
fruitsCollection.addObject("apple");
fruitsCollection.addObject("orange");
fruitsCollection.addObject("banana");
 
// prints: ["apple", "orange", "banana"]
console.log(fruitsCollection.getObjects());
 
fruitsCollection.removeObject("apple");
 
// prints: ["orange", "banana"]
console.log(fruitsCollection.getObjects());
```

> The greatest utility of this pattern is to make a clear separation between the public and private parts of an object.
Not everything is happiness, this pattern has some problems. When we want to change the visibility of a member, we must change that on every caller as well, because the access is different for public and private parts. Another problem is that methods added after the object is created can not access private methods (but we don't want to add new methods anyways).

##### Revealing Module Pattern #####

> This is an evolution of the module pattern described above. The main difference being that we write all object’s logic on the private scope and then expose what we want trough an anonymous object. We can also change the private member's names when we map then to the public ones.

```
// We write the whole logic as private members
// and expose an anonymous object that maps the
// methods we want as their public counterparts
let fruitsCollection = (() => {
    // private
    let objects = [];
 
    const addObject = (object) => {
        object.push(object);
    }
 
    const removeObject = (object) => {
        let index = objects.indexOf(object);
        if (index >= 0) {
            objects.splice(index, 1);
        }
    }
 
    const getObjects = () => JSON.parse(JSON.stringify(objects))
 
    // public
    return {
        addName: addObject,
        removeName: removeObject,
        getNames: getObjects
    };
})();
 
fruitsCollection.addName("Bob");
fruitsCollection.addName("Alice");
fruitsCollection.addName("Frank");
 
// prints: ["Bob", "Alice", "Frank"]
console.log(namesCollection.getNames());
 
namesCollection.removeName("Alice");
 
// prints: ["Bob", "Frank"]
console.log(namesCollection.getNames());
```

> The revealing module pattern is one of the ways we can implement a module pattern. The differences between the revealing module pattern and the other variants is mainly the way we reference the public modules. As a result, the revealing module pattern is a lot easier to be used and changed. It can still prove to be fragile in certain scenarios, when we will use the objects on a inheritance chain, for example. The most problematic situations are:

> If we have a private function referencing a public function, we can’t overwrite the public function. The private function will keep pointing private implementation and it will lead to bugs.

> When we have a public member pointing to a private member and we try to overwrite the public member from outside the module, all other functions will still be referencing the private value, introducing bugs.

##### Singleton Pattern #####

```
let configurationSingleton = (() => {
    // private value of the singleton initialized only once
    let config;
 
    const initializeConfiguration = (values) => {
        this.randomNumber = Math.random();
        values = values || {};
        this.number = values.number || 5;
        this.size = values.size || 10;
    }
 
    // We export the centralized method to return
    // the singleton's value
    return {
        getConfig: (values) => {
            // initialize the singleton only once
            if (config === undefined) {
                config = new initializeConfiguration(values);
            }
 
            // and always return the same value
            return config;
        }
    };
})();
 
const configObject = configurationSingleton.getConfig({ "size": 8 });
// prints number: 5, size: 8, randomNumber: someRandomDecimalValue
console.log(configObject);
 
const configObject1 = configurationSingleton.getConfig({ "number": 8 });
// prints number: 5, size: 8, randomNumber: same randomDecimalValue // como no primeiro config
console.log(configObject1);
``` 

##### Observer Pattern #####
> The observer pattern is very useful when we want to optimize the communication between separated parts of the system. It promotes an integration of the parts without making then too coupled.
> you will find several different ways to implement this pattern, but the simpler case is when we have one emitter and lots of observers.
The emitter will execute all the operations to which the observers are subscribed. Those operations can be, subscribe to a topic, unsubscribe from a topic and notify subscribers every time something is published to a topic.
> One variant to this pattern is the publisher/subscriber pattern, which is the one we will see on this text.

```
let publisherSubscriber = {};
 
// We pass an object to the container to manage subscriptions
((container) => {
    // the id represents a subscription to the topic
    let id = 0;
 
    // the objects will subscribe to a topic by
    // sending a callback to be executed when
    // the event is fired
    container.subscribe = (topic, f) => {
        if (!(topic in container)) {
            container[topic] = [];
        }
 
        container[topic].push({
            'id': ++id,
            'callback': f
        });
 
        return id;
    }
 
    // Every subscription has it's own id, we will
    // use it to remove the subscription
    container.unsubscribe = (topic, id) => {
        let subscribers = [];
        for (car subscriber of container[topic]) {
             if (subscriber.id !== id) {
                subscribers.push(subscriber);
            }
        }
        container[topic] = subscribers;
    }
    container.publish = (topic, data) => {
        for (var subscriber of container[topic]) {
            // when we execute a callback it is always
            // good to read the documentation to know which
            // arguments are passed by the object firing
            // the event
            subscriber.callback(data);
        }
    }
})(publisherSubscriber);
 
let subscriptionID1 = publisherSubscriber.subscribe("mouseClicked", (data) => {
    console.log("mouseClicked, data: " + JSON.stringify(data));
});
let subscriptionID2 = publisherSubscriber.subscribe("mouseHovered", function(data) {
    console.log("mouseHovered, data: " + JSON.stringify(data));
});
let subscriptionID1 = publisherSubscriber.subscribe("mouseClicked", function(data) {
    console.log("second mouseClicked, data: " + JSON.stringify(data));
});
 
// When we publish an event, all callbacks should
// be called and you will see three logs
publisherSubscriber.publish("mouseClicked", {"data": "data1"});
publisherSubscriber.publish("mouseHovered", {"data": "data2"});
 
// We unsubscribe an event
publisherSubscriber.unsubscribe("mouseClicked", subscriptionID3);
 
// now we have 2 logs
publisherSubscriber.publish("mouseClicked", {"data": "data1"});
publisherSubscriber.publish("mouseHovered", {"data": "data2"});

```
> This design pattern is specially useful when we want to respond to a single event on different places. Imagine you need to make several requests to an API and depending on the responses you will make other calls. You would have to nest several callbacks and this might be difficult to manage. Using the publisher/subscriber pattern you solve this situation in a much simpler way.
> The problem with this pattern are the tests. It might get hard to test the behavior of the publisher and listeners.

##### Prototype Pattern #####

> you must be getting tired of reading this, but JavaScript doesn’t support classes on its native form. Inheritance is made trough prototypes.
We can create objects that will be used as prototypes for other objects to be created. The prototype is a blueprint for objects that the constructor makes. Almost like we differentiate classes and objects in Java.

```
let personPrototype = {
    sayHi: () => {
        console.log("Hi, I am " + this.name + ", and I have " + this.age);
    },
    sayBye: () => {
        console.log("Bye!");
    }
};
const Person = (name, age) => {
    let name = name || "John Doe";
    let age = age || 26;
 
    const constructorFunction = (name, age) => {
        this.name = name;
        this.age = age;
    };
 
    constructorFunction.prototype = personPrototype;
 
    let instance = new constructorFunction(name, age);
    return instance;
}
 
const person1 = Person();
const person2 = Person("Jane Doe", 38);
 
// prints Hi, I am John Doe, and I have 26
person1.sayHi();
// prints Hi, I am Jane Doe, and I have 38
person2.sayHi();
```

##### Command Pattern #####

> The command pattern is used when we want to encapsulate a call as an object. It is a way to keep separated the caller's context from the called.
Let’s say your JavaScript application has several calls to an API. Imagine now that the API changes. We don't want to change every place that interacts with the API.
> That is where an abstraction layer to separate the objects that call the API from the objects that determine when to call it comes in. This way we don’t need to do a big change on the application, because we just have to change the call to the API on a single place.
> A problem that arises with this pattern is that it creates an additional abstraction layer, and it may impact the performance of an app. It is important to know how to balance performance and code legibility.

```
// The object that knows how to execute the command
const invoker = {
    add: (x, y) => {
        return x + y;
    },
    subtract: (x, y) => {
        return x - y;
    }
}
 
// the object to be used as abstraction layer when
// we execute commands; it represents a interface
// to the caller object
let manager = {
    execute: (name, args) => {
        if (name in invoker) {
            return invoker[name].apply(invoker, [].slice.call(arguments, 1));
        }
        return false;
    }
}
// prints 8
console.log(manager.execute("add", 3, 5));
// prints 2
console.log(manager.execute("subtract", 5, 3));
```

##### Facade Pattern ##### 

> The facade design pattern is used when we want to create an abstraction layer between what is show publicly and the internal implementation. It is used when we want to have a simpler interface.
> This pattern is used, for example, on the DOM selectors of libraries as JQuery, Dojo and D3. These frameworks have powerful selectors that allow us to write complex queries on a very simple way. 
> Something like jQuery(".parent .child div.span") seems simple, but it hides a complex query logic underneath.
