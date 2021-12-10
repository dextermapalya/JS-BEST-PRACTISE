### Simple Higher Order Functions ###


```
// Don't ❌
const extractNames = function(users) {
    const names = []
    for (let i = 0; i < users.length; i++) {
        if (users[i].name ) {
            names.push(users[i].name)
        }
    }
    return names;
};
```

```
// Do O(1) ✅
function makeApiCall(endpoint) {
  return function (data) {
    return fetch(endpoint, data);
  };
}

var fetchUsers = makeApiCall("/users");
var users = fetchUsers({ role: 'editor' });

```

