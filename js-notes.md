# JavaScript Notes

## const

Variables defined with const cannot be redeclared or reassigned. They have block scope. They must be assigned a value when declared.

Always declare a variable with `const` when you know that the value should not be changed. e.g. a new array, object, function or regexp.

It actually defines a constant *reference* to a value. Because of this, you can't reassign a constant value, array or object. BUT, you can change the elements of a constant array or change the properties of a constant object.

So this is okay:

    const cars = ["Saab", "Volvo", "BMW"];
    cars[0] = "Toyota";
    cars.push("Audi");

This is not, you will get an error:

    const cars = ["Saab", "Volvo", "BMW"];
    cars = ["Toyota", "Volvo", "Audi"];

Similarly for objects, these are allowed:

    const car = {type:"Fiat", model:"500", color:"white"};
    car.color = "red";
    car.owner = "Johnson";

But reassigning the object would cause an error.

Variables declared with `const` are hoisted to the top, but NOT initialised. So, using a `const` variable before it is declared will result in a `ReferenceError`:

    alert(carName);
    const carName = "Volvo";

## Operators, operands, expressions
Operand: an entity on which an operator acts
Operator: used to compare values and perform operations on operands
Expression: a sequence of operators and operands that computes a value or designates an object or function

## <script></script>, async, defer
It's apparently an older practice to store scripts in the <head> section or after the <body> tags. It's hard to find definitive answers on why (a lot of contentious stackexchange threads), but at least best practice info is available.
### async (boolean attribute)
For classic scripts, if `async` attribute is present, the script will be fetched in parallel to parsing and evaluated as soon as it is available.
For module scripts, if `async` is there, then the script and all its dependencies will be executed in the defer queue - so, fetched parallel to parsing and evaluated as soon as it is available.
This allows the elimination of *parser-blocking JavaScript*, where the browser would have to load/evaluate scripts before continuing to parse.
In this case, `defer` has a similar effect.
See notes on **modules**.
### defer (boolean attribute)
Indicates that a script is meant to be executed after the document has been parsed, but before firing `DOMContentLoaded`.
Scripts with the `defer` attribute will prevent the `DOMContentLoaded` event from firing until the script has loaded and finished evaluating.
Scripts with the `defer` attribute execute in the order in which they appear in the document (top to bottom).
**note**: MUST NOT be used if the `src` attribute is absent (e.g. for inline scripts, as it will have no effect). Also, has no effect on module scripts as they defer by default.

## Shallow copy

https://developer.mozilla.org/en-US/docs/Glossary/Shallow_copy
An object whose properties share the same values as the source object. If you change properties of a shallow copy, you will alter the properties of the source as well.
As opposed to a deep copy, which is an independent copy of the source.

e.g. An `ingredients_list` array object is created, then `ingredients_list_copy` is created by copying that.

let ingredients_list = ["noodles", { list: ["eggs", "flour", "water"] }]; * SEE NOTE

let ingredients_list_copy = Array.from(ingredients_list);
console.log(JSON.stringify(ingredients_list_copy));

The output will be `["noodles",{"list":["eggs","flour","water"]}]`.

Changing the value of the `list` property in the copy will cause the same property to change in the original.

e.g. ingredients_list_copy[1].list = ["rice flour", "water"];
`console.log(ingredients_list[1].list);` will be `Array ["rice flour", "water"]`
`console.loh(JSON.stringify(ingredients_list));` will output `["noodles",{"list":["rice flour","water"]}]`

However, if you assign a new value to the first element in `ingredients_list_copy`, you won't change the first element in the source object.

NOTE: In `ingredients_list = ["noodles", { list: ["eggs", "flour", "water"] }];`, `ingredients_list` is an array. `ingredients_list[0]`, which is `"noodles"`, is an object/string. `ingredients_list[1]`, which is `{ list: ["eggs", "flour", "water"] }`, is an ARRAY, not a list, because of []. The {} denotes an object, and [] denotes an array. "list" is the "key" of an object.
A key is usually a string that connects to some kind of value

In this example:
let x = {
    'list': ["egg", "milk", "flour"],
    'cost': 10.45
}
This is an example of an object format.
x is an object. The comma separates the keys. 'list' and 'cost' are **key values**.
`console.log(x);` shows that x is an Object. `console.log(x.list);` returns the value associated with that key (which is a property of x), which is the array containing `["egg", "milk", "flour"]`.
You can also do `console.log(x["list"])`, which will work for both objects and JSONS. This is significant because with JSONS, you can't do `x.list` (apparently).

