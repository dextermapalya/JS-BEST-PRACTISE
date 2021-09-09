### Writing Clean JavaScript — ES6 Edition ###

#### Use meaningful names ####

> The rule of thumb is that most JavaScript variables are in Camel Case (camelCase).

```
// Don't ❌
const foo = "JDoe@example.com";
const bar = "John";
const age = 23;
const qux = true;

// Do ✅
const email = "John@example.com";
const firstName = "John";
const age = 23;
const isActive = true
```

#### Avoid adding unnecessary contexts ####
 
> Do not add redundant context to variable names when the context is already provided by the containing object or class.

```
// Don't ❌
const user = {
  userId: "296e2589-7b33-400a-b762-007b730c8e6d",
  userEmail: "JDoe@example.com",
  userFirstName: "John",
  userLastName: "Doe",
  userAge: 23,
};

user.userId;

// Do ✅
const user = {
  id: "296e2589-7b33-400a-b762-007b730c8e6d",
  email: "JDoe@example.com",
  firstName: "John",
  lastName: "Doe",
  age: 23,
};

user.id;
```

#### Use descriptive names ####

```
// Don't ❌
function toggle() {
  // ...
}

function agreed(user) {
  // ...
}

// Do ✅
function toggleThemeSwitcher() {
  // ...
}

function didAgreeToAllTerms(user) {
  // ...
}
```

#### Use default arguments ####
```
// Don't ❌
function printAllFilesInDirectory(dir) {
  const directory = dir || "./";
  //   ...
}

// Do ✅
function printAllFilesInDirectory(dir = "./") {
  // ...
}
```

#### Limit the number of arguments ####
> As controversial as this rule might be, functions should have 0, 1, or 2 arguments. Having three arguments is already excessive, and beyond that implies either of two cases:
The function is already doing a lot and should be divided.
The data passed to the function is somehow related and can be passed as a dedicated data structure.

```
// Don't ❌
function sendPushNotification(title, message, image, isSilent, delayMs) {
  // ...
}

sendPushNotification("New Message", "...", "http://...", false, 1000);

// Do ✅
function sendPushNotification({ title, message, image, isSilent, delayMs }) {
  // ...
}

const notificationConfig = {
  title: "New Message",
  message: "...",
  image: "http://...",
  isSilent: false,
  delayMs: 1000,
};

sendPushNotification(notificationConfig);
```

#### Avoid executing multiple actions in a function ####
> A function should do one thing at a time. This rule helps reduce the function’s size and complexity, which results in easier testing, debugging, and refactoring. 

```
// Don't ❌
function pingUsers(users) {
  users.forEach((user) => {
    const userRecord = database.lookup(user);
    if (!userRecord.isActive()) {
      ping(user);
    }
  });
}

// Do ✅
function pingInactiveUsers(users) {
  users.filter(!isUserActive).forEach(ping);
}

function isUserActive(user) {
  const userRecord = database.lookup(user);
  return userRecord.isActive();
}
```

#### Avoid using flags as arguments ####

> A flag in one of the arguments effectively means the function can still be simplified.
```
// Don't ❌
function createFile(name, isPublic) {
  if (isPublic) {
    fs.create(`./public/${name}`);
  } else {
    fs.create(name);
  }
}

// Do ✅
function createFile(name) {
  fs.create(name);
}

function createPublicFile(name) {
  createFile(`./public/${name}`);
}
```

#### Do not repeat yourself (DRY) ####
> Duplicate code is never a good sign. If you repeat yourself you will have to update multiple places whenever there is a change in logic.

```
// Don't ❌
function renderCarsList(cars) {
  cars.forEach((car) => {
    const price = car.getPrice();
    const make = car.getMake();
    const brand = car.getBrand();
    const nbOfDoors = car.getNbOfDoors();

    render({ price, make, brand, nbOfDoors });
  });
}

function renderMotorcyclesList(motorcycles) {
  motorcycles.forEach((motorcycle) => {
    const price = motorcycle.getPrice();
    const make = motorcycle.getMake();
    const brand = motorcycle.getBrand();
    const seatHeight = motorcycle.getSeatHeight();

    render({ price, make, brand, seatHeight });
  });
}

// Do ✅
function renderVehiclesList(vehicles) {
  vehicles.forEach((vehicle) => {
    const price = vehicle.getPrice();
    const make = vehicle.getMake();
    const brand = vehicle.getBrand();

    const data = { price, make, brand };

    switch (vehicle.type) {
      case "car":
        data.nbOfDoors = vehicle.getNbOfDoors();
        break;
      case "motorcycle":
        data.seatHeight = vehicle.getSeatHeight();
        break;
    }

    render(data);
  });
}
```

