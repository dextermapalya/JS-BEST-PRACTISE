### 10 handy regex functions ###

#### Match a number only string ####

    - ^ means the start of a string
    - \d+ match one or more digit
    - $ means the end of a string

```
/^\d+$/
var regex = "/^\d+$/"
regex.test('123456')  // true
regex.test('123,4,56')  // false
regex.test('id123')  // false
```

#### Match a Decimal Number ####

    - \. match a single char .
    - ? means optional.
    - () means a group
    - (\.?\d)? is an optional group

```
var regex = /^\d+(\.?\d+)?$/
regex.test('0')   // true
regex.test('.1')  // false
regex.test('3.14')  // true
regex.test('123456')  // true
regex.test('12.')    //false
```

#### Alphanumeric Characters ####
> We often use this regex when detecting usernames and passwords.
```
/^[a-zA-Z0-9]+$/
```
    - [a-zA-Z0–9] matchs all letters and numbers

```
var regex = "/^[a-zA-Z0-9]+$/"
regex.test("bytefish")   //true
regex.test("2022")   // true
regex.test("hello world")   // false contains a space
regex.test("hello,world")   // false contains a comma
regex.test('')    // false

#### Allow Spaces ####
> If you want to allow space in the string, you can just write regex like this:
    - /^[a-zA-Z0-9\s]+$/
    - \s match white space.

```
var regex = '/^[a-zA-Z0-9\s]+$/'
regex.test('hello world')    true //contains space
regex.test('helloworld')    true //though space not present


#### Email ####

> There may be many formats of email addresses, and it is difficult for us to write a perfect regular expression that matches email addresses.
    - ^\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$

```
var regex = ^\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$
regex.test("xyz@gmail.com")   //true
regex.test("xy-z@gmail.com")   //true
regex.test("x.y.z@gmail.com")   //true
```

> Use case 2
    - ^\w+@[a-zA-Z_]+?\.[a-zA-Z]{2,3}$
> Simple email expression. Doesn’t allow numbers in the domain name and doesn’t allow for top-level domains that are less than 2 or more than 3 letters (which is fine until they allow more).

```
var regex = /^\w+@[a-zA-Z_]+?\.[a-zA-Z]{2,3}$/
regex.test("xyz@gmail.com")   //true
regex.test("xy-z@123gmail.com")   //false contains a number
regex.test("x.y.z@gmail.subdomain.com")   //false contains 2 dots
```

#### Password ####
    - Rule 1    ^[a-zA-Z]\w{8,20}$
    - Rule 2    /^(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9])(?=.*?[^\w\s]).{8,}$/

```
var regex = /^[a-zA-Z]\w{8,20}$/
regex.test("qwe123456")   // true
regex.test("qq23")   // false less than 8 digits
regex.test("1TSesw3")   // false starts with a digit
```
> Rule 2
    - /^(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9])(?=.*?[^\w\s]).{8,}$/
    - at least 8 characters
    - at least 1 numeric character
    - at least 1 lowercase letter
    - at least 1 uppercase letter
    - at least 1 special character

#### Username ####
> The alphanumeric string that may include _ and — having a length of 3 to 16 characters.
    - /^[a-zA-Z0-9_-]{3,16}$/

```
var regex = /^[a-zA-Z0-9_-]{3,16}$/
regex.test('jon_snow-2022')  //true
regex.test('jon snow')  //false contains a space

```

#### URL ####
> Check if a string is a URL
    - /https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#()?&//=]*)/

```
var regex = /https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#()?&//=]*)/
regex.test('https://medium.com/234?tag=js')  // true
```

#### IPv4  ####
    - ^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$

#### Dates ####
> Date Format YYYY-MM-dd using separator 
    - /([12]\d{3}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01]))/
```
var regex = /([12]\d{3}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01]))/
regex.test('2022-02-08')  //true
regex.test('1999-12-31')  //true
regex.test('99-12-31')  //false  4 digit years
regex.test('2022-02-33')  //false  date greater than 31
```

> Date Format dd-MM-YYYY using separators - or . or /
    - /^(?:(?:31(\/|-|\.)(?:0?[13578]|1[02]))\1|(?:(?:29|30)(\/|-|\.)(?:0?[1,3-9]|1[0-2])\2))(?:(?:1[6-9]|[2-9]\d)?\d{2})$|^(?:29(\/|-|\.)0?2\3(?:(?:(?:1[6-9]|[2-9]\d)?(?:0[48]|[2468][048]|[13579][26])|(?:(?:16|[2468][048]|[3579][26])00))))$|^(?:0?[1-9]|1\d|2[0-8])(\/|-|\.)(?:(?:0?[1-9])|(?:1[0-2]))\4(?:(?:1[6-9]|[2-9]\d)?\d{2})$/

```
var regex = /^(?:(?:31(\/|-|\.)(?:0?[13578]|1[02]))\1|(?:(?:29|30)(\/|-|\.)(?:0?[1,3-9]|1[0-2])\2))(?:(?:1[6-9]|[2-9]\d)?\d{2})$|^(?:29(\/|-|\.)0?2\3(?:(?:(?:1[6-9]|[2-9]\d)?(?:0[48]|[2468][048]|[13579][26])|(?:(?:16|[2468][048]|[3579][26])00))))$|^(?:0?[1-9]|1\d|2[0-8])(\/|-|\.)(?:(?:0?[1-9])|(?:1[0-2]))\4(?:(?:1[6-9]|[2-9]\d)?\d{2})$/

regex.test('08/02/2022')  //true    
regex.test('03.11.1999')  //true    
regex.test('03-11-1999')  //true    
regex.test('33/02/2022')  //false no date greater than 31    
regex.test('08-13-2022')  //false no month greater than 13    

```

#### Match HTML tags with attributes: ####
    - /<\/?[\w\s]*>|<.+[\W]>/

```
var regex = /<\/?[\w\s]*>|<.+[\W]>/
regex.test('<html>') //true
regex.test('<div class="container">') //true
regex.test('</div>') //true
regex.test('<ss/div>') //false
regex.test('</div') //false
```

