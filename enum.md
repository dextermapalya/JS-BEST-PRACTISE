### How to use ENUM properly in JS ###

> You may also notice that we have objects with cross values and keys. Meaning, for each key-value there is a mapping of value-key. This is value-key mapping is useful to cross-reference check:

```
const HTTP_RESPONSE_STATUS = Object.entries({
  OK: 200,
  CREATED: 201,
  MOVED: 301,
  BAD_REQUEST: 400,
  UNAUTHORIZED: 401,
  FORBIDDEN: 403,
}).reduce((m, [key, value]) => {
  m[key] = value;
  m[value] = key;
  return m;
}, {});

console.log(HTTP_RESPONSE_STATUS);
or to print or read a key
console.log(HTTP_RESPONSE_STATUS.OK)
console.log(HTTP_RESPONSE_STATUS.BAD_REQUEST)

```