#### Avoid side effects ####
> In JavaScript, you should favor functional over imperative patterns. In other words, keep functions pure unless needed otherwise. 
```
// Don't ❌
let date = "21-8-2021";

function splitIntoDayMonthYear() {
  date = date.split("-");
}

splitIntoDayMonthYear();

// Another function could be expecting date as a string
console.log(date); // ['21', '8', '2021'];

// Do ✅
function splitIntoDayMonthYear(date) {
  return date.split("-");
}

const date = "21-8-2021";
const newDate = splitIntoDayMonthYear(date);

// Original vlaue is intact
console.log(date); // '21-8-2021';
console.log(newDate); // ['21', '8', '2021'];
```
> Moreover, if a mutable value is passed to a function, you should return a new mutated clone of the value rather than mutating the value directly and returning it.

```
// Don't ❌
function enrollStudentInCourse(course, student) {
  course.push({ student, enrollmentDate: Date.now() });
}

// Do ✅
function enrollStudentInCourse(course, student) {
  var clone = Object.assign({}, course)
  clone.enrollmentData = Date.now()
  return clone
}
```

#### Use shorthands whenever possible ####
```
// Don't ❌
if (isActive === true) {
  // ...
}

if (firstName !== "" && firstName !== null && firstName !== undefined) {
  // ...
}

const isUserEligible = user.isVerified() && user.didSubscribe() ? true : false;

// Do ✅
if (isActive) {
  // ...
}

if (!!firstName) {
  // ...
}

const isUserEligible = user.isVerified() && user.didSubscribe();
```

#### Avoid branching and return soon ####
```
// Don't ❌
function addUserService(db, user) {
  if (!db) {
    if (!db.isConnected()) {
      if (!user) {
        return db.insert("users", user);
      } else {
        throw new Error("No user");
      }
    } else {
      throw new Error("No database connection");
    }
  } else {
    throw new Error("No database");
  }
}

// Do ✅
function addUserService(db, user) {
  if (!db) throw new Error("No database");
  if (!db.isConnected()) throw new Error("No database connection");
  if (!user) throw new Error("No user");

  return db.insert("users", user);
}
```

#### Favor object literals or maps over switch statements ####

> Whenever this applies, indexing using objects or maps will reduce code and improve performance.
```
// Don't ❌
const getColorByStatus = (status) => {
  switch (status) {
    case "success":
      return "green";
    case "failure":
      return "red";
    case "warning":
      return "yellow";
    case "loading":
    default:
      return "blue";
  }
};

// Do ✅
const statusColors = {
  success: "green",
  failure: "red",
  warning: "yellow",
  loading: "blue",
};

const getColorByStatus = (status) => statusColors[status] || "blue";
```

#### Use optional chaining and nullish coalescing ####
```
const user = {
  email: "JDoe@example.com",
  billing: {
    iban: "...",
    swift: "...",
    address: {
      street: "Some Street Name",
      state: "CA",
    },
  },
};

// Don't ❌
const email = (user && user.email) || "N/A";
const street =
  (user &&
    user.billing &&
    user.billing.address &&
    user.billing.address.street) ||
  "N/A";
const state =
  (user &&
    user.billing &&
    user.billing.address &&
    user.billing.address.state) ||
  "N/A";

// Do ✅
const email = user?.email ?? "N/A";
const street = user?.billing?.address?.street ?? "N/A";
const state = user?.billing?.address?.state ?? "N/A";
```

#### Concurrency ####
> Avoid callbacks, Callbacks are messy and result in nested code.   ES6 offers Promises which allow for chaining callbacks and thus result in cleaner code.

```
// Don't ❌
getUser(function (err, user) {
  getProfile(user, function (err, profile) {
    getAccount(profile, function (err, account) {
      getReports(account, function (err, reports) {
        sendStatistics(reports, function (err) {
          console.error(err);
        });
      });
    });
  });
});

// Do ✅
getUser()
  .then(getProfile)
  .then(getAccount)
  .then(getReports)
  .then(sendStatistics)
  .catch((err) => console.error(err));

// or using Async/Await ✅✅

async function sendUserStatistics() {
  try {
    const user = await getUser();
    const profile = await getProfile(user);
    const account = await getAccount(profile);
    const reports = await getReports(account);
    return sendStatistics(reports);
  } catch (e) {
    console.error(err);
  }
}
```

#### Error Handling ####
> Handle thrown errors and rejected promises

```
// Don't ❌
try {
  // Possible erronous code
} catch (e) {
  console.log(e);
}

// Do ✅
try {
  // Possible erronous code
} catch (e) {
  // Follow the most applicable (or all):
  // 1- More suitable than console.log
  console.error(e);

  // 2- Notify user if applicable
  alertUserOfError(e);

  // 3- Report to server
  reportErrorToServer(e);

  // 4- Use a custom error handler
  throw new CustomError(e);
}
```

#### Document when possible ####

```
**  
 * Returns x raised to the n-th power.  
 *  
 * @param {number} x The number to raise.  
 * @param {number} n The power, should be a natural number.  
 * @return {number} x raised to the n-th power.  
 */ 
function pow(x, n) {   
    // ...
}
```