## Array.filter() 

If you have an array, you can use `.filter()` which is a method and also an iterator. 
e.g. `const result = words.filter(word => word.length > 6);`

By the way, the expanded version of the above arrow function is 

    const result = words.filter(function (word) {
        return word.length > 6;
    })

`word` is naming the variable that the filter() anonymous function uses to check the condition, basically like x.

`Array.map()` creates an array populated by the results of calling a provided function on every element in the calling array. It does not create a shallow copy like filter does.

## Functions

Local variables that share a name with outer variables: local is used first. Outer variables are only used if no local variable by that name exists. This is called shadowing (as in, the local variable *shadows* the outer one.)

Variables declared outside any function are called *global*. These are visible from any function, unless shadowed by locals.

It is good practice to minimize the use of global variables. Modern code has few or no globals - most reside in their functions. Sometimes though, they can be useful to store project-level data.

### Parameters

Pass arbitrary data to functions using parameters. In `function showMessage(from, text) { }`, `from` and `text` are parameters.

A function always get a copy of the value when called. 

    function showMessage(from, text) {
    from = '*' + from + '*'; // make "from" look nicer
    alert( from + ': ' + text );
    }

    let from = "Ann";

    showMessage(from, "Hello"); // *Ann*: Hello

    // the value of "from" is the same, the function modified a local copy
    alert( from ); // Ann
The value of `from` within showMessage is the same, regardless of the global `from`.

### Default values

If a function is called but an argument is not provided, the corresponding value becomes `undefined`.

From the above showMessage(), `showMessage("Ann");` would NOT be an error. Instead, it would output `*Ann*: undefined`, as no argument for text was passed.

You can specify a default value to use if an argument is omitted using =. As in, `function showMessage(from, text = "no text given") { }`. In this example, we've given a string default value, but you can use a more complex expression which is only evaluated andn assigned if the parameter is missing. So: `function showMessage(from, text = anotherFunction()) { }`. The result of `anotherFunction()` will then be the value of `text`, IF no argument for text is given.

In old Javascript, default parameters weren't supported, so they may have been given as `text = text || 'no text given';` or `if (text === undefined)`.

### Nullish Coalescing Operator ??

This is an operator, written as `??`. It treats `null` and `undefined` similarly. 

The result of `a ?? b` is: if a is defined, then a. If a isn't defined, then b. If b isn't defined, you'll have an error.

In the context of default values, you can have `function showCount(count) { alert(count ?? "unknown"); }`. So if `count` isn't given an argument, the alert will show "unknown".

### Returning a value

A function can return a value back into the calling code as the result. 
    function sum(a, b) {
        return a + b;
    }

    let result = sum(1, 2);
    alert (result); //3

The directive `return`  can be in any place of the function. When the execution reaches return, the function stops and the value is returned to the calling code. 
There can be many occurrences of return in a single function. For instance:
    function checkAge(age) {
        if (age >= 18) {
            return true;
        } else {
            return confirm('Do you have permission from your parents?');
        }
    }

A function with an empty return or without a return will return `undefined`.

To wrap a return across multiple lines, start it on the same line as return, or put the opening parentheses like:
    return (
        some + long + expression
        + or +
        whatever * f(a) + f(b)
    )

### Naming a function

Functions are actions, so their name is usually a verb that is brief and accurately describes what the function does.
It is common to start a function with a verbal prefix that vaguely describes the action.
e.g. functions that start with "show" usually show something. "get" returns a value, "calc" calculates something, "create" creates something, "check" checks something and returns a boolean.
With these prefixes, a glance at a function gives an understanding of what work it does and what value it returns.

### Functions == Comments

Functions **should be short and do exactly one thing**. If that thing is big, it may be worth it to split the function into a few smaller functions. Sometimes following this rule may not be easy, but it's definitely a good thing.

A separate function is not only easier to test and debug - its very existence is a great comment!

For instance, compare the two functions showPrimes(n). Both functions output prime numbers up to n.

The first variant uses a label:
    function showPrimes(n) {
        nextPrime: for (let i = 2; i < n; i ++) {

            for (let j = 2; j < i; j ++) )
                if (i % j == 0) continue nextPrime;
            }

        alert ( i ); // a prime)
        }
    }

