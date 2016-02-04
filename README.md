Not an expert on this topic, just someone who has been experimenting with ES2015's features in 2015.  Welcome insights, tricks, or techniques from anyone who has them.  Just make a pull request to update this document.

What is ES2015?
===============
"ES2015" is short for "ECMAScript 2015".  It is the current standard for the JavaScript language, which browsers must implement to be standards compliant.

**What is ECMA?**

ECMA stands for "European Computer Manufacturers Association".  It's the standards body that maintains what offical ECMAScript/JavaScript is, which browsers then implement so that the same JavaScript code can work in any browser environment.

**'ES6' vs. 'ES2015'**

New standards of ECMAScript had in the past been rolled out with release numbers (ES3, ES5).   ES6 was set to be the newest released, but the standards body has decided to aim at a new official standard every year.  So now we'll have ES2015, ES2016, etc.

You can read Kyle Simpson's circa Feb 2015 thoughts on the naming of the standard, [here][ks] (that link goes to part of an entire book, available on GitHub, which he has written on the new JavaScript features).

**Currently Implemented Features By Browser**

https://kangax.github.io/compat-table/es6/

**Short Documentation for New Features**

http://es6-features.org/

**Longer Documentation for New Features**

For any feature of ES2015 (or really of any browser technology), Mozilla Developer Network will be your best resource for thorough documentation:

https://developer.mozilla.org/en-US/docs/Web/JavaScript

**CodeSchool ES2015 Class (requires Subscription)**

CodeSchool has a nice set of videos and challenges that work through ES2015 features:

https://www.codeschool.com/courses/es2015-the-shape-of-javascript-to-come/

Transpiling ES2015 to ES5
=========================
Because certain features of ES2015 are not universally implemented on different browsers, if you want to write ES2015 code right now that can work anywhere you will need to "transpile" your ES2015 code to ES5.

**What is transpiling?**

Transpiling is a subset of compiling: transforming source code from one language to another.  Traditional compilation moves from a higher to a lower level of abstraction (e.g., compiling C source code to binary). Transpiling is compiling source code to source code at a similar level of abstraction (e.g., Sass/Less, which are CSS pre-processors, to CSS).  To get ES2015 JavaScript to run in any modern browser environment, it needs to be transpiled to ES5 JavaScript.

**Transpiling ES2015 with Babel**

The main tool for transpiling ES2015 is [Babel][bab].  For actual production projects, you will want to transpile as part of a build process through something like [Browserfy][br] or [webpack][wp].  [Node Catapult][nc], a JavaScript application starter kit,  For experimentation purposes, you can manually transpile using the Babel command-line tools:

***Command-Line Babel Transpiling Recipe***

Babel can be installed as an NPM Module.  Unlike many command-line tools that you will want to install globally, you may want to have different versions of Babel running for different projects, so install it locally.

1. Create a directory, cd in, then run:
2. `npm init`
3. `npm install --save-dev babel-cli`
4. `npm install --save-dev babel-preset-es2015`
5. `echo '{ "presets": ["es2015"] }' > .babelrc`
6. `./node_modules/.bin/babel --out-file destinationFile.js sourceFile.js`
    
Run the last command everytime you want to transpile. You could also alias the command to an npm script--e.g., "npm build".

Syntactic Sugar
===============
A lot of ES2015 features are "syntactic sugar": ways to peform similar operations with cleaner code or less cumbersome syntax.  An example of syntactic sugar would be the `+` operator for concatenating strings: `"Hello, " + "world!"` is syntactic sugar for `"Hello, ".concat("world!")`.

Arrow Functions
===============
[Arrow Functions][af] are a new feature of JS that are one part syntactic sugar, one part new functionality.  As the MDN documentation says: 

>An arrow function expression (also known as fat arrow function) has a shorter syntax compared to function expressions and lexically binds the this value (does not bind its own this, arguments, super, or new.target). Arrow functions are always anonymous.

**Arrow functions as syntactic sugar**

As anonymous functions, arrow functions are most often useful in cases where you are declaring short callback functions.  Take a function that adds together all the values passed into it as arguments.  The ES5 way of doing this would be:

