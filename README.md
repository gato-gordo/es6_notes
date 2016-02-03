Not an expert on this topic, just someone who has been experimenting with ES2015's features in 2015.  Welcome insights, tricks, or techniques from anyone who has them.  Just make a pull request to update this document.

What is ES2015?
===============
"ES2015" is short for "ECMAScript 2015".  It is the current standard for the JavaScript language, which browsers must implement to be standards compliant.

**What is ECMA?**

ECMA stands for "European Computer Manufacturers Association".  It's the standards body that maintains what offical ECMAScript/JavaScript is, which browsers then implement so that the same JavaScript code can work in any browser environment.

**'ES6' vs. 'ES2015'**

New standards of ECMAScript had in the past been rolled out with release numbers (ES3, ES5).   ES6 was set to be the newest relased, but the standards body has decided to aim at a new official standard every year.  So now we'll have ES2015, ES2016, etc.

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

1. Create directory and cd in
2. Run some commands: 
    `npm init`
    `npm install --save-dev babel-cli`
    `npm install --save-dev babel-preset-es2015`
    `echo '{ "presets": ["es2015"] }' > .babelrc`
    `./node_modules/.bin/babel --out-file destinationFile.js sourceFile.js`
    
Run the last command everytime you want to transpile. You could also alias the command to an npm script--e.g., "npm build";

Arrow Functions
===============
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters

/*

function add(...nums){
    return nums.reduce( (accum, val) => accum + val );
}

*/


//console.log(add(1, 2, 3, 4, 5));

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions

An arrow function expression (also known as fat arrow function) has a shorter syntax compared to function expressions and lexically binds the this value (does not bind its own this, arguments, super, or new.target). Arrow functions are always anonymous.

**No this binding**

// var Blinky = function(name, time){
//     this.time = time;
//     this.name = name;
//     this.step = function(){
//         setInterval( function() { 
//             console.log(this.name);
//         }, this.time);
//     };
// };

// var blink = new Blinky("Square", 500);
// blink.step();

var blink = new Blinky("Square", 500);
blink.step();

Syntactic Sugar
===============
Doing old things with less code: 

Examples + operator for strings:
        "Hello, " + "world!" is syntactic sugar for "Hello, ".concat("world!")
/*
function projectNums(projector){
    var nums = Array.prototype.slice.call(arguments, 1);
    return nums.map(projector);
}
function by2(num){
    return num * 2;
}
*/

Destructuring
=============
//var nums = [1, 2, 3, 4, 5];
//var a = nums[0]
//var b = nums[1];
//var args = nums.slice(2);

//var [a, b, ...args] = nums;
//var [a, , b]
//console.log(a, b, args);

//var me = {
//    name: "Ignacio",
//    profession: "Fellow"
//}

var name = me.name, profession = me.profession;

/var {name, profession} = me;
//console.log(name, profession);

var name = "Ignacio", profession "Fellow";
var me = {
    name: name
    profession: profession;
};

//var me = { name, profession };

Default Parameters
==================
function howDeep(){
    depth = depth || 0;
    console.log(depth);
}

howDeep(2);
howDeep();

// function howDeep(depth = 0){
//     console.log(depth);  
// }

Bindings
========
let
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let

// var nums = [0, 1, 2, 3, 4, 5];
// for(var i = 0; i < nums.length; i++){
//     var name = "Just using this in my for loop";
//     console.log(nums[i])
// }
// console.log(i)
// console.log(name);

const
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const
//const MAX_NUM = 25;
//MAX_NUM = 23;
//console.log(MAX_NUM);

//var num = 25;
//const MAX = num;
//num = 23;
//console.log(MAX);

// scopeFun();
// function scopeFun(){
//     var MAX = 20;
//     for(let i = 0; i < 5; i++){
//         let MAX = i;
//     }
//     return MAX;
// }


// const ME = {
//     name: "Ignacio",
//     profession: "Fellow"
// }

//ME = "Software Engineer";
//ME.profession = "Software Engineer";
//console.log(ME)

//really about signalling intent

...

Set
===
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set

// var fib = new Set(arry);
// fib.add(0);
// fib.add(1);
// fib.add(1);
// fib.add(2);
// fib.add(3);
// console.log(fib.has(3));
// console.log(fib.has(20));
// console.log(fib.delete(3));
// console.log(fib.delete(20));
// console.log(fib.has(3));
// console.log(fib.has(20));

// for(var num of fib){
//     console.log(num);
// }
// var arry = [0, 1, 1, 2, 3, 5, 8, 13];
Map
===
Key/value store: key can be any value, instead of just a string
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map

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

//Whaa?
for(var key in a){
    console.log(key);
}


Iterable Object
===============
An abstraction for a collection that knows how to go through its members in order.

Objects are not iterable!  Make no guarantee of order that you can go through.

[ks]: [https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch1.md#versioning]
[bab]:[https://babeljs.io/]
[wp]: [https://webpack.github.io/]