The second variant uses an additional function isPrime(n) to test for primality:
    function showPrimes(n) {
        for (let i = 2; i < n; i++) {
            if (!isPrime(i)) continue;

            alert (i); // a prime
        }
    }

    function isPrime(n) {
        for (let i = 2; i < n; i++) {
            if (n % i == 0) return false;
        }
        return true;
    }

The readability of the second variant is significantly greater. Instead of the code, we see `isPrime` - this is referred to by some as *self-describing* code.

So, functions can be created even if we don't intend to reuse them. They structure the code and make it readable.

## Truthy and falsy values

A truthy value is a value that is considered true when encountered in a boolean context. A falsy value is a value that is considered false when encountered in a boolean context.

## Ternary operators vs. if statements

In `result = (a !== null && a !== undefined) ? a : b;`
? checks the condition before it . If it is true, then result = a. The : can be thought of as "else".
`(condition) (?/then) (a/true statement) (:/else) (b/false statement)`
`result = condition ? trueValue : falseValue`

### Ternary operators vs. if statements 
Ternary operators are *expressions*. They always evaluate to a value, as opposed to if statements, which are standalone units of execution. You can't assign the result of a statement to a variable, or pass the result of a statement as a function argument. If statements are useful for doing anything *besides* resolving to a value, e.g. network calls, reading/writing files, modifying DOM elements. These are called side effects.

Interesting article that defined the difference between ternary operators and if statements for me: https://jrsinclair.com/articles/2021/rethinking-the-javascript-ternary-operator/

The article states that it's better to focus on building expressions when coding. You can't escape using statements, obviously, but you need to be vigilant about causing side effects (reaching outside the block scope and changing the ennvironment).

If you were doing this:

    let result;
    if (someCondition) {
        result = calculationA();
    } else {
        result = calculationB();
    }

You might as well have used a ternary operator.

### Safer if statements
A rule suggested in the article: the first 'then' branch of an if statement must end with a `return`. That way even though the if statement doesn't resolve to a value, the outer function will. e.g.:

    if (condition) {
        return resultOfMyCalculation();
    }

This means you won't need an `else` block, so you can avoid unnecessary side effects.

### More readable ternaries, nested ternaries

    const ten     = Ratio.fromPair(10, 1);
    const maxYVal = Ratio.fromNumber(Math.max(...yValues));
    const minYVal = Ratio.fromNumber(Math.min(...yValues));

    // Create two functions so we only calculate the range we need.
    const rangeEmpty = maxYVal.minus(minYVal).isZero();
    const roundRange = () => ten.pow(maxYVal.minus(minYVal).floorLog10());
    const defaultRng = () => {
        const zeroRange  = maxYVal.isZero() ? Ratio.one : maxYVal;
        return ten.pow(maxYVal.plus(zeroRange).floorLog10());
    };

    // Piece together the final ternary using our two new functions.
    const yAxisRange = !rangeEmpty ? roundRange() : defaultRng();

In this example from the article, this is considered readable, even though the code itself is not as compact as it could be.

In this one:

    const xRangeInSecs = (Math.max(...xValues) - Math.min(...xValues));
    // prettier-ignore
    const xAxisScaleFactor =
        (xRangeInSecs <= 60)       ? 'seconds' :
        (xRangeInSecs <= 3600)     ? 'minutes' :
        (xRangeInSecs <= 86400)    ? 'hours'   :
        (xRangeInSecs <= 2592000)  ? 'days'    :
        (xRangeInSecs <= 31536000) ? 'months'  :
        /* otherwise */              'years';

    The use of vertical alignment makes nested ternary operators quite readable.

## Function expressions

    let sayHi = function() {
        alert("Hello");
    }

You can create a function in the middle of any expression. You don't have to provide a name for function expressions.