```javascript
function add(){
    var nums = Array.prototype.slice.call(arguments);
    return nums.reduce( function(accum, val) {
        return accum + val ;
    });
}

add(1, 2, 3, 4, 5) // output >> 15
```

Using arrow functions, we no longer need to use the `function` keyword in our callback.  We simply declare our function with the following syntax: `(params) => { //function body }`:

```javascript
function add(){
    var nums = Array.prototype.slice.call(arguments);
    return nums.reduce( (accum, val) => {
        return accum + val ;
    });
}
```

Furthermore, when our function body is only one-line long, we can omit the braces and the explicit return statement:

```javascript
function add(){
    var nums = Array.prototype.slice.call(arguments);
    return nums.reduce( (accum, val) => accum + val );
}
```

The final bit of syntactic sugar arrow functions provide is the ability to omit parentheses when our function only takes one argument:

```javascript
function multiplyBy2(){
    var nums = Array.prototype.slice.call(arguments);
    return nums.map( num => num * 2);
}

multiplyBy2(1, 2, 3, 4, 5) // output >> [2, 4, 6, 8, 10]
```

**No dynamic this binding**

The syntactic sugar arrow functions make available can provide a nice way for writing cleaner, easier-to-read callback functions.  They do have one functional difference from anonymous functions declared with the keyword `function`, however, which has to do with the binding of `this` inside of the function body.

In plain old JavaScript functions, `this` gets bound at call time. If we run the following code, our step function will log "undefined" to the console at every interval.

```javascript
    var NameLogger = function(name, time){
        this.time = time;
        this.name = name;
        this.step = function(){
            setInterval( function() { 
             console.log(this.name);
            }, this.time);
        };
    };
    var logger = new NameLogger("Ignacio", 500);
    logger.step(); 
    /*
    logs =>
    undefined
    undefined
    undefined...
    */
```

The reason that it logs "undefined" is that our callback function (a plain old JS function) is a "free function": it is not the method of an object.  When that callback function is run, `this` is bound to the global object, rather than the object that we created with the `NameLogger` constructor, which is likely what was intended by the use of `this`.

If an arrow function were substituted for our plain old JS callback function, we would get the result we want:

```javascript
    var NameLogger = function(name, time){
        this.time = time;
        this.name = name;
        this.step = function() {
            setInterval( () => { 
             console.log(this.name);
            }, this.time);
        };
    };
    var logger = new NameLogger("Ignacio", 500);
    logger.step(); 
    /*
    logs =>
    "Ignacio"
    "Ignacio"
    "Ignacio"...
    */
```

The reason for the difference in behavior between our arrow function and the plain old JS callback function is that arrow functions create no binding to `this` at call time.  If `this` appears in an arrow function body, it will be lexically bound.  So, when the constructor function is run, it will create a `this` binding to the object being constructed.  The arrow function creates no separate `this` binding, so occurrences of `this` inside of it will be bound to the same object.

Rest Parameters
=====================
[Rest parameters][rp] provide more syntactic sugar.  They are way of gathering an indefinite numbers of function arguments into an array.  The  
        
```javascript
function projectNums(projector){
    var nums = Array.prototype.slice.call(arguments, 1);
    return nums.map(projector);
}
function by2(num){
    return num * 2;
}
```

Destructuring
=============
```javascript
var nums = [1, 2, 3, 4, 5];
var a = nums[0]
var b = nums[1];
var args = nums.slice(2);

var [a, b, ...args] = nums;
var [a, , b];
console.log(a, b, args);

var me = {
    name: "Ignacio",
    profession: "Fellow"
}

var name = me.name, profession = me.profession;

var {name, profession} = me;
console.log(name, profession);

var name = "Ignacio", profession "Fellow";
var me = {
    name: name
    profession: profession;
};

var me = { name, profession };
```

Default Parameters
==================
```javascript
function howDeep(){
    depth = depth || 0;
    console.log(depth);
}

howDeep(2);
howDeep();

function howDeep(depth = 0){
  console.log(depth);  
}
```

