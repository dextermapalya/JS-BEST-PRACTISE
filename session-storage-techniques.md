### Efficiently handle localStorage and sessionStorage ###

> LocalStorage and SessionStorage are great features that let us persist data in our web application. What is the difference between the two? SessionStorage data gets cleared when the page session ends.

#### Use cache mechanism to improve speed of storage ####

> Store only the minimal amount of data needed.
> Create your own abstraction for it. You can cache access to it

```
// dummy example do not use in prod
const cache = {};

export const getStorageValue = (key) => {
    if (cache[key]) {
        return cache[key];
    }
    
    const value = localStorage.getItem(key);
    return value === null ? value : JSON.parse(value);
};

export const setStorageValue = (key: string, value: unknown) => {
    cache[key] = value;
    localStorage.setItem(key, JSON.stringify(value));
};
```