if you were to do `alert( sayHi );` you would see the function code. This is because the parentheses are missing (function doesn't run), and a function is a value. The code above shows its string representation.

You can copy a function to another variable:

    function sayHi() {
        alert("Hello");
    }

    let func = sayHi; // copy

    func(); // runs like sayHi()

Please note that sayHi has no parentheses when copied. This is so the source code of sayHi is copied, not the result of the call.

## Callback functions

    function ask(question, yes, no) {
        if (confirm(question)) yes ()
        else no();
    }

    function showOk() {
        alert("You agreed".);
    }

    function showCancel() {
        alert("You canceled the execution.");
    }

    // usage: functions showOk, showCancel are passed as arguments to ask
    ask("Do you agree?", showOk, showCancel);

In a real life example of this function, ask would use more complex ways to interact with the user than a simple confirm. The function would also draw a nice-looking question window.

The arguments `showOk` and `showCancel` in `ask` are called callback functions or just callbacks.

The idea is that a function is passed and we expect to "call it back" later if necessary. So showOk becomes the callback for "yes" and same for showCancel and "no".

Using function expressions:

    function ask(question, yes, no) {
        if (confirm(question)) yes()
        else no();
    }

    ask(
        "Do you agree?",
        function() { alert("You agreed."); },
        function() { alert("You canceled the execution."); }
    );

Here, anonymous functions are declared inside the ask call. They are not accessible outside of ask if done like this.

## Function Expression vs Function Declaration

Function declaration: a function declared as a separate statement, in the main code flow.

    function sum(a, b) {
        return a + b;
    }

Function expression: a function created inside an expression or inside another syntax construct. On the right side of the =.

    let sum = function (a, b) {
        return a + b;
    };

The more important difference is *when* a function is created by JS.

**A function expression is created when the execution reaches it and is usable only from that moment.** 

Meanwhile, **function declarations can be called earlier than they are defined**. e.g. a global function declaration is visible in the whole script, regardless of where it is.

When a script is initialized, global function declarations are searched for and created first. After all of them are processed, the code is executed.

### Block scope

Function declarations are only visible inside the code block they are in.

    let age = prompt("What is your age?", 18);

    // conditionally declare a function
    if (age < 18) {

        function welcome() {
            alert("Hello!");
    }

    } else {

        function welcome() {
            alert("Greetings!");
    }

    }

    // ...use it later
    welcome(); // Error: welcome is not defined

Here, welcome() is not accessible outside of that mini section of the function that it's in.

    let age = prompt("What is your age?", 18);

    let welcome;

    if (age < 18) {

        welcome = function() {
            alert("Hello!");
    };

    } else {

        welcome = function() {
            alert("Greetings!");
    };

    }

    welcome(); // ok now

Using a function expression to set welcome in either block is apparently correct. Also welcome the variable is global.

Function declarations give more freedom in code organisation due to **hoisting**, which is better for readability. It's easier to look up function f() vs let f = function().

But in cases where they aren't fit for the task, e.g. conditional declarations, function expressions work better.

## Arrow Functions

Better than function expressions in term of simple nd concise function syntax.

    let func = (arg1, arg2) => expression;

Expanded:

    let func = function(arg1, arg2) {
        return expression;
    };

If you only have one argument then you don't need () around the argument.

    let double = n => n * 2;

If you have no arguments you need empty ().

    let sayHi = () => alert("Hello!");

Use them to dynamically create functions:

    let age = prompt("What is your age?", 18);

    let welcome = (age < 18) ?
        () => alert("Hello!") :
        () => alert("Greetings!");

    welcome();

Multiline arrow functions:

    let sum = (a, b) => { 
        let result = a + b;
        return result; // if you use curly braces you need an explicit return
    };

    alert( sum(1, 2) ); //3

## Arraylike

An `arrayLike` is anything that looks like an array. It is iterable.

## Array.from
In this example:

    Array.from({length: 5}, (v, i) => i);
    // [0, 1, 2, 3, 4]

`(v, i) => i`

## JavaScript Modules

Oh boy, this looks big. I'll have to revisit this later
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules

Modern browsers have started to support module functionality natively (without frameworks/libraries). Use of native JS modules is dependent on the `import` and `export` statements.

## JavaScript Objects

A car is a real life **object** that has **properties** like weight and colour (car.name = Fiat), and **methods** like start and stop (car.start(), car.stop()).

All cars have the same properties, but the property **values* differ from car to car. All cars have the same methods, but the methods are performed at different times.

JS variables are containers for data values, `let car = Fiat` assigns a *simple value* to a *variable* named car. Objects are variables too, but can contain many values. `const car = {type:"Fiat", model:"500", color:"white"};` assigns many values (Fiat, 500, white) to a variable named car. 
The values are written as `name:value` pairs. It is common practice to declare objects with `const`.

You define an object with an object literal:

const person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"};

Object definition can span multiple lines.

Access object properties via `objectName.propertyName` or `objectName["propertyName"]`.

### Methods

A method is a function stored as a property. Below, `fullName` is a method.

const person =  {
    firstName: "John",
    lastName: "Doe",
    id: "5566";
    fullName: function() {
        return this.firstName + " " + this.lastName;
    }
};

You can access the method via `name = person.fullName();`. If accessed without the parentheses, it will return the function definition (the source code).

When a JavaScript variable is declared with the keyword `new`, it will be created as an object.

### this

`this` refers to an object. But what object depends on how `this` is invoked.

- In an object method, it refers to the object.
- Alone, it refers to the global object.
- In a function, it refers to the global object.
- In a function, in strict mode, `this` is `undefined`.
- In an event, it refers to the element that received the event.
- Methods like `call()`, `apply()`, `bind()` can refer `this` to any object.

## Object Properties

An object property name can be any valid JSD string or anything that *can be converted* to a string. But any property name that is not a valid JS identifier cannot use dot notation `object.property`. e.g. a property name that has a space or hyphen, starts with a number, or is held inside a variable. These can only be accessed via square bracket `[]` notation. Square brackets are also useful when property names are to be dynamically determined (not determined until runtime).

    // four variables created and assigned in a single go
    const myObj = {},
        str = 'myString',
        rand = Math.random(),
        anotherObj = {};
    
    // Now creating additional properties
    myObj.type = 'Dot syntax for a key named type';
    myObj['date created'] = 'This key has a space';
    myObj[str] = 'This key is in the variable named str';
    myObj[rand] = 'A random number from `rand` is the name of this key';
    myObj[anotherObj] = 'This key is object "anotherObj"';
    myObj[''] = 'This key is an empty string';

    console.log(myObj);
    console.log(myObj.myString);

In the above, all the keys that could not use dot notation used square brackets.
Please note that the console log does not print the keys in the order they were created.

JS object property names can only be strings or symbols - all keys in the square bracket notation are converted to strings unless they are Symbols. e.g. When the key `anotherObj` is added to `myObj`, JS calls the `toString()` method of anotherObj and uses the resulting string as the new key.

You can access a property with a string value stored in a variable. The variable must be passed in bracket notation. In the example above, `str` held `"myString"`, so `myString` was the property name. So `myObj.str` will return as undefined. `myObj[str]` will return `This key is in variable str`, as will `myObj.myString`.

### Multilevel objects

If you have

    const object = {
        one: {
            one,
            two,
            three
        },
        two: {
            one,
            two,
            three
        },
        three: {
            one,
            two,
            three
        }
    }

You can access object properties using `object.one`. BUT, you can access properties of nested objects by using their associated string names and `[]`. Objects are sometimes called associative arrays, since each property is associated with a string value that you can yse to access it.

So, for example, you can access the second property of the third property by `object["three"]["two"]`.

## Naming functions and variables

Always name variables and functions with camelCase.

Always try to start variable names with a noun or adjective (noun phrase). Single letter names are acceptable only within the context of a loop or callback function.

Always start function names with a verb.

    // good variables
    const numberOfThings = 10;
    const myName = "Nathan";
    let selected = true;

    // bad variables
    const getCount = 10; // function name
    const isSelected = true; // starts with verb

    // good function
    function getCount() {
        return numberOfThings;
    }

    // bad function (it's a noun)
    function myName() {
        return "Nathan";
    }

## do

Apparently this exists https://github.com/tc39/proposal-do-expressions

## Arrays

Arrays are a type of *collection*.

Declare arrays: `const array = [item, item...];` Using const is standard practice. Declare empty array: `const array = [];`.

You can have variables of **different types** in JS arrays, e.g objects, functions, arrays.

Be aware that arrays in JS use numbered indexes only. For named indexes, you will use an object.

### Array methods

The strength of arrays are the methods available, e.g. `array.length`: to access the first element of an array, use `array[0]`, and for the last, use `array[array.length - 1]`.

Use `for` loops to loop through an array, or, use `Array.forEach()`. e.g `fruits.forEach(myFunction)`.

Add array elements with `fruits.push("Lemon")`, OR using `length`. As in, `fruits[fruits.length] = "Lemon";`. Be careful when adding elements! If the index is higher than the length of the array, you might create "holes" where the value is `undefined`.

    const fruits = ["Banana", "Orange", "Apple"];
    fruits[6] = "Lemon";  // Creates undefined "holes" in fruits

To remove the last element, use `array.pop()`, which returns the value that was popped.

`array.toString()` converts an array to a string of comma separated array values.

`join()` joins all elements into a string but you can specify the separator, e.g. `fruits.join(" * ");` returns `Banana * Orange * Apple * Mango`.

`shift()` removes the first element and shifts all the others to one lower, then returns the value that was shifted out.

`unshift()` adds a new element to the start and pushes other elements up by 1.

Merge existing arrays using `concat()`, e.g. `const myChildren = myGirls.concat(myBoys, myOthers);`, which returns a new array.

`slice()` slices out the part of an array starting from the specified position.
    const fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"];
    const newFruits = fruits.slice(1);
    // Orange,Lemon,Apple,Mango

Passing 2 arguments in `slice(1,3)` will return elements from the first argument, up to (not including) the second.

`splice()` is used to add elements. In `splice(1, 1, "Lemon", "Kiwi")`, the first parameter defines the position where new elements should be added, the second determines how many are to be removed, and the rest of them define the new items to be added. It creates and returns a new array with the deleted items.

You can use splice to remove array elements midway without leaving holes, e.g. `array.splice(1, 1)` deletes only `array[1]`.

### Automatic toString()

When a simple value is expected, JavaScript automatically converts an array to a comma separated string. e.g. in `document.getElementById("demo").innerHTML = fruits;`.

## Loops
`for...of` loops through a collection:

    `for (const cat of cats) {
        console.log(cat);
    }

This example will print each element value.
UNLIKE `for...in`, which - in the above example - will print out the array index value instead `0 1 2 3 ...`.

`map()` executes a function on each array item, as in `cats.map(toUpper);`, and creates a new collection with the result.

`filter()` tests each item in a collection and returns a new collection containing only the items that match.

    function lCat(cat) {
        return cat.startsWith('L');
    }

    const cats = ['Leopard', 'Serval', 'Jaguar', 'Tiger', 'Caracal', 'Lion'];

    const filtered = cats.filter('lCat');

    console.log(filtered); // [ "Leopard", "Lion" ]

the difference between map and filter is that filter takes a function that will return a boolean. Anything that returns `true` will be returned.

The above with *function expressions* (just to be more compact):

    const cats = ['Leopard', 'Serval', 'Jaguar', 'Tiger', 'Caracal', 'Lion'];

    const filtered = cats.filter((cat)) =>
    cat.startsWith('L'));

Map and filter often use function expressions.

### for loop

If you don't have a collection of items to loop through, you just want to run the same code x times, you should use the `for` loop.

    for (initializer; condition; final-expression) {
        // code
    }

Initializer: variable set to number to count the number of times the loop has run. `i = 0`
Condition: when the loop should stop looping `i < 5`
Expression: Always evaluated/run each time the loop has gone through a full iteration. Used to increment/sometimes decrement the counter variable. `i++`

You can use a for loop to loop through a collection too. when looping through an array with this, don't forget that arrays start at 0 and you need to i `< array.length`, not `<=`. 

    for (let i = 0; i < cats.length; i++) {
        console.log(cats[i]);
    }

If you only want to do the same thing every time, `for...of` is probably less risky, but if you want to track loop progress, like knowing when you're on the last loop, you can use a `for` loop and an if statement.

    for (let i = 0; i < cats.length; i++) {
        if (i === cats.length - 1) {   // We are at the end of the array
            myFavoriteCats += `and ${cats[i]}.`
        } else {
            myFavoriteCats += `${cats[i]}, `
        }
    }

### Exit loops with break

If you want to exit a loop before all iterations are completed, e.g. searching through an array of contacts and return only the number you need, use `break` to move on to the next code.
See "String method: split" section for an example.

### Skip iterations with continue

Works similarly to break but it just skips to the next iteration of the loop. e.g. if you wanted to return which numbers up to x are square roots, use `continue`.
    for (let i = 1; i <= num; i++) {
        let sqRoot = Math.sqrt(i);
            if (Math.floor(sqRoot) !== sqRoot) {
                continue;
            }
        para.textContent += `${i} `;
    }

### while and do...while

I've heard the `while` loop can be a risky move.
    while (condition) {
        // code

        //final expression
    }
Unlike the `for` loop, the initialiser variable is set before the while loop, and the final expression (e.g. i++ in a for loop) is included inside the loop at the end.

    const cats = ['Pete', 'Biggles', 'Jasmine'];

    let myFavoriteCats = 'My cats are called ';

    let i = 0;

    while (i < cats.length) {
        if (i === cats.length - 1) {
            myFavoriteCats += `and ${cats[i]}.`;
        } else {
            myFavoriteCats += `${cats[i]}, `;
        }

        i++;
    }

    console.log(myFavoriteCats);     // "My cats are called Pete, Biggles, and Jasmine."

`do...while` is similar, but the structure varies:

    let i = 0;

    do {
        //code

        //final expression
    } while (condition)

The difference between these 2 loops are that the code inside a `do...while` loop is always executed at least once. This is because the condition comes *after* the code inside the loop. So it runs the code, then checks to see if it needs to be run again. In while and for loops, the code may never be executed as the check comes first.

**IMPORTANT NOTE ON INFINITE LOOPS**: With while and do...while loops (as with all loops) you must make sure that the initializer is incremented/decremented, so the condition eventually becomes false. Otherwise the loop will go on forever and will either be force stopped by the browser or crash.

## Strings with expressions

To use an expression in a string: `words are here, expressions are ${x}`
`${newResult}\n`
para.textContent = `Countdown ${i}`

### String method: split

`split()` takes a pattern and divides a string into an ordered list of substrings by searching for the pattern, then returns an array where each element is each part of the string split by the pattern.

    const input = document.querySelector('input');
    const searchName = input.value.toLowerCase();

    for (const contact of contacts) {
        const splitContact = contact.split(':');
        if (splitContact[0].toLowerCase() === searchName) {
            para.textContent = `${splitContact[0]}'s number is ${splitContact[1]}.`;
            break;
        }
    }