Bindings
========
let
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let

```javascript
var nums = [0, 1, 2, 3, 4, 5];
for(var i = 0; i < nums.length; i++){
    var name = "Just using this in my for loop";
     console.log(nums[i])
}
console.log(i)
console.log(name);
```

const
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const
```javascript
const MAX_NUM = 25;
MAX_NUM = 23;
console.log(MAX_NUM);

var num = 25;
const MAX = num;
num = 23;
console.log(MAX);

scopeFun();
function scopeFun(){
     var MAX = 20;
     for(let i = 0; i < 5; i++){
         let MAX = i;
     }
     return MAX;
}


 const ME = {
     name: "Ignacio",
     profession: "Fellow"
 }

ME = "Software Engineer";
ME.profession = "Software Engineer";
console.log(ME)
```

Iterable Objects & For-Of
=========================
An iterable object is an abstraction for a collection that knows how to go through its members in order.

Plain old JS objects are not iterable!  They make no guarantee of how their keys will be ordered when you loop through them (might be insertion order, might be ordered through a Unicode comparison of strings, or it might be something else).

JavaScript does, however, provide a bunch of specialized built-in object types (Arrays, Sets, Maps) that are iterable, and ES2015 provides a special for loop, `for-of`, that can be used to loop through iterable objects in order.  For an array, it would work as follow:

```javascript
var nums = [0, 10, 20, 30, 40, 50];

for(var num of nums){
    console.log(num);
}
/*
outputs >>
0
10
20
30
40
50
*/
```

So, `for-of` works by declaring a variable to the left of the `of` that will be associated with each value in the iterable object, supplied to the right of the `of`.

Set
===
[Sets][sets] are one relatively simple type of iterable object.  They are basically a collection values of that enforces uniqueness for its members (the same value cannot be stored in the collection twice).

```javascript
var fib = new Set(arry);
fib.add(0);
fib.add(1);
fib.add(1);
fib.add(2);
fib.add(3);
console.log(fib.has(3));
console.log(fib.has(20));
console.log(fib.delete(3));
console.log(fib.delete(20));
console.log(fib.has(3));
console.log(fib.has(20));

for(var num of fib){
    console.log(num);
}
var arry = [0, 1, 1, 2, 3, 5, 8, 13];
```

Map
===
[Maps][map] are type of key/value store.  Unlike plain old JS objects, they are iterable (a Map will iterate through its members in insertion order).  The other distinguishing feature of Maps is that any kind of value can be a key (rather than simply string values, which are the only kind of key in plain old JS objects).

This can illustrated through a slightly artificial example:


```javascript
var a = {}
var b = {
    age: 21,
    profession: "baker"
}
var c = {
    age: 37,
    profession: "programmer"
}

a[b] = "bob";
a[c] = "christine";

console.log(a[b], a[c])

/*
outputs >>
"christine, "christine"
Whaa?  Let's see: */

for(var key in a){
    console.log(key);
}
/*

outputs >>
[object Object]
*/
```


Iterable Objects & Iterators
=============================

Iterable objects have been described at a high-level as collections that know how go through their values in order.  Under the hood, iterable objects are objects that know how to return a special object called an "Iterator".

An iterator is an object knows how to access members of a collection while keeping track of its current position.

Iterators are returned from the function stored at the Symbol.iterator key of an object.  Symbols are a new type of value in ES2105.  Explaining them is it's own rabbit hole.  For now, know that an iterable object returns an object that knows called iterator at the Symbol.iterator key:

```javascript
var iterable = {
    a: 1,
    b: 2,
    c: 3,
    d: 4,
    e: 5,
    Symbol.iterator: function(){ 
        //returns an iterator that knows how to through iterable in order 
    }
}
```

The iterator that's returned from iterable[Symbol.iterator] has a next method that returns 

[ks]: [https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch1.md#versioning]
[bab]:[https://babeljs.io/]
[wp]: [https://webpack.github.io/]
[af]: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions]
[sets]: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set]
[map]: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map]
[rp]: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters]
[br]: [http://browserify.org/]