## Variadic functions

Function arguments are actually an array.

To have a function with a variable number of arrays, use rest parameters: `function(a, b, ...others) { }`. This is the preferred modern way of doing this.

## Set
Seems like array but easier to manipulate and faster?
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set

## Flow Control
This article has interesting notes on basic programming concepts (but in Ruby).
From: https://pine.fm/LearnToProgram/chap_06.html

Don't forget you can compare strings. `'cat' < 'dog'` returns true based on cat coming first alphabetically. Also, capital letters are considered to be "before" lowercase.

## For Each

`array.forEach();` takes a *callback* function as its argument.

e.g.: `notes.forEach( (note) => console.log(note) );`

    const myArry = [2, 3, 4, 2];
    myForEach(myArry, (item) => {
        console.log(item + 2); 

## Event Capture, Propagation, Bubbling

From https://youtu.be/F1anRyL37lE

**Bubbling** is when the browser records events not only for the event target, but for everything else that was included where the event happened. e.g. click on a nested div, you actually also clicked on that div PLUS any overlapping divs, the background, browser, OS etc.
So clicking on a div will ripple all the way to the top of the DOM and trigger any elements along the way.

If listening for a click on multiple nested elements, they will all be triggered.

When you click on a browser, the browser does a **capture**, from top down, to figure out everything you clicked on. Then it will do a bubble, working from the bottom up, to fire all associated events.

On `.addEventListener('click', logText, { capture: true });`, adding the options object (third parameter, default false) as capture: true makes the browser runs the function on the capture, instead of on the bubble.

**Propagation**
  function logText(e) {
    e.stopPropagation();
  }
In this example, running stopPropagation essentially stops the browser from bubbling. So clicking on a nested div will run a function on that div alone.
If the eventListener has capture: true, then the browser will only run on the topmost element with an eventListener, as it will reach that element and then not capture any further.
Likewise, if capture: false, the browser will stop bubbling after it reaches the lowest level element with an EventListener.

### Once

  divs.forEach(div => div.addEventListener('click', logText, { 
    capture: false,
    once: true
  }));

What `once` does is listen for a click and then **unbind itself**, as in `div.removeEventListener('click', logText);`. Then that function will not fire again.

This is used for situations like store checkouts, where you do not want someone to be able to spam click.

## Regular Expressions

In JS, regular expressions are also objects. These are used with the `exec()` and `test()` methods of RegExp (a JS object used for matching text with a pattern), and also with the `match()`, `matchAll()`, `replace()`, `replaceAll()`, `search()`, `split()` methods of String.

### Constructing a regular expression

**Regular expression literal**: a pattern enclosed in slashes. `const re = /ab+c/;` These provide compilation of the regular expression *when the script is loaded*.

**Calling the constructor function of the RegExp object**: `const re = new RegExp('ab+c');`. Calling the constructor function provides *runtime compilation* of the regular expression. So you should use this when you know the regular expression pattern will be changing, or you are getting it from another source, e.g. user input.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions

## AJAX

**Asynchronous JavaScript And XML**  is the use of `XMLHttpRequest` object to communicate with servers. It can send and receive information in various formats, incl. JSON, XML, HTML and txt. It's most appealing feature is that it is **asynchronous**, meaning it can communicate with the server and update the page without having to refresh the page.

- Make requests to the server without reloading
- Receive and work with data from the server

### Making a HTTP request

You need an instance of an object with the necessary functionality.

    const httpRequest = new XMLHttpRequest();

After making a request, you'll receive a response. Then you need to tell the XMLHttpRequest object which JS function will handle the response by setting the `onreadystagechange` property of the object to the function called when the request changes state:

    function handler() {
        // Process server response here
    }

    httpRequest.onreadystatechange = handler;

NOTE: there are no parentheses/parameters after the function name because you're *assigning a reference to the function*, rather than calling it.

You can also use an anonymous function for this.

Now, you need to actually make the request by calling `open()` and `send()`:

    httpRequest.open('GET', 'http://www.requestURL.com/random.file', true);
    httpRequest.send();

`.open()`:
- First parameter: the HTTP request method - GET, POST, HEAD, any other method supported by your server. All-capitals is the HTTP standard as not all browsers support lowercase.
- Second: the request URL - by default you cannot call URLs on 3rd party domains. Make sure to use the exact domain name on all your pages or you will get "permission denied".
- Third: sets whether the request is asynchronous. If `true`, JS execution continues and the user can interact with the page while the server response hasn't arrived yet. Default value true.

`.send()` parameter can be any data you want to send to the server if `POST`ing the request. Form data should be in a format the server can parse, like a query string:

    "name=value&anothername="+encodeURIComponent(myVar)+"&so=on"

or other formats, like multipart/form-data, JSON, XML, and so on.

If you want to `POST` data, you may need to set the MIME type of the request. e.g. use the following before calling `send()` for form data sent as a query string:

    httpRequest.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');

### Handling Server Response

The function handling the response needs to check the request's state. If the state has the value of XMLHttpRequest.DONE (corresponds to 4) the full response was received and you may proceed.

    if (httpRequest.readyState === XMLHttpRequest.DONE) {
        // all good
    } else {
        // not ready yet
    }

Full list of `readyState` values:
- 0 - uninitialized
- 1 - loading/server connection established
- 2 - loaded/request received
- 3 - interactive/processing request
- 4 - complete/request finished, response ready

Next, check the HTTP response status codes of the response. `if (httpRequest.status === 200)` although the value seems subject to change. Other status codes are 404 (Not Found), 500 (Internal Server Error).

After checking the state of the request and the HTTP status code, you can do what you want with the data. Two options for accessing data:

- `httpRequest.responseText` returns the server response as a string of text
- `httpRequest.responseXML` returns the response as an `XMLDocument` object that can be traversed with DOM functions

Please note that the steps above are only valid if an async request was used.

#### Possible Issues

If you send a request that returns XML, you must set response headers `Content-Type:application/xml` otherwise Internet Explorer will throw a JS `Object Expected` error.

If you do not set header `Cache-Control: no-cache` the browser will cache the response and never resubmit, which can make debugging an issue. You can also *bypass the cache* by adding an always-different GET parameter, e.g. a timestamp or random number.

If `httpRequest` variable is global, competing functions calling `makeRequest()` can overwrite each other, causing a race condition. Declaring the `httpRequest` variable local to a **closure** containing the AJAX functions avoids this.

If a communication error occurs (e.g. server going down), an exception will be thrown in the  `onreadystatechange` method when accessing the response status. To mitigate this, you could try wrapping the `if else` in ` try catch` statement.

## Closure

A closure is created every time a function is created. It is a function bundled together with references to its surrounding state.

One example of this is wrapping otherwise global variables in an anonymous function `() => {}` that executes immediately, so that other JS scripts can't access these global variables.

