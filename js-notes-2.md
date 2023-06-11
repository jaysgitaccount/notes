# Advanced JS Notes

## Objects

It's best to use **object literal** syntax to declare objects:

    const myObject = {
        property: 'Value',
        otherProperty: 77,
        "property func": function() {
            // code
        }
    }

There are 2 ways to get data from an object: dot notation `myObject.property`, bracket notation `myObject["property func"]`.

Dot notation is cleaner and usually preferred, but doesn't work in every usage. `myObject."property func"` won't work because the property is a string with a space. You also can't use variables in dot notation as the browser will look for a property instead.

    const variable = 'property'

    myObject.variable // returns undefined

    myObject[variable] // equivalent to myObject['property'], will return 'Value'

### Objects as a Design Pattern

Grouping things into objects is a good way to organise code. e.g, from a tic tac toe game:

    // example one
    const playerOneName = "tim"
    const playerTwoName = "jenn"
    const playerOneMarker = "X"
    const playerTwoMarker = "O"

    // example two
    const playerOne = {
        name: "tim",
        marker: "X"
    }

    const playertwo = {
        name: "jenn",
        marker: "O"
    }

The benefits of the second approach outweigh the fewer line of the first. You can do:

    function printName(player) {
        console.log(player.name)
    }

Whereas you couldn't do this with the first eg. You'd have to remember the correct variable name and manually print it.

    function gameOver(winningPlayer){
        console.log("Congratulations!")
        console.log(winningPlayer.name + " is the winner!")
    }

You can scale code much better using objects.

### Object Constructors

When you need to create duplicate objects, e.g. the players above:

    function Player(name, marker) {
        this.name = name
        this.marker = marker
    }

When you call this with the keyword `new`,

    const player = new Player('steve', 'X')
    console.log(player.name) // 'steve'

Just like with objects created via Object Literal, you can add functions to the object.

    function Player(name, marker) {
        this.name = name
        this.marker = marker
        this.sayName = function() {
            console.log(name)
        }
    }

    const player1 = new Player('steve', 'X')
    player1.sayName() // logs 'steve'

If using constructors to make objects, **it's best to define functions on the prototype of each object**. That means a single instance of each function will be shared between all the inherited objects. If you declare the function directly in the constructor, that function would be duplicated every time a new Object is created, which will scale badly.

    function Student(name, grade) {
        this.name = name
        this.grade = grade
    }

    Student.prototype.sayName = function() {
        console.log(this.name)
    }
    Student.prototype.goToProm = function() {
        console.log("Eh.. go to prom?")
    }

By default, when creating a constructor function, e.g. `Student` here, you automatically create another object, the *prototype*.

## Prototype

### prototype property
All objects in JavaScript have a *prototype*. Basically, this is another object that the original object *inherits* from; the original object has access to all its prototype's methods and properties.

Every JS function has a prototype property, empty by default. You attach properties and methods on this prototype property when you want to implement inheritance, and these properties and methods are available to instances of that function. 

The prototype is NOT enumerable (can't be used in a for/in loop). Firefox/most versions of Safari/Chrome have a `__proto__` "pseudo" property (alternative syntax) that accesses an object's prototype property. You may never use this `__proto__` property, but you should know it exists and is simply a way to access an object's prototype property in some browsers.

Simple example:

    function PrintStuff (myDocuments) {
        this.documents = myDocuments;
    }

    // Add the print () method to the PrintStuff prototype property.
    PrintStuff.prototype.print = function () {
        return(this.documents);
    }

    var newObj = new PrintStuff ("I am a new Object and I can print.");

    // Now, newObj inherited all properties and methods, including print ().
    newObj.print (); // I am a new Object and I can print.

### prototype attribute
Think of the prototype attribute is a characteristic of the object, or the object's parent. This is the object it inherited its properties from. The prototype attribute is normally referred to as the *prototype object*, and it's automatically set when creating a new object.

**NOTE:** All objects have attributes, just like object properties have attributes. The object attributes are the *prototype*, *class*, and *extensible* attributes. Also, the `__proto__` pseudo property contains an object's prototype object (the parent).

### Constructor

A function used for initializing new objects, using the `new` keyword when calling it.

    function Account () {
        // le code here
    }

    let userAccount = new Account ();

All objects that inherit from another object also inherit a *constructor* property, a property that points to the constructor.

### When prototype is used

#### Prototype-based inheritance

JavaScript, unlike other object oriented languages, doesn't have classical inheritance based on classes. So you do inheritance via prototypes.

    function Plant() {
        this.country = "Mexico"
        this.isOrganic = true;
    }

    // Add method to plant()
    plant.prototype.showNameAndColor = function() {
        return "I am a " + this.name + " and my color is " + this.color;
    }

    // Add method to plant() 
    plant.prototype.amIOrganic = function() {
        if (this.isOrganic) {
            return "I am organic";
        }
    }
    
    function Fruit(fruitName, fruitColor) {
        this.name = fruitName;
        this.color = fruitColor;
    }

    // Set fruit's prototype to plant's constructor. Fruit inherits all plant.prototype
    // methods and properties
    fruit.prototype = new Plant();

    // Create Banana, an object with the fruit constructor
    let Banana = new fruit ('banana', 'yellow');

    // Banana has inherited Plant method.
    Banana.showNameAndColor(); // I am a banana and my color is yellow.

This is the main manner in which inheritance is implemented in JavaScript.

#### Prototype chain (accessing properties on objects)

The prototype attribute of any object is the parent object, where the inherited properties were defined. In a way, it works like family surname inheritance (namely, tracing it back up the family tree).

In JS, if you want to access a property of an object, the search for the property begins on the object itself. If the JS runtime can't find it there, it will look at the prototype. This continues (if not found, check the prototype of the prototype etc.), until there is no more prototype. Then, **undefined** is returned.

That's the *prototype chain*, which JS uses to look for properties and methods

All objects in JavaScript inherit properties and methods from Object.prototype. These inherited properties and methods are constructor, hasOwnProperty (), isPrototypeOf (), propertyIsEnumerable (), toLocaleString (), toString (), and valueOf (). ECMAScript 5 also adds 4 accessor methods to Object.prototype.

### Prototypal Inheritance

But a different (hopefully less poorly written) article this time

Sometimes you want to take something and extend it, e.g. have a `user` object, make `admin` and `guest` variants from it.

In JS, objects have a hidden property, `[[Prototype]]`, that is either `null` or references another object. If you try to assign a prototype in a circle, JS will throw an error. Objects can only inherit from ONE prototype.

Prototypes are read only. Writing/deleting operations work directly with the object, not any parent. If you assign a method that can be found in an object's prototype to the object itself, that function will always be called instead of the prototype's, and it won't affect the prototype's method at all.

*Accessor properties* are an exception, as assignment is handled by a *setter function*.  So, writing to an accessor property is the same as calling a function.

For that reason, `admin.fullName` works as expected below:

    let user = {
        name: "John",
        surname: "Smith",

        set fullName(value) {
            [this.name, this.surname] = value.split(" ");

        get fullName() {
            return `${this.name} ${this.surname}`;
        }
    }

    let admin = {
        this.prototype: user; // Idk if this works or not but original example uses __proto__
        isAdmin: true;
    };

    alert(admin.fullName); // John Smith (*)

    // setter is called
    admin.fullName = "Alice Cooper"; // (**)

    alert(admin.fullName); // Alice Cooper
    alert(user.fullName); // John Smith

`(*)` - the property `admin.fullName` has a *getter* in the user prototype, so it is called.

`(**)` - the property has a setter in the prototype, so it's called.

#### "this"

In the above example, there may be confusion about the value of `this` inside `set fullName(value)`; where are the properties `this.name` and `this.surname` written: user or admin?

The answer: `this` is not affected by prototypes at all. No matter where the method is found, object or prototype, in a method call, `this` is always the object before the dot.

So the setter in above used `admin` as `this`.

This is very important. When you have a big object with many methods, and objects that inherit from it, and the inherited objects run the inherited methods, they will modify only their own states, not the state of the big object.

    let animal = {
        walk() {
            if (!this.isSleeping) {
                alert('I walk');
            }
        },
        sleep() {
            this.isSleeping = true;
        }
    };

    let rabbit = {
        name: "White Rabbit",
        this.prototype: animal
    };

    // modifies rabbit.isSleeping
    rabbit.sleep();

    alert(rabbit.isSleeping); // true
    alert(animal.isSleeping); // undefined (no such property in the prototype)

As a result, methods are shared, but object state is not.

### for...in loop

The `for...in` loop iterates over inherited properties too.

For instance:

    let animal = {
        eats: true
    };

    let rabbit = {
        jumps: true,
        this.prototype: animal
    };

    // Object.keys only returns own keys
    alert(Object.keys(rabbit)); // jumps

    // for...in loops over both own and inherited keys
    for(let prop in rabbit) alert(prop); // jumps, then eats

If that's not what we want, and we want to exclude inherited properties, there's a built-in method `obj.hasOwnProperty(key)`: it returns `true` if `obj` has its own (not inherited) property named `key`.

Filtering out inherited properties (or, doing something else with them):

    let animal = {
        eats: true;
    }

    let rabbit = {
        jumps: true;
        this.prototype: animal;
    }

    for (let prop in rabbit) {
        let isOwn = rabbit.hasOwnProperty(prop);

        if (isOwn) {
            alert.(`Our: ${prop}`); // Our: jumps
        } else {
            alert(`Inherited: ${prop}`); // Inherited: eats
        }
    }

Reminder: we did not define `hasOwnProperty`; it was inherited from `Object.prototype`.
SO, why did it not show up in the `for...in` loop in the above example? This is because it is not enumerable, just like all other properties of `Object.prototype`. They all have `enumerable:false` flag, so they're excluded from loops.

**NOTE**: Almost all other key/value getting methods ignore inherited properties, e.g. `Object.keys`, `Object.values`, etc. They only operate on the object itself, so properties from the prototype are not taken into account.

### Why are both hamsters full?
This is an [exercise][https://javascript.info/task/hamster-proto] from [this page](https://javascript.info/prototype-inheritance). I decided to include my answer in my notes for posterity.

We have two hamsters: `speedy` and `lazy` inheriting from the general `hamster` object.

When we feed one of them, the other one is also full. Why? How can we fix it?

    let hamster = {
        stomach: [],

        eat(food) {
            this.stomach.push(food);
        }
    };

    let speedy = {
        __proto__: hamster
    };

    let lazy = {
        __proto__: hamster
    };

    // This one found the food
    speedy.eat("apple");
    alert( speedy.stomach ); // apple

    // This one also has it, why? fix please.
    alert( lazy.stomach ); // apple

Answer:
Why?
Because... `stomach` does not have `this.` in front of it, so the stomach array is stored on the shared `hamster` prototype, and there isn't one on each individual hamster object. So, `this.stomach.push` pushes apple to the prototype array (because it doesn't find any stomach on speedy), where lazy can also access it.
To fix it, change `stomach: []` to `this.stomach: []`.

Marking:
My explanation was correct, but to fix it, I was incorrect. I tried it in the console, you can't do `this.stomach: []`. It gives a SyntaxError.

    let hamster = {
        stomach: [],

        eat(food) {
            this.stomach = [food];
        }
    }

See above: it's important to note that this issue does NOT occur if you don't `push` and instead assign `this.stomach`. However it's not really a great scalable solution.

To actually fix it, you can make sure that each hamster has its own stomach.

    let hamster = {
        stomach: [],

        eat(food) {
            this.stomach.push(food);
        }
    };

    let speedy = {
        __proto__: hamster
        stomach: []
    };

    let lazy = {
        __proto__: hamster
        stomach: []
    };

**NOTE**: As a common solution, all properties that describe the **state** of a particular object, like `stomach` in the above, should be written into that object. That will avoid this issue.

## "this"
From [this article](https://dmitripavlutin.com/gentle-explanation-of-this-in-javascript/).

### Mysterious word

In other languages, `this` is the instance of the *current object* in the class method. It can't be used outside the method.

In JavaScript though... `this` is the context of a function *invocation*. 

JS has 4 function invocation types:
- function invocation `alert('Hello World!')`
- method invocation: `console.log('Hello World!')`
- constructor invocation: `new RegExp('\\d')`
- indirect invocation: `alert.call(undefined, 'Hello World!')`

Each invocation type defines the context differently, so `this` can be a bit wild. ALSO, [strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) (which I should learn about later) also changes the way `this` behaves.

To understand `this`, you should have a clear view of function invocation and how it impacts the context.

Terminology:
- **Invocation** of a function: executing the code that makes the body of a function; calling a function.
- **Context** of an invocation is the value of `this` within the function body.
- **Scope** of a function: the set of variables and functions accessible within a function body.

### Function Invocation
Performed when an expression that evaluates to a function is followed by `()`.

`parseInt('18')` is a function invocation.

`obj.myFunc()` is NOT. This is a *property accessor*, which creates a method invocation. e.g. `[1,5].join(',')` is a *method call*, not a function invocation.

IIFE (immediately-invoked function expression), a more advanced function invocation:

    const message = (function(name) {
        return 'Hello ' + name + '!';
    })('World');

The first pair of `()`, `(function(name) { ... })` is an expression that **evaluates to a function object**, followed by the `()` with the argument `'World'`.

#### "this" in function invocations
The *global object* is determined by the execution environment; in a browser, the global object is `window`. When using `this` outside any function scope (topmost scope/global execution context), you'll be referring to `window`.

In a function invocation, the *execution context* is the **global object**. So `this` will refer to `window`.

#### strict mode

Strict mode: available since ECMAScript 5.1; restricted variant of JS; better security and stronger error checking. You can enable strict mode `'use strict';` at the top of a function body, and it will affect all scopes within the function. Functions that are declared within execution scopes that have strict mode enabled will also inherit strict mode.

When enabled, `this` is `undefined` in a regular function invocation. The execution context is NOT the global object anymore.

#### Pitfall: "this" in inner function

`this` in an inner function is NOT the same as `this` in an outer function invocation!

The context of an inner function (except arrow functions) depends **only** on its own invocation type, not on the outer function's context.

To manipulate `this`, modify the inner function's context with indirect invocation (using `.call()` or `.apply()`) or create a bound function (`.bind()`).

    const numbers = {
        numberA: 5,
        numberB: 10,

        sum: function() {
            console.log(this === numbers); // => true

            function calculate() {
                // this = window or undefined in strict mode
                console.log(this === numbers); // => false
                return this.numberA + this.numberB;
            }

            return calculate();
        }
    };

In above, `numbers.sum()` is a *method invocation* on an object, thus `this` = `numbers`.

Although `calculate()` is defined inside sum, it is a *function evocation*, not a *method invocation*, so `this === numbers` returns `false` because `this` refers to the global context, where `this === window` (or, in strict mode, `undefined`).

Also, in strict mode, the result of invoking `numbers.sum()` is `NaN` or `TypeError`, because `this.` is used inside `calculate()`. To solve this issue, `calculate()` needs to execute with the same context as `numbers.sum()`.

One solution: manually change the context of `calculate()`  with `calculate.call(this)` (an indirect invocation of a function).

    const numbers = {
        numberA: 5,
        numberB: 10,

        sum: function() {
            console.log(this === numbers); // => true

            function calculate() {
                console.log(this === numbers); // => true
                return this.numberA + this.numberB;
            }

            // use .call() method to modify the context
            return calculate.call(this);
        }
    };
    numbers.sum(); // => 15

`calculate.call(this)` executes `calculate()` as usual, but modifies the context to a value specified as the first argument. So, now `calculate()` can access `numberA`/B.

Another slightly better solution is to use an arrrow function:

    const numbers = {
        numberA: 5,
        numberB: 10,

        sum: function() {
            console.log(this === numbers); // => true

            const calculate = () => {
                console.log(this === numbers); // => true
                return this.numberA + this.numberB;
            }

            // use .call() method to modify the context
            return calculate();
        }
    };
    numbers.sum(); // => 15

The arrow function resolves `this` *lexically* (uses `this` value of `numbers.sum()`).

### Method invocation

**method**: function stored in a property of an object.

    const myObject = {
        // helloMethod is a method
        helloMethod: function() {
            return 'Hello World!';
        }
    };
    const message = myObject.helloMethod();

To access `helloMethod`, you use a property accessor `myObject.helloMethod`.

This is **method invocation**, when an expression in the form of a property accessor (that evaluates to a function object) is followed by `()`.

Obviously, method invocation is different from function invocation, which is important.

    const words = ['Hello', 'World'];
    words.join(', ');   // method invocation

    const obj = {
        myMethod() {
            return new Date().toString();
        }
    };
    obj.myMethod();     // method invocation

    const func = obj.myMethod;
    func();             // function invocation
    parseFloat('16.6'); // function invocation
    isNaN(0);           // function invocation

See above example: `func()` is a function invocation, even though it includes a method invocation. See notes on "Pitfall: separating method from its object".

In method invocations `this` is the **object that owns the method**.

    const calc = {
        num: 0,
        increment() {
            console.log(this === calc); // => true
            this.num += 1;
            return this.num;
        }
    };
    // method invocation. this is calc
    calc.increment(); // => 1
    calc.increment(); // => 2

In above, in `calc.increment()`, `this.num` is set to the context of `calc`, so you can use `this` for that.

Also, as noted earlier, JS objects inherit methods from their prototype. But, when an inherited method is invoke on the object, the context is the object itself, not the prototype.

    const myDog = Object.create({
        sayName() {
            console.log(this === myDog); // => true
            return this.name;
        }
    });
    myDog.name = 'Milo';
    // method invocation. this is myDog
    myDog.sayName(); // => 'Milo'

`Object.create()` creates a new object, assigned to `myDog`, and sets its prototype from the first argument, the object `{ sayName() { return this.name; } }`. Obviously, `myDog.sayName()`'s context is myDog.

ECMAScript 2015 `class` syntax: the method invocation context is also the instance itself:

    class Planet {
        constructor(name) {
            this.name = name;
        }
        getName() {
            console.log(this === earth); // => true
            return this.name;
        }
    }
    const earth = new Planet('Earth');
    // method invocation. the context is earth
    earth.getName(); // => 'Earth'

#### Pitfall: separating method from its object
As seen in a previous example, `const func = obj.myMethod;`, calling `func()` will be a *function invocation*, NOT method invocation. So, when calling `func()`, `this` will actually be `window`.

If you don't want `this` to be global context, then create the separated variable with `const func = myobj.myMethod.bind(myObj)`. Adding `.bind(object)` fixes the context to the `object` that is passed, which is not necessarily the object that owns the method!

    function Pet(type, legs) {
        this.type = type;
        this.legs = legs;
        this.logInfo = function() {
            console.log(this === myCat); // => true if not passed as param, false if it is
            console.log(`The ${this.type} has ${this.legs} legs`);
        }
    }
    const myCat = new Pet('Cat', 4);
    // logs "The undefined has undefined legs"
    // or throws a TypeError in strict mode
    setTimeout(myCat.logInfo, 1000);

In the above example, `myCat.logInfo()` is separated from its object when passed into `setTimeout(myCat.logInfo, 1000);`. The following cases are equivalent:

    setTimeout(myCat.logInfo);
    // is equivalent to:
    const extractedLogInfo = myCat.logInfo;
    setTimeout(extractedLogInfo);

...which means that you've actually done a function invocation instead of a method invocation, so `this` is `window`, NOT `myCat`.

If you were instead to do 

    const myCat = new Pet('Cat', 4);

    const boundLogInfo = myCat.logInfo.bind(myCat); // now logs "The Cat has 4 legs"
    setTimeout(boundLogInfo, 1000);

Then it would work, as you've set the context correctly.

OR, again, use an **arrow function**, binding `this` lexically.

    function Pet(type, legs) {
        this.type = type;
        this.legs = legs;
        this.logInfo = () => {
            console.log(this === myCat); // => true
            console.log(`The ${this.type} has ${this.legs} legs`);
        };
    }
    const myCat = new Pet('Cat', 4);
    // logs "The Cat has 4 legs"
    setTimeout(myCat.logInfo, 1000);

If you want to use classes and bind `this` to the class instance in your method, use the arrow function as a *class property*.

    class Pet {
        constructor(type, legs) {
            this.type = legs;
            this.legs = legs;
        }

        logInfo = () => {
            console.log(this === myCat); // true
            console.log(`The ${this.type} has ${this.legs} legs`);
        }
    }
    const myCat = new Pet('Cat', 4);
    // logs "The Cat has 4 legs"
    setTimeout(myCat.logInfo, 1000);

### Constructor invocation

Occurs when the `new` keyword is followed by an expression that evaluates to a function object, then `()`. e.g: `new Pet('cat', 4)`

**NOTE**: if the constructor is called without arguments, then you don't need the parentheses, e.g. `const france = new Country`.

ECMAScript 2015: JS allows constructors using `class` syntax.

    class City {
        constructor(name, traveled) {
            this.name = name;
            this.traveled = false;
        }
        travel() {
            this.traveled = true;
        }
    }
    // Constructor invocation
    const paris = new City('Paris', false);
    paris.travel();

The object `paris` is initialised via a special class method, `constructor`, which assigns data using `this` as the newly created object.

Important: when a property accessor `myObject.myFunction` is preceded by `new`, JS performs a **constructor invocation**, and NOT a method invocation. How it works: in `new myObject.myFunction()`, the function is first extrtacted using the property accessor `extractedFunction = myObject.myFunction`. Then, it is invoked `new extractedFunction` to create a new object.


#### "this" in a constructor invocation

In a constructor invocation, `this` = the **newly created object**. This applies when using both `function` and `class` syntax, but with `class`, the initialisation happens in the `constructor` method.

#### Pitfall: forgetting about new

Some JS functions, e.g. `RegExp`, create instances not only when invoked as constructors, but also when invoked as functions.

    const reg1 = new RegExp('\\w+');
    const reg2 = RegExp('\\w+');

    reg1 instanceof RegExp; // => true
    reg2 instanceof RegExp; // => true
    reg1.source === reg2.source; // => true

As seen here, JS created equivalent regular expression objects.

Excluding [factory patterns](https://www.patterns.dev/posts/factory-pattern/), using function invocation to create objects may cause issues, as some constructors may omit the logic to initialize the object when `new` is missing.

    function Vehicle(type, wheelsCount) {
        this.type = type;
        this.wheelsCount = wheelsCount;
        return this;
    }
    // Function invocation
    const car = Vehicle('Car', 4);
    car.type; // => 'Car'
    car.wheelsCount // => 4
    car === window // => true

In this example, `car` has been created without using `new`. This is fine until you check the context of `this`, which is actually the global object. So what `const car = Vehicle('Car', 4);` did was actually set `type` and `wheelsCount` properties on the **window object** instead of creating a new object, which is a mistake.

Remember to use the `new` operator when you want to use a constructor call!

    function Vehicle(type, wheelsCount) {
        if (!(this instanceof Vehicle)) {
            throw Error('Error: Incorrect invocation');
        }
        this.type = type;
        this.wheelsCount = wheelsCount;
        return this;
    }
    // Constructor invocation
    const car = new Vehicle('Car', 4);
    car.type               // => 'Car'
    car.wheelsCount        // => 4
    car instanceof Vehicle // => true
    // Function invocation. Throws an error.
    const brokenCar = Vehicle('Broken Car', 3);

A verification `if (!(this instanceof Vehicle))` is added, to ensure that the execution context is the correct object type, so if you don't use `new`, it will throw an error.

### Indirect invocation

Performed when a function is called using `myFun.call()` or `myFun.apply()`.

Functions in JavaScript are first-class objects, which means that a function is an object. Functions inherit methods from their prototype, the `Function` object, including call and apply, which are used to invoke the function in a configurable context.

So, in `myFunction.call(thisArg, arg1, arg2, ...)`, `call()` accepts the first argument `thisArg` as the context of the invocation. Then, the rest of the arguments, `args...`, are passed as arguments to the called function. Examples:

    function sum(number1, number2) {
        return number1 + number2;
    }
    sum.call(undefined, 10, 2);    // => 12
    sum.apply(undefined, [10, 2]); // => 12

#### "this" in indirect invocations

`this` is the first argument of .call() or .apply() in an indirect invocation.

    const rabbit = { name: 'White Rabbit' };

    function concatName(string) {
    console.log(this === rabbit); // => true
    return string + this.name;
    }

    // Indirect invocations
    concatName.call(rabbit, 'Hello ');  // => 'Hello White Rabbit'
    concatName.apply(rabbit, ['Bye ']); // => 'Bye White Rabbit'

The indirect invocation is useful when a function needs to be executed with a *specific context*, like to solve the context issues with function invocation, or, as in the previous example, to simulate a method call on an object.

Another practical example: creating hierarchies of classes to call the parent constructor:

    function Runner(name) {
        console.log(this instanceof Rabbit); // => true
        this.name = name;
    }

    function Rabbit(name, countLegs) {
        console.log(this instanceof Rabbit); // => true
        // Indirect invocation. Call parent constructor.
        Runner.call(this, name);
        this.countLegs = countLegs;
    }

    const myRabbit = new Rabbit('White Rabbit', 4);
    myRabbit; // { name: 'White Rabbit', countLegs: 4 }

In above, `Runner.call(this, name)` inside `Rabbit`, makes an indirect call of the parent function to initialize the object.

### Bound function
A function whose context and arguments are bound to specific values. Created using `.bind()`. The original and bound functions share the same scope and code, but have different contexts and arguments on execution.

Unlike `.apply()` and `.call()`, which invoke the function right away, `.bind()` only returns a function to be invoked later with a predefined `this` value.

#### "this" inside a bound function
`this` is the first argument of myFunc.bind(thisArg, args...) when invoking a bound function.

#### Context binding

Bound functions have their context **permanently linked**. When using call or apply on a bound function, or a rebound, they won't have any effect.

The exception is when making a constructor call `new` on a bound function. But generally you won't want to do this, as constructor invocations require regular functions (not bound).

### Arrow function

Declare function in a shorter form and lexically bind the context.

    const hello = (name) => {
        return 'Hello ' + name;
    };

    hello('World'); // => 'Hello World'
    // Keep only even numbers
    [1, 2, 5, 6].filter(item => item % 2 === 0); // => [2, 6]

#### `this` in arrow functions

Arrow functions take `this` from the **outer function where it is defined** (resolves `this` lexically).

    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }
        log() {
            console.log(this === myPoint); // => true
            setTimeout(() => {
            console.log(this === myPoint);      // => true
            console.log(this.x + ':' + this.y); // => '95:165'
            }, 1000);
        }
    }
    const myPoint = new Point(95, 165);
    myPoint.log();

In this example, if we used a regular function inside `setTimeout()`, then that function's `this` would be the global object and you would have to bind it manually `setTimeout(function() {...}.bind(this))`. This is unnecessarily verbose, and using an arrow function is a cleaner solution.

**If an arrow function is defined in the topmost scope** (outside any function), the context is always the global object. An arrow function's `this` CANNOT be changed.

    const getContext = () => {
        console.log(this === window); // => true
        return this;
    };
    console.log(getContext() === window); // => true

Also, arrow functions will throw an error if used as a constructor.

#### Pitfall: defining method with an arrow function
Be aware of the context when defining a method.

    function Period (hours, minutes) { 
        this.hours = hours;
        this.minutes = minutes;
    }
    Period.prototype.format = () => {
        console.log(this === window); // => true
        return this.hours + ' hours and ' + this.minutes + ' minutes';
    };
    const walkPeriod = new Period(2, 30);
    walkPeriod.format(); // => 'undefined hours and undefined minutes'

In this example, `Period.prototype.format` is actually a topmost scope arrow function, so its `this` will be `window`. You'll have to use a **function expression** for this, as a regular function **does** change its context depending on invocation. (it's not a function invocation)

    Period.prototype.format = function() {
        console.log(this === walkPeriod); // => true
        return this.hours + ' hours and ' + this.minutes + ' minutes';
    };

### That was a lot, TL;DR

Function invocation has the biggest impact on `this`. 

Don't ask "where is `this` taken from?"

DO ask, "How is the function invoked?"

For arrow functions, ask "What is `this` within the outer function, where the arrow function is defined?"

## Prototypal Inheritance methods

At this point in time, the recommended way of setting the prototype of an object is `Object.create`. Simply, it returns a new object with the specified prototype and any additional properties you want to add.

    function Student()  {

    }

    Student.prototype.sayName = function() {
        console.log(this.name)
    }

    function EighthGrader(name) {
        this.name = name
        this.grade = 8
    }

    EighthGrader.prototype = Object.create(Student.prototype)

    const carl = new EightGrader("carl");

    carl.sayName() // "carl"
    carl.grade // 8

After creating the constructor for EighthGrader, we set its prototype to **a new object with the prototype** `Student.prototype`, i.e. an empty object with its `__proto__` pointing to `Student.prototype`. 

DON'T do this: `EighthGrader.prototype = Student.prototype` because it will set EighthGrader's prototype to `Student.prototype`, NOT a copy, which will cause problems editing in the future, as any changes you make to the object prototype will also unintentionally apply to any other objects that are inheriting from that prototype.

Instead, by doing `EighthGrader.prototype = Object.create(Student.prototype)`, you will create a copy of an object that inherits from `Student.prototype`. So any changes that you make to this object will remain unique to it.

### Exercises at the end of the page

function Pet(name, type) {
    this.name = name;
    this.type = type;
}

Pet.prototype.getInfo = function() {
    return `This is ${this.name}, a ${this.type}.`;
}

let Bobby = new Pet('Bobby', 'dog');
console.log(Bobby);

A prototype is an object that contains methods and properties that other objects inherit when they inherit from that prototype.

Prototypal inheritance is when an object's prototype points to an object, allowing the object to inherit the methods and properties available to the prototype. When a method or prototype is accessed on an object that does not explicitly have it defined, the engine will look for it on the prototype (and so on until there are no more prototypes).

DO:
Set prototypes using `object.prototype = Object.create(prototype)`
Be aware of the different ways that `this` can evaluate

DON'T:
Set prototypes in a circle, it won't work
Use `__proto__`
Set functions in the constructor of a prototype, or it will be duplicated every time

`Object.create()` creates a new object with a prototype specified.

`this`:
In function invocation, `this` = window/global object, or undefined in strict mode
Method invocation: `this` is the object that the method belongs to
Constructor: `this` is the new Object created by the constructor call
Indirect: when `.call(object)` or `.apply(object)` are used on a function, the first argument is the context that will be used for `this`
Bound: the first argument of `.bind(object)` is also used as `this`
Arrow function: `this` is the outer function that the arrow function was declared in. If declared outside a function, `this` will be the global object.

### Why declare object method using prototype?
Why not declare object methods in the constructor?

// Constructor function:
const Dog = function(name) {
    this.Name = name;
    this.Bark = function() {
        alert(this.Name + " bark");
    };
}

Instead of putting it on the prototype object?

    const Dog = function(name) {
        this.Name = name;
    }

    Dog.prototype.Bark = function() {
        alert(this.Name + " bark");
    };

[In general, it depends.](https://stackoverflow.com/questions/9772307/declaring-javascript-object-method-in-constructor-function-vs-in-prototype/9772864#9772864)

The main advantage of the constructor approach is that you can take advantage of the local scope to use variables defined within the constructor. These variables are otherwise "private", which means your API is cleaner than if these were instead defined as properties of the object.

- If your methods don't use local variables defined in the constructor, don't use that approach
- If you're going to be creating lots of `Dog`s, use the prototype approach instead, as that way they will all share one set of functions. If you do the constructor way, a new set of functions will be created each time the constructor is called, consuming more memory.

It may be worth it to use different approaches depending on the needs of the situation, e.g. if your methods require a local private constructor variable, declare it there.

Example given: local variable in the constructor tracks number of times dog has barked, while keeping the value private. `.wagTail()` doesn't use any private variables.

    const Dog = function(name) {
        this.name = name;

        var barkCount = 0;

        this.bark = function() {
            barkCount++;
            alert(this.name + " bark");
        };

        this.getBarkCount = function() {
            alert(this.name + " has barked " + barkCount + " times");
        };
    };

    Dog.prototype.wagTail = function() {
        alert(this.name + " wagging tail");
    };

    var dog = new Dog("Dave");
    dog.bark();
    dog.bark();
    dog.getBarkCount();
    dog.wagTail();

## Factory Functions & the Module Pattern

If you use an object constructor without the keyword `new`, you'll introduce an error, but it won't be easy to trace.

So instead of using constructors, you can use *factory functions*. Without using `new`, they set up an return the new object when called.

    const personFactory = (name, age) => {
        const sayHello = () => console.log('hello');
        return { name, age, sayHello };
    };

    const jeff = personFactory('jeff', 27);

### Object shorthand
Quick note about `return { name, age, sayHello };` - this is new syntax for creating objects from 2015. Without this, that line would look like `return {name: name, age: age, sayHello: sayHello};`

If you're creating an object where you are **referring to a variable that has the exact same name as the object property you're creating**, you can use `return { name, age, sayHello }`.

With that in mind, check this out:

    const name = "Maynard";
    const color = "red";
    const number = 34;
    const food = "rice";

    // logging all of these variables might be a useful thing to do,
    // but doing it like this can be somewhat confusing.
    console.log(name, color, number, food); // Maynard red 34 rice

    // if you simply turn them into an object with brackets,
    // the output is much easier to decipher:
    console.log({name, color, number, food});
    // { name: 'Maynard', color: 'red', number: 34, food: 'rice' }

## Scope and Closure

This is important sooooooo

In the following example, do you know what will be logged on the last line?

    let a = 17;

    const func = x => {
    let a = x;
    };

    func(99);

    console.log(a); // ???????

My answer: 17. Because a new `let a` is declared inside `func`. But it was declared within the scope of func, so it doesn't affect the a outside the function.

Edit code to print the other value:

    let a = 17;

    const func = x => {
        a = x;
    };

    func(99);

    console.log(a);

I removed the `let` so that the a within func is referring to the a outside the arrow function. Now it is assigning x to a instead of creating a new a.

### Global Scope
Not being careful with how you use global scope may cause *namespace clashes*.

Namespace - sometimes interchangeable word for scope, but usually refers to the highest level scope. e.g. when using jQuery, you access it in global scope. The jQuery namespace is defined in the global scope, which acts as a namespace for the jQuery library, as everything inside it becomes a descendent of that namespace.

### Local scope/function scope

Every function has its own (nested) local scope.

### Lexical scope

A function within another function has access to the scope in the outer function. That is *Lexical Scope* or *Closure*, also referred to as *Static Scope*.

### Scope Chain

JS checks the innermost scope, then searches outwards (up the scope chain).

### Closures

Ties closely with lexical scope. See the below function, which doesn't do anything when called normally, as it returns a function with a reference to a variable.

    let sayHello = function(name) {
        let text = 'Hello, ' + name;
        return function() {
            console.log(text);
        }
    }

The *closure* concept makes the scope inside `sayHello` inaccessible to the public scope.

Calling this function won't do anything as it returns a function, unless...

    sayHello('Todd'); // does nothing

    let helloTodd = sayHello('Todd');
    helloTodd(); // this works

    sayHello('Todd')(); // calls the returned function, without needing assignment

Apparently AngularJS uses the third method for its `$compile` method, where the current scope reference is passed into the closure:

    $compile(template)(scope);

So the code would (as a very simplified example) look like...

    var $compile = function(template) {
        // code
        return function(scope) {
            // template and scope and accessible here
        }
    }

### Not "this" again

So actually you can cache a reference to your preferred "this", I guess in a `that` variable, so you can refer to the lexical binding instead.

    let nav = document.querySelector('.nav'); // <nav class="nav">
    let toggleNav = function () {
        let that = this; // that = <nav> element
        setTimeout(function() {
            console.log(that); // <nav> element
        }, 1000);
    };
    nav.addEventListener('click', toggleNav, false);

In the context of the event listeners, `this` (outside of the function invocation) refers to the nav element, so capturing a reference to it allows you to easily access it within the setTimeout function invocation without issue (where `this` would otherwise refer to the global object).

### Private and Public Scope

In JS there is no such thing. You can only *emulate* private/public scope using things like closures. Using the JavaScript design pattern, e.g. the `Module` pattern.

A simple way to create private scope is wrapping functions inside functions. 

    (function () {
        var myFunction = function () {
            // do some stuff here
        };
    })();

myFunction(); // Uncaught ReferenceError: myFunction is not definedBUT, what if you want the function to be public?

Use the **Module Pattern** (and Revealing Module Pattern) to scope functions correctly, using private and public scope and an Object. In below, the global namespace, called `Module`, contains the relevant code for that module.

    // define module
    var Module = (function () {
        return {
            myMethod: function() {
                console.log('myMethod called');
            }
        };
    })();

    // To call the module + methods
    Module.myMethod();

The `return` statement is what returns the `public` methods, accessible in the global scope, but also `namespaced`. So the module takes care of the namespace, and can contain as many methods as you want.

You can extend Module as needed:

    // define module
    let Module = (
        function() {
            return {
                myMethod: function() {
                    // code
                },
                someOtherMethod: function() {
                    // other code
                }
            };
        }
    )();

    // call module + methods
    Module.myMethod();
    Module.someOtherMethod();

Also, `this` will work as if they are method invocations (because they are).

#### Private methods

There is no need to pollute the global namespace by putting all fuctions in the global scope. Functions that help our code work don't need to be global, only API calls (and other things that need to accessed globally). 

    let Module = (function() {
        let privateMethod = function() {
            // private code
        };
        return {
            publicMethod: function() {
                // public code
                // Has access to privateMethod even after returned!
            }
        }
    })();

With this, `publicMethod` can be called, and `privateMethod` can't.

**NOTE**: Returned (public) methods *can still access private methods*, so they can continue to interact while being inaccessible in the global scope!

Code security is hugely important in JavaScript, which is why you can't put all your functions in the global scope - they'll be vulnerable to attacks.

#### Naming convention for private methods

To differentiate between private and public methods, use `_` as a prefix.

    let Module = (function() {
        let _privateMethod = function() {
            // code
        }
        let publicMethod = function() {
            // code
        }
        return {
            publicMethod: publicMethod
        }
    })();

This helps when returning an anonymous Object, which the module can use in Object fashion, as we can simply assign the function references.

### How `let` and `const` are scoped in ES6

`var` - global scope. However, if declared within a function, it leaks onto the global scope.

#### Block Scope

When you see `{}` curly brackets, that's a block. Functions are also blocks. The difference between `let`/`const` and `var` is that `let` and `const` are limited to within that block (the closest set of curly brackets).

## Private Variables and Functions

Applying scope to the Factory Function...

    const FactoryFunction = string => {
        const capitalizeString = () => string.toUpperCase();
        const printString = () => console.log(`----${capitalizeString()}----`);
        return { printString };
    };

    const taco = FactoryFunction('taco');

    // Prints "----TACO----"
    taco.printString();
    
    // All the below return ERRORs
    printString();
    capitalizeString();
    taco.capitalizeString();

Because of scope, the only thing that can be accessed is the function that was returned. Note that even though we can't actively access `capitalizeString()`, `printString()` still can.

Functions retain their scope even if they are passed around and called outside their scope.

    const counterCreator = () => {
        let count = 0;
        return () => {
            console.log(count);
            count++;
        }
    }

    const counter = counterCreator();

    counter();

Because you can make private functions, even though your object does one or two things, you can split your functions up as much as you want for better readability. Then, you can only export the functions that the rest of the program will use.

You should use private functions as much as possible.

## Factory Functions and Inheritance

## var/let/const
Basically, `var` uses **function** scope. That is, it is available throughout any function it is declared within. If you declare a var inside an if statement, it will be accessible outside that if statement.
It CAN be redeclared within the same scope. It CAN be accessed before initialisation (will be `undefined` but won't throw an error), aka will be hoisted.

`let` uses **block scope**, CANNOT be redeclared within the same scope, and CANNOT be accessed before initialisation (will throw error) (won't be hoisted?)

`const` uses block scope, cannot be redeclared, cannot be reassigned at all (no `myConst = newValue`), cannot be accessed before initialisation/won't be hoisted.

### Function scope vs block scope
Function scope: when a variable is declared inside a function, it is only accessible within that function, and cannot be used outside that function. BUT, within that function, it is accessible anywhere.

    function hello() {
        console.log(a); // undefined, NOT error
        if (true) {
            var a = 'hello';
        }
        console.log(a); // hello
    }
You can't do this with `let` or `const`! If the above example is written with `let a` or `const a`, both console.logs will throw errors.

Block scope: A variable, when declared inside the if or switch conditions, or inside for/while loops, are accessible within that particular condition or loop, but NOT accessible outside that "block" (`{}`).

## Event loop
JS has a runtime model, based on an **event loop**, which is responsible for executing the code, collecting and processing events, and executing queued sub-tasks. These notes will be written from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop), where it's stated that these runtime concepts are theoretical. Modern JS engines implement and heavily optimise the described semantics.

An event loop has one or more task queues, which is a **set** of tasks. They are sets, NOT queues, because the event loop processing model grabs the first runnable task from the chosen queue, instead of dequeuing the first task.
- Task: a task is a struct which has:
    - steps: a series of steps specifying the work to be done by the task
    - a source: one of the task sources, used to group and serialize related tasks
    - a Document associated with the task, or null for tasks that are not in a window event loop
    - a script evaluation environment settings object set, a set of environment settings objects used for tracking script evaluation during the task.
- Runnable: A task is runnable if its document (DOM) is either `null` or fully active.
- Fully active: A Document `d` is fully active when `d` is the active document of a navigable navigable, and either navigable is a top-level traversable or navigable's container document is fully active.
- Navigable: presents a Document to the user via its active session history entry. [Further reading](https://html.spec.whatwg.org/multipage/document-sequences.html#navigable)
- Dequeue: to remove its first item and return it, if the queue is not empty, or to return nothing if it is.

### Stack
Function calls form a stack of **frames**. From below,

    function foo(b) {
        const a = 10;
        return a + b + 11;
    }

        function bar(x) {
        const y = 3;
        return foo(x * y);
    }

    const baz = bar(7); // assigns 42 to baz

The order of operations will be:
1. When calling `bar`, a first frame is created containing references to `bar`'s arguments, and local variables.
2. When `bar` calls `foo`, a second frame is created and pushed *on top of* the first one. It contains references to `foo`'s argument's and local variables.
3. When `foo` returns,  the top frame element is popped out of the stack (only `bar`'s call frame is left.)
4. When `bar` returns, the stack is now empty.
So, the stack can be visualised as each function call stacking frames on top of each other, then emptying top-down as they are resolved in sequence.
Note that the arguments and local variables may continue to exist, as they are stored outside the stack - so they can be accessed by any *nested functions* long after their outer function has returned.

[Further reading](https://www.javascripttutorial.net/javascript-call-stack/)

### Heap
Objects are allocated in a **heap**, which is a name to denote a large and mostly unstructured region of memory.

### Queue
A JS runtime uses a message queue, which is a list of messages to be processed. Each message has an associated function that is called to handle the message.

At some point during the event loop, the runtime starts handling the messages on the queue, starting with the **oldest** one. To do this, the message is removed from the queue and its corresponding function is called with the message as an input parameter. This will create a new stack frame.

The processing of functions continues until the stack is empty again. Then, the event loop will process the next message in the queue, if there is one.

### Event loop
The event loop got its name because of how it's usually implemented, which resembles:

    while (queue.waitForMessage()) {
        queue.processNextMessage();
    }
`queue.waitForMessage()` waits synchronously for a message to arrive (if one is not already available and waiting to be handled).

#### Run to completion
Each message is *processed completely* before any other message is processed.

When reasoning about your program, this offers some nice properties: the fact that whenever a function runs, it cannot be preempted and *will run entirely before any other code runs*. So, no other code can modify the data that the function manipulates mid-run. This is different from C, where a function running in a thread can be stopped by the runtime system to run other code in another thread.

The downside of this model is that if a message takes too long to complete, the web application will be unable to process user interactions (e.g. click or scroll) (in other words, the browser hangs). The browser will mitigate this with the "a script is taking too long to run" dialog.

A good practice to follow is to make message processing short, and if possible, cut down one message into several.

#### Adding messages
In browsers, messages are added **anytime an event occurs and there is an event listener attached to it**. (if there isn't a listener, the event is lost)

The function `setTimeout` is called with 2 arguments: a message to add to the queue, and a time value (optional, defaults to 0, in ms). The time value represents the *minimum delay* after which the message will be pushed into the queue. If there are other messages in the queue, it won't be processed immediately. Hence, think of setTimeout as "don't execute this message before *this many* ms", instead of "after *this many* ms, immediately execute this message".

#### Zero delays
*Zero delay* does not mean the callback fires after 0ms. The execution depends on the number of waiting tasks in the queue.

    (() => {

        console.log('this is the start');

        setTimeout(() => {
            console.log('Callback 1: this is a msg from call back');
        }); // has a default time value of 0

        console.log('this is just a message');

        setTimeout(() => {
            console.log('Callback 2: this is a msg from call back');
        }, 0);

        console.log('this is the end');

    })();

The above example will output

    // "this is the start"
    // "this is just a message"
    // "this is the end"
    // "Callback 1: this is a msg from call back"
    // "Callback 2: this is a msg from call back"

Because `setTimeout` needs to wait for ALL the code for queued messages to complete. Even though neither callback has a delay above 0, that is the *minimum* time to wait until the message is processed.

#### Several runtimes communicating together
A web worker or cross-origin `iframe` has its own stack, heap, and message queue. To distinct runtimes can only communicate through sending messages via the method `postMessage`. This method adds a message to the other runtime if it is listening to `message` events.

### Never blocking
An interesting property of the event loop model is that JavaScript, unlike a lot of other languages, **never blocks**. Handling I/O (input/output) is typically performed via events and callbacks, so while the application is waiting for an IndexedDB query to return or an XHR request to return, other things like user input can still be processed.

*Legacy exceptions do exist*, such as `alert` or synchronous XHR, but it's good practice to avoid them.

### Single-threaded model
JS is single threaded, which means it can do only one thing at any single point in time.

The JS engine executes a script from the top of the file and works its way down, starting with creating the execution contexts, then pushing/popping functions on and off the **call stack** in the execution phase. 

A function that takes a long time to complete is called a blocking function, as it technically blocks all user interactions on the webpage. e.g. a function that calls an API from a remote server, or a very big loop.

To prevent a blocking function from blocking other activities, you typically put it in a callback function for execution later. e.g

    console.log('Start script...');

    setTimeout(() => {
        task('Download a file.');
    }, 1000);

    console.log('Done!');

The above outputs:
    Start script...
    Done!
    Download a file.

Earlier we said the JS engine can only do one thing at a time. But it's more accurate to say that the JS runtime can do one thing at a time. The web browser has other components, not just the JS engine, so when you call `setTimeout`, make a fetch request, click a button etc. the browser can do these activities concurrently and asynchronously. 

Note that `setTimeout`, fetch requests and DOM events are part of the Web APIs of the web browser. So...

In the example script above, when calling `setTimeout`, the JS engine places it on the call stack, and the Web API creates a timer that expires in one second (1000ms). Then, JS engine places the `task()` function into a queue, called the **callback queue** or **task queue**.

The **event loop** is a constantly running process that monitors both the callback queue and the call stack.

IF the call stack is not empty, the event loop waits until it IS empty, then places the next function from the callback queue to the call stack. (if the queue is empty,nothing happens).

As another example:

    console.log('Hi!');

    setTimeout(() => {
        console.log('Execute immediately.');
    }, 0);

    console.log('Bye!');

In this, both console.logs execute before the setTimeout, even though there's no delay. This is because the JS engine places the `console.log('Execute immediately.');` function call on the **callback queue**, and *executes it when the callback is empty*. Due to this, `console.log('Hi!');` and `console.log('Bye!');` are placed into the call stack and executed first, THEN the setTimeout callback is executed.

    Hi!
    Bye!
    Execute immediately.

## Promise
A **Promise** is an object representing the eventual completion or failure of an asynchronous operation.

It is essentially a returned object to which you attach callbacks, instead of passing callbacks into a function. For example, a function `createAudioFileAsync()` asynchronously generates a sound file given a configuration record and two callbacks: one for if the audio file is successfully created, and the other if an error occurs.

Here is the above function, written without a promise:
    function successCallback(result) {
        console.log(`Audio file ready at URL: ${result}`);
    }

    function failureCallback(error) {
        console.error(`Error generating audio file: ${error}`);
    }

    createAudioFileAsync(audioSettings, successCallback, failureCallback);

And here is `createAudioFileAsync()` written as a promise:

    createAudioFileAsync(audioSettings).then(successCallback, failureCallback);

There are many advantages to promises.

### Chaining
You commonly need to execute two or more asynchronous operations back to back, where each subsequent operation starts when the previous one succeeds, using results from the previous step. Without promises, you would quickly find yourself in a callback pyramid from hell.

WITH promises, you can make a **promise chain**. The API design of promises attaches callbacks to the *returned* promise object, instead of being passed into a function.

The magic: the `then()` function returns a **new promise**, different from the original.

    const promise = doSomething();
    const promise2 = promise.then(successCallback, failureCallback);

`promise2` represents the completion not just of `doSomething()`, but also of the `successCallback` or `failureCallback`.

With this pattern, you can make longer chains of processing, where each promise represents the completion of one asynchronous step in the chain. In addition, the arguments to `then` are **optional**, and `catch(failureCallback)` is short for `then(null, failureCallback)`. SO, if your error handling code is the same for all steps, you can just attach this to the end of the chain:

    doSomething()
        .then(function (result) {
            return doSomethingElse(result);
        })
        .then(function (newResult) {
            return doThirdThing(newResult);
        })
        .then(function (finalResult) {
            console.log(`Got final result: ${finalResult}`);
        })
        .catch(failureCallback);

This can be expressed with arrow functions instead:

    doSomething()
    .then((result) => doSomethingElse(result))
    .then((newResult) => doThirdThing(newResult))
    .then((finalResult) => {
        console.log(`Got final result: ${finalResult}`);
    })
    .catch(failureCallback);

**IMPORTANT**: ALWAYS return results, otherwise callbacks won't catch the result of a previous promise (with arrow functions, `() => x` is short for `() => {return x;}`). If a previous handler started a promise but did not return it, there's no way to track its settlement anymore, and the promise is then said to be "floating".

This may be worse if you have race conditions, as if the promise from the last handler is not returned, the `then` handler will be called early, reading incomplete values.

So, as a rule of thumb, whenever your operation encounters a promise, return it and defer its handling to the next `then` handler.

    const listofIngredients = []l

    doSomething()
        .then((url) => {
            fetch(url)
                .then((res) => res.json())
                .then((data) => {
                    listOfIngredients.push(data);
                }),
        )
        .then(() => {
            console.log(listOfIngredients);
        })
    
    // OR

    doSomething()
        .then((url) => fetch(url))
        .then((res) => res.json())
        .then((data) => {
            listOfIngredients.push(data);
        })
        .then(() => {
            console.log(listOfIngredients);
        });

### Nesting

In the examples above, the first one has a promise chain nested in the return value of another `then()` handler, while the second uses an entirely flat chain.

Simple promise chains are best kept flat without nesting, as nesting can be a result of careless composition. Nesting is a control structure to limit the scope of `catch()` statements. Specifically, a nested `catch()` only catches failures in its scope and below, not higher up the chain outside the nested scope. When used correctly, this gives more accurate error recovery:

    doSomethingCritical()
        .then((result) =>
            doSomethingOptional(result)
                .then(optionalResult) =>
                    doSomethingExtra(optionalResult))
            .catch((e) => {}),
            ) // Ignore if optional stuff fails; proceed
            .then(() => moreCriticalStuff())
            .catch((e) => console.error(`Critical failure: ${e.message}`))

Note that optional steps here are nested, with the nesting caused not by the indentation, but by hte placement of the outer `()` around the steps.

The inner error-silencing `catch` handler only catches failures from `doSomethingOptional()` and `doSomethingExtra()`, after which the code resumes with `moreCriticalStuff()`. Importantly, if `doSomethingCritical()` fails, its erro is caught by the final `catch`, and does not get eaten by the inner `catch` handler.

### Chaining after a catch
It's possible to chain *after* a failure, which is useful to accomplish new actions even after an action failed in the chain.

    new Promise((resolve, reject) => {
        console.log("initial");

        resolve();
    })
        .then(() => {
            throw new Error ("Something failed");

            console.log("Do this");
        })
        . catch(() => {
            console.error("Do that");
        })
        .then (() => {
            console.log("Do this, no matter what happened before");
        });

        // OUTPUT:
        // Initial
        // Do that
        // Do this, no matter what happened before

Note that "Do this" is NOT output, because the error caused a rejection.

### Common mistakes
When composing promise chains, look out for these:

    doSomething()
        .then(function(result) {
            // Forgot to return promise from inner chain + unnecessary nesting
            doSomethingElse(result).then((newResult) => doThirdThing(newResult));
        })
        .then(() => doFourthThing());
    // Forgot to terminate chain with a catch!

**First mistake**: not chaining things together properly. This happens when we create a new promise but forget to return it. Consequently, the chain is broken, or rather we now have 2 independent chains racing. This means `doFourthThing()` won't wait for `doSomethingElse` and `doThirdThing` to finish, and will run in parallel with them, which is likely unintended.

Separate chains also have separate error handling, leading to uncaught errors.

**Second mistake**: nesting unnecessarily, enabling the first mistake. A variant of this is the[ promise constructor anti-pattern](https://stackoverflow.com/questions/23803743/what-is-the-explicit-promise-construction-antipattern-and-how-do-i-avoid-it), which combines nesting with redundant use of the promise constructor to wrap code that already uses promises.

**Third mistake**: forgetting to terminate chains with `catch`. Unterminated promise chains lead to uncaught promise rejections in most browsers.

A good rule of thumb is to always return or terminate promise chains, and as soon as you get a new promise, return it immediately, to flatten things.

doSomething()
    .then(function (result) {
        // If using a full function expression: return the promise
        return doSomethingElse(result);
    })
    // If using arrow functions: omit the braces and implicitly return the result
    .then((newResult) => doThirdThing(newResult))
    // Even if the previous chained promise returns a result, the next one
    // doesn't necessarily have to use it. You can pass a handler that doesn't
    // consume any result.
    .then(() => doFourthThing())
    // Always end the promise chain with a catch handler to avoid any
    // unhandled rejections!
    .catch((error) => console.error(error));

Using **async/await** addresses most if not all of these problems, the tradeoff being that it may be easy to forget the **await** keyword.

### Error handling
If there are exceptions, the browser will look down the chain for `.catch()` handlers or `onRejected`. This is modeled after how synchronous code works:

    try {
        const result = syncDoSomething();
        const newResult = syncDoSomethingElse(result);
        const finalResult = syncDoThirdThing(newResult);
        console.log(`Got the final result: ${finalResult}`);
    } catch (error) {
        failureCallback(error);
    }

This symmetry with asynchronous code culminates in the async/await syntax:

    async function foo() {
        try {
            const result = await doSomething();
            const newResult = await doSomethingElse(result);
            const finalResult = await doThirdThing(newResult);
            console.log(`Got the final result: ${finalResult}`);
        } catch (error) {
            failureCallback(error);
        }
    }

It builds on promises - e.g. `doSomething()` is the same function as before, so there's minimal refactoring needed to change from promises to `async`/`await`.

#### Promise rejection events
If a promise rejection is not handled by any handler, it bubbles to the top of the call stack, and the host needs to surface it. On the web, when a promise is rejected, one or two events is sent to the global scope:
- `rejectionhandled` - sent when a promise is rejected, after that rejection has been handled by the executor's `reject` function
- `unhandledrejection` - sent when a promise is rejected but there's no rejection handler available
In both cases, the event (of type `PromiseRejectionEvent`) has a `promise` property indicating the promise that was rejected, and a `reason` property giving a reason.

With these, you can offer fallback error handling for promises, as well as debug issues with your promise management.

### Composition
There are four composition tools for running asynchronous operations concurrently:
- `Promise.all()`
- `Promise.allSettled()`
- `Promise.any()`
- `Promise.race()`

We can start operations in parallel and wait for them all to finish like this:

    Promise.all( [ func1(), func2(), func3() ] )
        .then( ([result1, result2, result3]) => {
            // use result1, result2, and result3
    });

As seen above, `Promise.all()` takes an **array** of callbacks, and returns an array of corresponding results.

If one of the promises in the array rejects, `Promise.all()` immediately rejects the returned promise and aborts the other operations, which may cause unexpected state or behaviour.

Like `Promise.all()`, `Promise.allSetlled()` ensures all operations are complete before resolving.

All these methods run promises in parallel - they are started simultaneously and do not wait for each other. Sequential composition is possible using some clever JS:

    [func1, func2, func3]
        .reduce((p, f) => p.then(f), Promise.resolve())
        .then((result3) => {
            // use result3
        })
In above, we **reduce** an array of asynchronous functions down to a promise chain. The code above is equivalent to:

    Promise.resolve()
        .then(func1)
        .then(func2)
        .then(func3)
        .then((result3) => {
            // use result3
        })

This can be made into a reusable compose function, common in functional programming:
    const applyAsync = (acc, val) => acc.then(val);
    const composeAsync =
        (...funcs) =>
        (x) =>
            funcs.reduce(applyAsync, Promise.resolve(x));

Before you compose promises sequentially, consider if it's really necessary - it's always better to run promises in parallel so they don't unnecessarily block each other.

### Timing

#### Task queues vs microtasks
Promise callbacks are handled as microtasks, whereas setTimeout callbacks are handled as task queues.

## Promise

A promise is a *proxy* for a value that is not necessarily known when the promise is created. It allows you to associate handlers with an asynchronous action's eventual success value or failure reason. This lets asynchronous methods return values like synchronous methods: instead of immediately returning the final value, the asynchronous method returns a **promise** to supply the value at some point in the future.

A promise is in one of three states:
- pending: initial state, neither fulfilled nor rejected
- fulfilled: operation completed successfully
- rejected: operation failed

The eventual state of a pending promise can either be fulfilled with a value or rejected with a reason. When either of these occur, the associated handlers queued up by a promise's `then` method are called. A Promise is said to be *settled* if either fulfilled or rejected.

The term *resolved* used with promises refers to the promise being *settled* or "locked-in" to match the eventual state of another promise - further resolving or rejecting it has no effect. "Resolved" is often used to refer to successfully fulfilled promises, but sometimes it can refer to pending or rejected promises as well.

    new Promise((resolveOuter) => {
        resolveOuter(
            new Promise((resolveInner) => {
            setTimeout(resolveInner, 1000);
            })
        );
    });

Like so, this promise is already *resolved* at the time it's created (because `resolveOuter` is called synchronously), but it is resolved with another promise, so it won't actually be fulfilled until 1 secomd later, when `resolveInner` fulfills.

### Chained promises
`.then()` takes 2 arguments: first is a callback for the fulfilled case, second is callback for rejected case. Each `.then()` returns a newly generated promise, which can optionally be used for chaining.

It's simpler to leave error handling for a rejected promise to the very end, in a `catch()`, but if an error needs to be handled immediately, you should throw an error of some type to maintain error state down the chain.

NOTE: For faster execution, all synchronous actions should be done within one handler, otherwise it would take several ticks to execute all handlers in sequence.

### Promise Concurrency
The Promise class offers 4 static methods to facilitate async task concurrency:

#### Promise.race()
Settles when ANY of the promises settles.
Takes an iterable of promises as input, returns a single promise. This promise settles with the eventual state of the FIRST promise that settles, whether it is fulfilled or rejected.

#### Promise.any()
Fulfills when ANY of the promises fulfills, rejects when ALL of the promises reject.
Takes an interable of promises, returns a single Promise, which fulfills when any of the input's promises fulfills, with this first fulfillment value. Rejects when ALL of the input's promises reject (including when empty iterable is passed), returning an array of rejection reasons. Otherwise, even if one promise rejects, it will return the first successfully fulfilled promise.

#### Promise.all()
Fulfills when ALL the promises fulfill, rejects when ANY of the promises reject.
Takes an array of promises, and returns an array of promises that resolve with the corresponding fulfillment values *in the same order of the input array* - regardless of which promises fulfill first. Rejects when any of the promises reject.

#### Promise.allSettled()
Fulfills when ALL promises settle.
Takes an iterable of promises as input, returns a single Promise. This returned Promise fulfills when all of the input's promises settle (including when an empty iterable is passed), with an array of objects that *describe the outcome* of each promise.

    const promise1 = Promise.resolve(3);
    const promise2 = new Promise((resolve, reject) => setTimeout(reject, 100, 'foo'));
    const promises = [promise1, promise2];

    Promise.allSettled(promises).
        then((results) => results.forEach((result) => console.log(result.status)));

    // expected output:
    // "fulfilled"
    // "rejected"

### Promise constructor
Creates a new Promise. Used to wrap functions that do not already support promises.

    const promise1 = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('foo');
        }, 300);
    });

    promise1.then((value) => {
        console.log(value); // expected output: "foo"
    });

    console.log(promise1); // expected output: [object Promise]

### .finally()
Haven't seen this before!

Schedules a callback function to be called when the promise is settled. Like `then` and `catch`, it immediately returns an equivalent Promise object, allowing chaining.

function checkMail() {
  return new Promise((resolve, reject) => {
    if (Math.random() > 0.5) {
      resolve('Mail has arrived');
    } else {
      reject(new Error('Failed to arrive'));
    }
  });
}

checkMail()
  .then((mail) => {
    console.log(mail);
  })
  .catch((err) => {
    console.error(err);
  })
  .finally(() => {
    console.log('Experiment completed');
  });

So basically, this code executes when the Promise settles, regardless of whether it fulfilled or rejected. If you want something to happen regardless of result, then use `.finally()`.

## async
The `async` function declaration declares an async function where the `await` keyword is permitted within the function body. `async` and `await` enable asynchronous, promise-based behaviour to be written in a cleaner style, so you don't need to explicitly configure promise chains.

Demo:

    function resolveAfter2Seconds() {
        return new Promise(resolve => {
            setTimeout(() => {
                resolve('resolved');
            }, 2000);
            })
        });
    }

    async function asyncCall() {
        console.log('calling');
        const result = await resolveAfter2Seconds();
        console.log(result);
    }

    asyncCall();

The above code outputs "resolved" after 2 seconds. Even though `asyncCall()` is called at the start, the `await` keyword delays the execution of the remainder of the function until `resolveAfter2Seconds` is resolved and returns the expected value.

Async functions return a Promise which will be resolved with the value returned by the async function, or rejected with an exception. They **always return a Promise**, so if the return value of an async function is not explicitly a promise, it will be implicitly wrapped in a promise.

    async function foo() {
        return 1;
    }

    function foo() {
        return Promise.resolve(1);
    }

These two functions are similar. HOWEVER, the return value of an async function is not 100% equivalent to a `Promise.resolve`. An async function will return a different reference, whereas `Promise.resolve` returns the same reference if the given value is a promise. This can cause issues checking the equality of a promise and a returned value from an async function.

    const p = new Promise((res, rej) => {
        res(1);
    });

    async function asyncReturn() {
        return p;
    }

    function basicReturn() {
        return Promise.resolve(p);
    }

    console.log(p === basicReturn()); // true
    console.log(p === asyncReturn()); // false

Async functions can contain zero or more `await` expressions, which make promise-returning functions behave as though they are synchronous by **suspending execution** until the returned promise is fulfilled or rejected. The resolved value of the promise is treated as the return value of the await expression.

Use of `async`/`await` enables the use of ordinary `try`/`catch` blocks around asynchronous code.

The body of an async function can be thought of as being split by **zero or more** await functions. Top level code up to, and including, the first await expression, is run synchronously. In this way, an async function without an await expression will run synchronously.

If there is an await expression inside the function body, the async function will always complete asynchronously.

### Async, more in depth
Asynchronous code becomes more difficult to follow when lots of things are going on. 

The two code blocks below do the same thing (get info from server, process it, return a promise).

    function getPersonsInfo(name) {
        return server.getPeople().then(people => {
            return people.find(person => { return person.name === name });
        });
    }
And,

    async function getPersonsInfo(name) {
        const people = await server.getPeople();
        const person = people.find(person => { return person.name === name });
        return person;
    }

To use `await`, you need to precede a function declaration with `async`.

Functions declared with `async` **automatically** returns a promise. Returning an async function is the same as resolving a promise. Throwing an error will reject the promise.

It's important to note that `async` functions are just syntactical sugar for `promises`.

It's valid to use an `async` function anywhere you can use a normal function. You can use the keyword with ANY of the ways a function can be created. See below:

    // Arrow function
    const yourAsyncFunction = async () => {
        // do something asynchronously and return a promise
        return result;
    }

    // Array method
    anArray.forEach(async item => {
        // do something asynchronously for each item in 'anArray'
        // or, use .map to return an array of promises to use with Promise.all(inArray)
    })

    // As part of a promise
    server.getPeople().then(async people => {
        people.forEach(person => {
            // do something asynchronously for each person
        })
    })

### await
`await` tells JavaScript to wait for an asynchronous function to finish before continuing the function. It's basically "pause until done". `await` is used to get a value from a function **where you would normally use `.then()`.** Instead of calling `then()` after the asynchronous function, you simply assign a variable to the result using await, e.g. `let varName = await server.getPeople();`. Then you can use the result in your code as if it was synchronous.

### `async` error handling
Apparently, it's easy. Promises have `catch()`, and since async functions return a promise, you can simply call the function and apply `.catch()` to the end.

    const asyncFunction = async () => {
        // async code
    }

    asyncFunction().catch(error => {
        console.error(error);
    })

#### Using Try/Catch
If you want to handle the error *directly* inside the async function...

    async function getPersonsInfo(name) {
        try {
            const people = await server.getPeople();
            const person = people.find(person => { return person.name === name });
            return person;
        } catch (error) {
            // Handle the error however you want
        }
    }

**I've been told to do the API lesson first, so I will do that, then come back to this**. I'll return here: https://www.theodinproject.com/lessons/node-path-javascript-async-and-await (bookmark)

## try/catch for error handling
Usually, when there is an error, the script "dies" (immediately stops) and prints the error to the console. BUT, the syntax `try...catch` allows us to "catch" errors so the script can, instead of dying, do something else.

### Syntax

Two main blocks: `try`, then `catch`

    try {
        // code
    } catch (err) {
        // error handling
    }

1. First, the code in `try` is executed.
2. If no errors, then `catch(err)` is ignored.
3. If an error occurs, then `try` execution is stopped, and control flows to the beginning of `catch(err)`. The `err` variable (which any name can be used for, not just err), will contain an error object with details about what happened.

So, an error inside `try` won't kill the script; we will have a chance to handle the error.

**NOTE**: `try...catch` only works for runtime errors. In other words, it needs to be valid JavaScript. If the issue is syntactically wrong, e.g. missing curly braces, the engine won't be able to understand the "parse-time" error, and the error will be unrecoverable.

**Also**, `try...catch` works **synchronously**. If an exception occurs in scheduled code, like `setTimeout`, then `try...catch` won't catch it. This is because the function itself executes later, when the engine has already left the try...catch construct.
So, to catch an exception inside a scheduled function, `try...catch` must be inside that function.

    setTimeout(function() {
        try {
            noSuchVariable;
        } catch {
            alert("error caught here");
        }
    }, 1000);

### Error object
When JavaScript encounters an error, it generates an object containing details about the error. This object is then passed as an argument to `catch`.

For built in errors, the object has 2 main properties:
- `name`, the error name, e.g. `ReferenceError` for an undefined variable.
- `message`, textual message about error details.
- `stack`, the current call stack; a string with information about the sequence of nested calls that led to the error, for debugging purposes.

e.g. `err.message` will say something like "variable is not defined".

#### Optional "catch" binding
If you don't actually need error details, you can just do `catch { }` without any arguments.

### When to use try/catch
If you make a fetch request and try to `.json()` it, if the json is malformed, an error will be generated, so the script "dies".

But, dying without any notice is bad UX. So, use try/catch to handle the error.

    let json = "{ bad json }";

    try {
        let user = JSON.parse(json);
        alert(user.name);
    } catch (err) {
        // the execution will jump here
        alert( "Our apologies, the data has errors, we'll try to request it one more time." );
        alert( err.name );
        alert( err.message );
    }

Here, `catch` only shows the message, but you can do other things, e.g. send a new network request, suggest an alternative to the user, send info to a logging facility. These are all better than just dying.

### Throwing our own errors
What if `json` is syntactically correct, but is missing a required `name` property?

    let json = '{ "age": 30 }'; // incomplete data

    try {
        let user = JSON.parse(json); // <-- no errors
        alert( user.name ); // no name! runs, but with "undefined"
    } catch (err) {
        alert( "doesn't execute" );
    }

Here, the absence of `name` is an error, for us. So, we can use the `throw` operator.

#### `throw`
Generates an error. `throw <error object>`, e.g. `throw error` (the variable name is error)

You can technically use anything as an error object, e.g. primitive, string, but it's better to use objects, preferably with `name` and `message` properties (to stay somewhat compatible with built-in errors).

JavaScript has built-in constructors for standard errors: `Error`, `SyntaxError`, `ReferenceError`, `TypeError`, etc. You can use these to create error objects.

Syntax:

    let error = new Error(message);
    let error = new SyntaxError(message);
    let error = new ReferenceError(message);

For built-in error objects only, the `name` is the same as the name of the constructor. The `message` property is taken from the argument.

So, in the previous example, we can now throw an error if the user has no `name`.

    let json = '{ "age": 30 }'; // incomplete data

    try {
        let user = JSON.parse(json); // <-- no errors

        if (!user.name) {
            throw new SyntaxError("Incomplete data: no name"); // (*)
        }

        alert( user.name );
    } catch (err) {
        alert( "JSON Error: " + err.message ); // JSON Error: Incomplete data: no name
    }

Now, the `catch` block is a single place for all error handling: both for `JSON.parse` and other cases.

### Rethrowing
In above example, we use try/catch to handle incorrect data. But, what if *another unexpected error* occurs in `try`? Like a programming error (variable is not defined) or something else. e.g
    let json = '{ "age": 30 }'; // incomplete data

    try {
        user = JSON.parse(json); // <-- forgot to put "let" before user!
        // ...
    } catch (err) {
        alert("JSON Error: " + err); // JSON Error: ReferenceError: user is not defined
        // (no JSON Error actually)
    }

`catch` shows a "JSON Error", but it actually isn't. This can make debugging more difficult.

For this, we can do "rethrowing". The rule: **Catch should only process errors that it knows, and rethrow all others.**

1. Catch gets all errors.
2. In the `catch (err) { }` block, analyze object `err`.
3. If we don't know how to handle it, do `throw err`.

You can usually check the error type using `instanceof` operator:

    try {
        user = { /*...*/ };
    } catch (err) {
        if (err instanceof ReferenceError) {
            alert('ReferenceError'); // "ReferenceError" for accessing an undefined variable
        }
    }

We can also get the error class name from `err.name` property. All native errors have it. Another option is to read `err.constructor.name`.

Below, rethrowing, such that `catch` only handles `SyntaxError`.

    let json = '{ "age": 30 }'; // incomplete data
    try {
        let user = JSON.parse(json);
        if (!user.name) {
            throw new SyntaxError("Incomplete data: no name");
        }
        blabla(); // unexpected error

        alert( user.name );
    } catch (err) {
        if (err instanceof SyntaxError) {
            alert( "JSON Error: " + err.message );
        } else {
            throw err; // rethrow (*)
        }
    }
Note that on line `(*)`, this error will "fall out" of the try/catch block and can either be caught by an outer try/catch (if it exists) or it will kill the script.

### try...catch...finally
As with promises, try/catch blocks also have an optional extra code clause, `finally { }`, which also runs in all cases (regardless of errors occurring).

    try {
        // code
    } catch (err) {
        // handle err
    } finally {
        // execute always
    }

**NOTE**: `finally` works for *any* exit from try/catch, including an explicit `return`.

#### try...finally (no catch)

This is also useful - when we don't want to handle errors, but we want to be sure that processes we started are finalized.

    function func() {
        // start doing something that needs completion (like measurements)
        try {
            // ...
        } finally {
            // complete that thing even if all dies
        }
    }

**NOTE**: Don't use try/catch to exit for loops. In JS, it seems that you need to have an error to access `catch`, and doing so just to exit a loop is bad practice.

## JSON
**JavaScript Object Notation**, a standardized format for structuring data, heavily based on the syntax for JS Objects. It's basically the universal format for transmitting data on the web, so it's often encountered working with external servers or APIs.

JSON is purely a string with a specified data format. Contains only properties, no methods. **Double quotes around strings and property names are required**. Single quotes are ONLY valid when surrounding the entire JSON string. A single misplaced comma/colon can cause a JSON file to throw errors, so be sure to validate any data you're using. JSON are not necessarily objects/arrays. A single string or number is valid JSON.

Needs to be converted to a native JS object to access data. JS provides a JSON object with built-in methods for converting things to/from JSON. e.g. arrays can be converted to/from JSON.

### Converting between objects and text
Sometimes you receive a raw JSON string that needs to be converted to an object, instead of a JSON that is nicely formatted and ready to be used.

And, when you want to send a JS object across the network, we need to convert it to JSON before sending. JavaScript's JSON object has two built in methods for this:

- `parse()` - accepts a JSON string as a parameter, **returns** the corresponding JavaScript object.
- `stringify()` - accepts an object as a parameter, and **returns** the equivalent JSON string.

### Mis-formatted JSON
A common cause of errors is misformatted JSON data, so try using this [JSON formatter website](https://jsonformatter.curiousconcept.com/) to search for formatting errors if you have any. [JSONLint](https://jsonlint.com/) is another option.

## Asynchronous code
Some things take a while to complete, e.g. fetching data from a server. JS includes support for asynchronous functions: functions that can happen in the background while the rest of your code executes.

Instead of the code executing top to bottom (like reading a book), async programs may execute different functions at different times based on order/speed that earlier functions happen.

Determining if a function is asynchronous or not depends on context. **Synchronous** code essentially can be expected to execute sequentially from top to bottom. Usually, methods like accessing things in memory or doing work on the CPU are synchronous. **Asynchronous**  methods generally involve talking to hard drives or networks. Because, sometimes, I/O is really slow - talking to a hard drive is about 100000 times slower than talking to memory, e.g. RAM.

When you run a program, all the functions are defined immediately, but *they don't all execute immediately*.

The key to callbacks is understanding that they are used when you don't know **when** some async operation will complete, but you know **where** it will complete (the last line of the async function). First you split up the code into functions, then use callbacks to declare if one function depends on another one finishing.

**NOTE**: functions are just objects that can be stored in variables and passed around with different names.

### Callbacks
**A function passed into another function as an argument**, which is then invoked within to complete a routine/action.

Using callbacks can get wild, especially if you need to chain several of them in a specific order. Try to avoid callback hell.

## Event Loop

### Stack
Data structure that records where we are, basically. If we step into a function, we put something on top the stack. If we return from a function, we pop it off the top of the stack. (always LIFO)

**Blowing the stack**: doing something recursive, e.g.

    function foo() {
        return foo();
    }
    foo();

Will stack the stack to infinity, so the browser will generally kill things (the page hangs).

### Blocking
Basically, code that is slow and on the stack is "blocking" things. In a single threaded programming language, you do kind of have to wait until things are "done" before moving on. However, in JS, you can use asynchronous callbacks...

### Concurrency and the Event Loop
JS can only do one thing at a time. BUT, the browser is more than just the runtime. So, the JS runtime can only do one thing at a time, but the browser has other tools, web APIs, which are effectively threads (you can't access them, only make calls to them). 

So, when you call a `setTimeout(callback)`, it's an API provided by the browser, so it gets added to the stack, then the browser kicks off a timer for you. The initial `setTimeout` call itself is now done, so it gets popped off the stack. Then the next piece of code gets put on the stack etc.

During this, the web API is counting down the timer, and when it's done, the `callback` is added to the **task queue**. 

Now, we get to the event loop. It has one job: to look at the stack and task queue. If the stack is empty, it takes the first thing from the task queue and pushes it onto the stack (which runs it). So, when the stack is next empty, the `callback` gets put on the stack. If it only has `console.log('hi')` inside, then that is put on top of the stack, then executed, then popped off the stack, then the `callback` frame is also popped off the stack.

#### setTimeout(0)

Hence, if you're trying to defer something until the stack is clear, you can wrap it in a `setTimeout(callback, 0)`. Even though the delay is 0, the fact that it has to be processed by the web API means that it will be added to the task queue. Your `main()` will continue to run, and only when the stack is empty, the callback is executed.

Asynchronous callbacks aren't the same as synchronous callbacks.

e.g. with `Array.forEach(callback)`, it IS executing a callback, but it's within the current stack, so it's not a given that it's asynchronous.

You CAN make a forEach asynchronous, by putting asynchronous code within the callback.

Each function call will sit and block the stack until it's done, by the way. The stack is filled on a per-function basis, not a per-document basis.

So, if you are doing something to each item in an array that could take a while, you can:

    function asyncForEach(array, cb) {
        array.forEach(function() {
            setTimeout(cb, 0);
        })
    }

##### About the Render Queue
Ideally the browser would like to repaint the screen at 60fps. But it can't do a render if there's code on the stack. Every 16ms, the Render Queue queues a render, then it waits until the stack is clear before it can actually do the render. However, it IS given higher priority than the task queue.

If you're doing something that is slow AND synchronous, the render will be blocked, so the user can't actually interact with the webpage (it's hanging). Because, the stack doesn't empty until the function is done.

BUT, by queueing something slow (making it asynchronous), you are *giving the render queue the chance to render in between each execution*. Instead of waiting for 20 slow synchronous things to happen, the render queue can execute a render between each of these 20 things, which feels much better.

Basically, don't put crappy slow code on the stack, because the browser can't do what it needs to do, which is create a fluid user experience.

An example of this concept is scroll handlers. These trigger a LOT, like every frame. 

#### Debouncing
Basic explanation: we can queue up all these events, but let's only do the slow work every few seconds or until the user stops scrolling.

## 3 Different Kinds of Prototypal Inheritance: ES6+ Edition
From [this](https://medium.com/javascript-scene/3-different-kinds-of-prototypal-inheritance-es6-edition-32d777fa16c9)

### Design Patterns, written by "The Gang of Four
The first principle: **You should program to an interface, not to an implementation.**
Second principle: **Favour object composition over class inheritance.**

### Factory function using Object.create()
In JS, any function can create new objects. When it's not a *constructor function*, it's called a *factory function*.

### Composition over Inheritance
Inheritance: designing your types around what they ARE

Composition: designing your types around what they DO. e.g.

    dog              = pooper + barker
    cat              = pooper + meower
    cleaningRobot    = driver + cleaner 
    murderRobot      = driver + killer
    murderRobotDog   = driver + killer + barker

From the above (from the video), let's put these into functions:

    const barker = (state) => ({
        bark: () => console.log('Woof, I am ' + state.name)
    })

    const driver = (state) => ({
        drive: () => state.position = state.position + state.speed
    })

    barker({name: 'karo'}).bark()

Here's the actual murderRobotDog factory function:

    const murderRobotDog = (name) => {
        let state = {
            name,
            speed: 100,
            position: 0
        }
        return Object.assign(
            {},
            barker(state),
            driver(state),
            killer(state)
        )
    }

`Object.assign` takes an object, and assigns the properties from the other objects into this object. So it creates barker, driver, killer, and merges them all into this first object, then returns the new object.

**When to use inheritance vs composition?** Most people say to favour composition. Video maker says in his opinion, **you should not need to use inheritance at all**. The problem with inheritance is that it encourages you to predict the future, and lock yourself into a taxonomy of objects early on in a project, which is then hard to get out of later.

However, composition is easy to do, and flexible, so why not.

### What about `class`?
The base JS object system is very easy and flexible, but because many people from other languages struggled with understanding it without any familiar words, they kept trying to emulate the class paradigm in JS. So many libraries and also vanilla JS added a pseudo-class system. However, it is inferior to composition, so you should just make use of the native prototypal alternatives.

## The Module Pattern (lite)
**NOTE**: ES6 introduced *modules*. Modules are syntax for importing/exporting code between different JS files. They are very powerful and will be covered later. They are also NOT what this section is about.

Modules are similar to factory functions, except instead of creating a factory that can be reused over and over to create multiple objects, the module pattern wraps the factory in an IIFE (Immediately Invoked Function Expression).

### IIFE
The basic concept:
1. Write a function
2. Wrap the function in `()`
3. Add a `()` onto the end of it.

Pronounced 'iffy' apparently. 

Function declaration:
    function myFunction() { }

Function expression (assigning a function to a variable or property):
    let myFunction = function () { }

    let myObj = {
        myFunction: function () { }
    }

A function created in the context of an expression is also an expression.

    ( function () { } )

I haven't seen this before but apparently anything after the `!` not operator is part of an expression too.

    !function () { /* logic here */ };

The key thing about expressions is that they return values. In both cases above, the return value is the expression of the function. This means, if you want to invoke the function expression immediately, just put `()` on the end.

    (function () {
        // code
    })();

#### WHY use IIFEs?
The primary reason is data privacy. Due to the way JS does scope, anything within a closure is essentially "private".

You COULD just explicitly name and invoke a function to achieve the same thing. HOWEVER, there are downsides:
- Pollutes the global namespace
- The intentions of this code are not self-documenting as an IIFE
- Because it is named and ISN'T self-documenting, it might accidentally be invoked more than once.

You can pass arguments into the IIFE as well.

    let foo = "foo";

    (function (innerFoo) {
        console.log(innerFoo); // Outputs "foo"
    })(foo);

### Back to module patterns
Commonly used for singleton style objects where only one instance exists. Good for testing/TDD and services.

Create an anonymous closure (IIFE).
    (function() {
        'use strict' // Apparently it's good practice to use strict mode?
        // put your code here
    })();

Export your module (assign it to a variable you can use to call the methods):
    let myModule = (function() {
        'use strict';
        // etc
    })();

Now, create a public method for your module to call. To expose this method outside the closure, return an Object with the methods defined.
    let myModule = (function() {
        'use strict';

        let _privateProperty = 'Hello World!';
        
        function _privateMethod: function() {
            console.log(_privateProperty);
        }

        return {
            publicMethod: function() {
                _privateMethod();
            }
        };
    })

    myModule.publicMethod(); // Hello World

Additionally, you can take advantage of the closure to create private properties and methods, and only export the ones you want. `_` is a common prefix to indicate that something is "private" in JS.

### Revealing Module Pattern
One of the most popular ways to create modules. Using the `return` statement, we can return an object literal that 'reveals' only the methods/properties we want to be public.

    let myModule = (function() {
        'use strict';

        var _privateProperty = 'Hello World';

        var publicProperty = 'I am a public property';

        function _privateMethod() {
            console.log(_privateProperty);
        }

        function publicMethod() {
            _privateMethod();
        }

        return {
            publicMethod,
            publicProperty
        };
    })();

    myModule.publicMethod(); // outputs 'Hello World'
    console.log(myModule.publicProperty); // outputs 'I am a public property'
    console.log(myModule._privateProperty); // is undefined protected by the module closure
    myModule._privateMethod(); // is TypeError protected by the module closure

One of the advantages of this pattern are that you can look at the bottom to quickly see what's available for public use.

Write a factory method that returns an object:

    const Pet = (name, age) => {
        function identify() {
            console.log('Hello, ' + name);
        }
        return {
            name,
            age,
            identify
        }
    }

    const Dog = (name, age) => {
        const {identify} = Pet(name);
        const play = () => console.log(name + ' is playing.');
        return {identify, play};
    }

    const bobby = Dog('Bobby', 13);

    bobby.identify();
    bobby.play();

## Classes
JS classes are *syntactic sugar* for the existing prototype-based constructors. Many people say that they obscure what's really going on with objects, so they're dangerous to use. However, frameworks use them sooooooooooo

### Getters and Setters
2 kinds of object properties:
- Data properties
- Accessor properties: functions that execute on getting and setting a value, but they look like regular properties to an external code.

#### Accessor properties
In an object literal, they are denoted by `get` and `set`:

    let obj =  {
        get propName() {
            // getter, code executed on getting obj.propName
        },
        set propName(value) {
            // setter, code executed on setting obj.propName = value
        }
    }

When `obj.propName` is read, the getter works. When it is assigned, the setter works.

e.g. If we have an object with a name and a surname, and we want a full name property, we can implement it as an accessor.

    let user = {
        name: "John",
        surname: "Smith",
        get fullname() {
            return `${this.name} ${this.surname}`
        }
    };

From the outside, this `fullName` accessor property will look just like a regular one. So we DON'T call it as a function, but just like `user.fullName`.

So far `fullName` only has a getter, so if you try to assign `user.fullName = ` there will be an error. If you want to be able to set it:

    let user = {
        name: "John",
        surname: "Smith",
        get fullname() {
            return `${this.name} ${this.surname}`
        },
        set fullName(value) {
            [this.name, this.surname] = value.split(" ");
        }
    };

Now you have a "virtual" property that is readable/writable.

### Accessor descriptors
Descriptors for accessor properties: there's no `value` or `writable`. Instead there are get and set functions.

An accessor descriptor may have:
- `get` - function without arguments that executes when property is read
- `set`, function with one argument, called when property is set
- `enumerable`, same as data properties
- `configurable`, same as data properties

To create an accessor `fullName` with `defineProperty`, you can pass a descriptor with `get` and `set`:
    
    let user = {
        name: "John",
        surname: "Smith",
    };

    Object.defineProperty(user, 'fullName', {
        get() {
            return `${this.name} ${this.surname}`;
        },
        set(value) {
            [this.name, this.surname] = value.split(" ");
        }
    });

    alert(user.fullName); // John Smith

    for(let key in user) alert(key); // name, surname

A property can be an accessor (has `get/set` methods) or a data property (has a `value`), NOT both.

If we try to supply both `get` and `value` in the same descriptor, there will be an error.

    // Error: Invalid property descriptor.
    Object.defineProperty({}, 'prop', {
        get() {
            return 1
        },

        value: 2
    });

### Smarter getters/setters
You can use getters/setters as wrappers over "real" property values to gain more control over operations with them.

e.g. If you want to have a minimum length for `user`, you can have a setter `name` and keep the value in a separate property `_name`.

    let user = {
        get name() {
            return this._name;
        },
        set name(value) {
            if (value.length < 4) {
                alert ("Name is too short, need at least 4 characters");
                return;
            }
            this._name = value;
        }
    };

    user.name = "Pete";
    alert(user.name); // Pete

    user.name = ""; // Name is too short...

Here, the value is stored in `_name`, and access is done via getter and setter.

*Technically*, external code will be able to access the name directly with `user._name`, but the convention is that `_` indicates a private property that shouldn't be touched.

### Using for compatibility
One of the great uses of accessors is that they allow control over a "regular" data property anytime by replacing it with a getter and setter and tweaking its behaviour.

e.g If we start off implementing user objects using data properties `name` and `age`:

    function User(name, age) {
        this.name = name;
        this.age = age;
    }

    let john = new User("John", 25);

    alert( john.age ); // 25

But then later we decide that storing `birthday` might be more convenient and accurate:

    function User(name, birthday) {
        this.name = name;
        this.birthday = birthday;
    }

    let john = new User("John", new Date(1992, 6, 1));

So, you could go back and change all the places that `age` appears throughout the code. But that will take time and can be very difficult to do, especially if the code is used by many other people. Also, it's still not a bad idea for a `user` to have `age`.

So, you can keep it and add a getter for `age` to fix this.

    function User(name, birthday) {
        this.name = name;
        this.birthday = birthday;

        // age is calculated from the current date and birthday
        Object.defineProperty(this, "age", {
            get() {
                let todayYear = new Date().getFullYear();
                return todayYear - this.birthday.getFullYear();
            }
        });
    }

    let john = new User("John", new Date(1992, 6, 1));

    alert( john.birthday ); // birthday is available
    alert( john.age );      // ...as well as the age

## Classes
Basic syntax:

    class MyClass {
        // class methods
        constructor() { }
        method1() { ... }
        method2() { ... }
        method3() { ... }
        ...
    }

Then use `new MyClass()` to create a new object with all the listed methods. `new` will automatically call the `constructor` method, initializing the object.

e.g.

    class User {
        constructor(name) {
            this.name = name;
        }

        sayHi() {
            alert(this.name);
        }
    }

    let user = new User("John");
    user.sayHi();

When `new User("John")` is called:
1. A new object is created.
2. The constructor runs with the given argument and assigns it to this.name.

...Then we can call object methods, such as `user.sayHi()`.

**NOTE**: Don't put commas between class methods, you'll get a syntax error. Classes aren't object literals.

### What is a class?
In JavaScript, a class is a kind of function.

    class User {
        constructor(name) { this.name = name; }
        sayHi() { alert(this.name); }
    }

    // proof: User is a function
    alert(typeof User); // function

What `class User {...}` construct really does:
1. Creates a function `User`, which is the result of the class declaration. The function code is taken from the `constructor` method (assumed empty if there isn't one).
2. Stores class methods e.g. `sayHi` in `User.prototype`.

After `new User` object is created, when we call its method, it's taken from the prototype. So the object has access to class methods.

We can illustrate the result of declaring `class User` as:
    class User {
        constructor(name) { this.name = name; }
        sayHi() { alert(this.name); }
    }

    // class is a function
    alert(typeof User); // function
    
    // ...or, more precisely, the constructor method
    alert(User === User.prototype.constructor); // true

    // The methods are in User.prototype, e.g:
    alert(User.prototype.sayHi); // the code of the sayHi method

    // there are exactly two methods in the prototype
    alert(Object.getOwnPropertyNames(User.prototype)); // constructor, sayHi

### Not just syntactic sugar
Technically you can achieve what a class achieves without using the `class` keyword at all, using a constructor function.

Note: A function prototype has "constructor" property by default, so we don't need to create it.

The outcome of a constructor will be roughly the same as using a class. So there are reasons why `class` can be considered syntactic sugar to define a constructor together with its its prototype methods.

Still, there are important differences.
1. A function created by `class` is labelled by a special internal property `[[IsClassConstructor]]: true`. So it's not entirely the same as creating it manually.

JS checks for that property in a variety of places. e.g. Unlike a regular function, it must be called with `new`:

    class User {
        constructor() { }
    }

    alert (typeof User); // function
    User(); // Error: Class constructor User cannot be invoked without 'new'

Also, a string representation of a class constructor in most JS engines starts with the "class..."

    class User {
        constructor() {}
    }

    alert(User); // class User { }

2. Class methods are non-enumerable. A class definition sets `enumerable` flag to `false` for all methods in the prototype. Which is good because if you `for...in` over an object, you don't want its class methods.
3. Classes always `use strict`. **ALL code inside the class construct uses strict mode automatically**.

There are also other `class` features.

### Class Expression
Just like functions, classes can be defined inside another expression/passed around/returned/assigned etc.

Here's an example of a class expression:
    let User = class {
        sayHi() {
            alert("Hello");
        }
    };

Similar to Named Function Expressions, class expressions may have a name (visible within the class only):

    // "Named Class Expression"
    // (no such term in the spec, but that's similar to Named Function Expression)
    let User = class MyClass {
        sayHi() {
            alert(MyClass); // MyClass name is visible only inside the class
        }
    };

    new User().sayHi(); // works, shows MyClass definition

    alert(MyClass); // error, MyClass name isn't visible outside of the class

You can also make classes dynamically "on demand":

    function makeClass(phrase) {
        // declare and return class
        return class {
            sayHi() {
                alert(phrase);
            }
        };
    }

    // Create a new class
    let User = makeClass("Hello");

    new User().sayHi(); // Hello

Kind of like the same concept as factory function.

### Getters/setters
Just like literal objects, classes can use getters/setters.

    class User {
        constructor(name) {
            this.name = name;
        }

        get name() {
            return this._name;
        }

        set name(value) {
            if (value.length < 4) {
                alert("name too short");
                return;
            }
            this._name = value;
        }
    }

### Computed names [...]
Example of a computed method name using `[]`

    class User {
        ['say' + 'Hi']() {
            alert("Hello");
        }
    }

    new User().sayHi();

Similar to object literals.

### Class fields
NOTE: Class fields are a recent addition to the language, older browsers may need a polyfill.

Class fields are a syntax allowing you to add any properties. e.g. Adding the property `name` to the class `User` like `name = "John"`, or via an expression.

The important difference of class fields: **they are set on individual objects**, not `User.prototype` (for example).

MDN uses the words "field" and "property" interchangeably when talking about classes.

### Bound methods with class fields
Remembering that functions in JS have a dynamic `this`, depending on the context of the call, if an object method is passed around and called in another context, `this` won't be a reference to its object anymore.

e.g. this code will show `undefined`:

    class Button {
        constructor(value) {
            this.value = value;
        }

        click() {
            alert(this.value);
        }
    }

    let button = new Button("hello");

    setTimeout(button.click, 1000); // undefined

I believe setTimeout (a function invocation) changes the `this` context of object methods.

You can fix this by binding the method to the object, or passing a wrapper function like `setTimeout(() => button.click(), 1000)`, but you could also use this **class field** syntax:

    class Button {
        constructor(value) {
            this.value = value;
        }
        click = () => {
            alert(this.value);
        }
    }

    let button = new Button("hello");

    setTimeout(button.click, 1000); // hello

The class field `click = () => {...}` is created on a *per-object* basis. There's a separate function for each `Button`, and the `this` references that object. So `button.click` can be passed anywhere and the value of `this` will stay consistent. This is very useful in the browser environment, for event listeners.

NOTE: I think this is because of the `this` behaviour of arrow functions, which may or may not be a class-specific thing (I don't think so though).

### TL;DR
A class is technically the constructor function, while methods, getters and setters are written to the class's prototype.

What this also means that defining methods outside of the constructor will mean those methods are stored on the prototype, not the object itself.

### Private field declarations
Preceding a variable with `#` will mean that you get an error when trying to reference that property from outside the constructor.

    class Rectangle
        #height = 0;
        #width;
        constructor(height, width) {
            this.#height = height;
        }

### static
When declaring a class field with the word `static` in front, e.g. `static this.value = 3;`, this property will only be able to be directly accessed by the class itself, not on any instances of it. 

Static methods are often utility functions (e.g. create/clone objects), whereas static properties are useful for cacheing/configuration, or any data that doesn't need to replicated across instances.

Static fields without initializers are initialized to have an `undefined` value.

To call a static method, you need to use the name of the original class, and not any of its instances.

#### Static blocks
These allow you to perform additional static initializations during the evaluation of class definitions. A class can have any number of static initialization blocks in its body, evaluated in the order they are declared. Any static initialization in super classes is performed before its subclasses.

In a static block, `this` refers to the constructor object of the class.

### Class extends
To create a class that is a child of another class:

    class ChildClass extends ParentClass

Any constructor that can be called with `new` (meaning it has the prototype property) can be the candidate for a parent class. This includes declaring a class right after the `extends` keyword.

`extends` sets the prototype for both `ChildClass` and `ChildClass.prototype`.

**NOTE**: The base class (parent) may return anything from its constructor, but the derived class must return an object or `undefined`, or you'll get a TypeError.

If the parent class returns an object, that object will be used as the `this` value for the derived class when initialising class fields.

Example of class extends:

    class Square extends Polygon {
        constructor(length) {
            // Here, it calls the parent class' constructor with lengths
            // provided for the Polygon's width and height

            super(length, length);
            // Note: In derived classes, super() must be called before you
            // can use 'this'. Leaving this out will cause a reference error.

            this.name = 'Square';
        }

        get area() {
            return this.height * this.width;
        }
    }

You can also extend built-in objects, such as `Date`.

    class myDate extends Date {
        getFormattedDate() {
            const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
            return `${this.getDate()}-${months[this.getMonth()]}-${this.getFullYear()}`;
        }
    }

### super
The `super` keyword is used to access properties on an object literal or class's Prototype, or invoke a superclass's constructor. It can be used as a function call `super(...args)` or a property lookup `super.prop`/`super[expression]`.

**NOTE**: `super` is a keyword, It is NOT a variable that points to the prototype object. Reading `super` itself will be a SyntaxError.

**A superclass is the parent class of a class.**

### Mix-ins
Abstract subclasses, or *mix-ins*, are templates for classes. An ECMAScript can only have one superclass, so functionality must be provided by the superclass.

A function with a superclass as an input and a subclass extending that superclass as an output can be used to implement mixins in ECMAScript (JavaScript).

    const calculatorMixin = (Base) => class extends Base {
        calc() { }
    };

    const randomizerMixin = (Base) => class extends Base {
        randomize() { }
    };

Then you can write a class that uses these mix-ins:

    class Foo { }
    class Bar extends calculatorMixin(randomizerMixin(Foo)) { }

## Function declaration vs expression

Function declaration:

    function myFunction() {
        // code
    }

Function expression:

    const myFunc = function() {
        // code
    }

    const myArrowFunc = () => {
        // code
    }

Named function expression (referring to the current function inside the function body):

    const math = {
        factit: function factorial(n) {
            console.log(n)
            if (n <= 1) {
                return 1;
            }
            return n*factorial(n-1);
        }
    };

    math.factit(3); // 3;2;1

Unnamed function (assigned to x, usage `x(y)`):

    const x = function(y) {
        return y * y;
    };

This is most commonly used as a callback.

The differences between function declarations and expressions: 
- in expressions, the function name can be omitted to create an anonymous function.
- function declarations are hoisted, expressions are not.

## Promises
A promise:
- essentially is an object that might produce a value in the future
- is a placeholder for an async operation.
- returns a `Promise` object on *execution*, in which you can register callbacks that will be executed on *completion* (success or failure) of the async operation.

This example uses XMLHttpRequest. I use fetch API which automatically returns a promise, but I think it's good to be aware that this structure exists.

    // Make a promise
    function get(url){
        return new Promise(function(resolve, reject) {
            let xhttp = new XMLHttpRequest();
            xhttp.open("GET", url, true);
            xhttp.onload = function() {
                if (xhttp.status == 200) {
                    resolve(JSON.parse(xhttp.response));
                } else {
                    reject(xhttp.statusText);
                }
            };
            xhttp.onerror = function() {
                reject(xhttp.statusText);
            };
            xhttp.send();
        });
    }

    // Use a promise
    var promise = get("data/tweets.json");

    promise.then(function(tweets) {
        console.log(tweets);
        return get("data/friends.json");
    }).then(function(friends) {
        console.log(friends)
    }).catch(function(error) {
        console.log(error)
    });

In order to utilise a promise, you'll want to return a Promise object (if your method does not already do that).

    function get(url) {
        return new Promise((resolve, reject) => {

        })
    }

#### Resolving vs fulfilling
**Please note:** resolving and fulfilling are NOT the same thing.

Resolving is when a promise is settled/locked in to match the eventual state of another promise, and further resolving/rejecting has no effect. Resolved promises can be pending or rejected as well. See:

    new Promise((resolveOuter) => {
        resolveOuter(
            new Promise((resolveInner) => {
                setTimeout(resolveInner, 1000);
            })
        )
    })

What is this code snippet doing?

This code is creating a new Promise, which resolves to create ANOTHER promise, which then waits until after 1000ms before resolving.

Because `resolveOuter` is called synchronously, the status of this first Promise is already *resolved* when it's created. HOWEVER, it has resolved with another Promise, therefore it won't be *fulfilled* until 1 second later, when the inner promise *fulfills*.


### .catch()
It's good to have a `Promise.catch()` to terminate your promise chain.

### .finally()
Usage:

    promise.then(() => )
    .then(() => )
    .catch((() => ))
    .finally();

`Promise.finally()` runs regardless of the outcome of the previous promise, so you should use it to run cleanup or code that you'd like to run that does not depend on previous outcomes.

### Things to keep in mind when you encounter a promise
- Always return results! Otherwise, callbacks won't catch the result of a previous promise, resulting in a "floating" promise (no way to track its settlement anymore)

When async code executes, it will execute with the value that it has at the time. This sounds obvious, but it means that if you simply have `(data) => array.push(data)` as the callback in a `.then()`, the next `.then()` will NOT have access to this result, so if `array` was declared as an empty array OUTSIDE of your promise chain, then accessing `array` in the next `.then()` will access the initial empty array. This is reminding me of "stale references" in React.

    const listOfIngreds = [];

    doSomething()
        .then((url) => {
            // We didn't return this
            fetch(url)
                .then((res) => res.json())
                .then((data) => {
                    listOfIngreds.push(data);
                });
        });
        .then(() => {
            console.log(listOfIngreds); // Always []
        })

In this example, `fetch` returns a Promise object, but only for a chained `.then`. We didn't return anything for the outer `.then` to pass to the next part of the chain.

## JavaScript Event Loop

### Call Stack
A data structure which records where in the program we are currently.

If we step into a function, we push to the top of the stack. If we return from a function, we pop off the top of the stack.

#### Example

    function multiply(a, b) {
        return a * b;
    }

    function square(n) {
        return multiply(n, n);
    }

    function printSquare(n) {
        let squared = square(n);
        console.log(squared);
    }

    printSquare(4);

So first, we'll have `main()` function added to the stack, kind of like the file itself.

Then, the first function call encountered get pushed onto the stack. This is `printSquare`. 

Then, in `printSquare`, we encounter `square`, adding that on top of the stack.

Then, because `square` calls `multiply`, we put that on the stack.

In `multiply`, we have a return statement and we aren't invoking any other functions. So we execute this, and pop `multiply` off the top of the stack.

Then, we next encounter the return statement in `square`, so we resolve that and pop `square` off the stack too.

Next, we return to `printSquare`, and finish executing the first line where we called `square`. Then we encounter `console.log(squared)`, and push that on top of the stack. Then, it's executed as is, so it goes off the top of the stack.

Then, we're done with `printSquare` (there's an implicit return because we're at the end of the function), so pop it off the stack as well. And then we're done with `main` so that goes off too. Now the call stack is empty.

#### Stack trace
We see the call stack visualised when we encounter an error in the console of a browser (what caused the error -> function where the error was caused -> what called that function -> etc), this is the stack trace.

#### Blowing the call stack

    function foo() {
        return foo();
    }

    foo();

The call stack will look like this:

    foo();
    foo();
    foo();
    foo();
    main();

 With `foo();` being added infinitely to the call stack.

 The browser will kill the document if this occurs, which is also called "blowing the stack".

 ### Blocking
 When a slow piece of code is on the call stack, the call stack is "blocked". e.g. a very large while loop (which is synchronous). Most "blocking" methods in browsers are asynchronous though.

JavaScript is a **single-threaded** language, which means it can only do one thing at a time at runtime. If the call stack is blocked (e.g. with a very large synchronous function invocation), the user can continue to interact with what's on the browser but they won't receive any feedback from it, because things won't be re-rendering.

However, the browser is not just the JavaScript runtime. This is why browsers can handle a fetch request while still repainting the UI.

Here's a very nice diagram that I worked hard to create.

             JS Runtime                     WebAPIs
    _____________    _____________      _________________
    |    heap    |  |    stack   |     |  DOM(document)  |
    |            |  |            |     |_________________|
    |            |  |            | --> |   ajax request  |
    |            |  |            |     |_________________|
    |            |  |____________|     |    setTimeout   |
    |            |  |    foo()   |     |_________________|
    |            |  |____________|              |
    |            |  |    bar()   |              |
    |            |  |____________|              |
    |            |  |    baz()   |              |
    |            |  |____________|              |
    |            |  |    main()  |              |
    |____________|  |____________|              v

            event loop    ^
                          |        callback queue
                        ___\____________________________________________
                         _______   _______   _______
                        |       | |       | |       |
                        |onClick| |onClick| |onClick|
                        |_______| |_______| |_______|
                        ________________________________________________

The `heap` is an area of memory where objects are stored when we define variables.

The WebAPIs are supplied by the browser for developers to use to make complex processes. The ones shown are just examples, there are many.

### Event loop & callback queue
Applying this example to the call stack:

    console.log('Hi');

    setTimeout(function cb() {
        console.log('there');
    }, 5000);

    console.log(':)');

First (assuming `main()` is already on the stack), `console.log('Hi');` is pushed to the stack, then executed, then popped off.

Then, the `setTimeout` callback `cb` is pushed onto the stack (on top of `main()`). This kicks off a timer using the setTimeout web API for 5 seconds. This is handled outside of the call stack, so the `setTimeout` call is considered complete and popped off the stack, *even though the timer hasn't completed yet*.

Then `console.log(':)')` is added on top of the stack, executed, then popped off the stack.

Then, since we're done with this `main()`, that goes off the stack too.

After any Web API finishes doing whatever task it's doing, it pushes the task to the **callback queue**. We can't have the Web APIs send their result directly to the call stack, as it would just appear in the middle of whatever task, which would be bad. That's what the callback queue, and the **event loop**, are for.

So, after the 5 second timer finishes, the web API sends `cb` to the callback queue, for the event loop to manage. The event loop has only one job: if the call stack is empty, it takes the first task in the callback queue, and pushes it onto the stack. In this case, the stack was already empty, so the `console.log` runs, then it gets popped off the stack, and we're done.

### The event loop
If you want to defer something until the stack is clear, you can use `setTimeout(callback, 0)`, to take advantage of the fact that the callback will be delayed until the stack is next empty.

AJAX requests work in the same system. As the code for AJAX requests lives in the browser, not in any of your JS files, your `get` request will be shunted off to be handled by a web API and the stack can continue, regardless of when the request completes, or whether or not the request completes successfully at all!

Let's say we have a button, and we add an event listener for the `click` event, with a callback that runs a simple `console.log`. When you click on this button, the web API first registers the click, then adds the callback to the callback queue. When the stack is empty, this callback gets added to the stack.

Web APIs are inherently async.

### Async APIs
Due to the way the event loop works, setTimeout (and other browser APIs) can't guarantee a set order/time of execution. A setTimeout with `4000` can only guarantee that it will be executed after a MINIMUM of 4000ms, not that it will execute after 4000ms passes.

### Render queue
This whole event loop sequence is happening alongside the browser queueing renders. The browser wants to repaint the screen every frame, at a rate of 60fps, but it's constrained by what we're doing in JS. Similarly to the callback queue, the browser can't perform a render until the call stack is clear.

The difference is that the renders are given a higher priority than the callback queue. So the browser queues a render every 16ms (the rate at which 60fps repaints would happen), then waits until the stack is empty to execute the render.

So, while a large synchronous task is occurring, the call stack will be blocked, so the render is also blocked. This means that on the user's end, the browser will be "hanging".

But, when doing async operations, the call stack is only blocked while we're queueing up async callbacks, which is usually relatively quick. After that, the browser will render. Then, something from the callback queue will be pushed onto the stack. Then, another render will occur. etc.

Be careful about "flooding the queue", e.g. having an event listener for scrolling without debouncing. 

## async await
Usage:

    async function getPersonInfo(name) {
        const people = await server.getPeople();
        const person = people.find(person => { return person.name === name });
        return person
    }

`async` keyword lets JS know we are declaring an asynchronous function. This is required to use `await` inside any function. A function declared with `async` **automatically returns a promise**. Returning in an `async` function is the same as resolving a promise. Similarly, throwing an error will reject the promise.

**NOTE**: `async` functions are syntactical sugar for promises.

You can use `async` before any type of function signature.

    const func = async () => {
        // do something asynchronously and return a promise
        return result;
    }

    array.forEach(async item => {
        // do something asynchronously for each item
        // You can also use .map to return an array of promises to use Promise.all()
    })

    server.getPeople().then(async people => {
        people.forEach(person => {
            // do something asynchronously for each person
        });
    });

`await` tells JavaScript to wait for an async action to finish before continuing the function, like a "pause until done" keyword. It's used to get a value from a function where you would normally use `.then()`. Instead of calling `.then()`, you simply assign the result to a variable using `await`. Then you can use the result in your code as if it is synchronous.

### Handling errors
Because `async` functions return a promise, you can just call the function and put `.catch()` on the end.

    asyncFunc().catch( error => {
        console.log(error);
    })

You can also use the `try/catch` block, if you want to handle the error directly inside the function.

    async function getPersonInfo(name) {
        try {
            const people = await server.getPeople();
            const person = people.find(person => { return person.name === name })
            return person;
        } catch (error) {
            // Handle the error any way you want
        }
    }

This way can look messy, but it's an easy way to handle errors without `catch`.

## Promises

Interesting structure I encountered:

**What is the difference between these 4 promises?**

    doSomething().then(function() {
        return doSomethingElse();
    })

    doSomething().then(function() {
        doSomethingElse();
    })

    doSomething().then(doSomethingElse())

    doSomething().then(doSomethingElse)

It looked confusing at first. BUT, each of these have distinct outcomes.

1. This executes `doSomething()`, then executes `doSomethingElse()` *with no arguments*
2. This example executes `doSomethingElse()`, again with no arguments, as expected in the promise chain, but the `.then()` handler returns `undefined`, because we did not return anything. The result of `doSomethingElse()` is not caught ( a "floating" promise?).
3. So this one is executing `doSomething()`, BUT THEN before resolving it, executing `doSomethingElse()` *with no arguments* (the brackets invoke the function), THEN the result of `doSomethingElse()` is passed through as the `.then()` handler. You basically don't want to do this.
4. This one is basically 

    doSomething().then(function(result) {
        return doSomethingElse(result);
    })

The fulfilled value of `doSomething()` is passed into `doSomethingElse` as its *first* argument, because there are no brackets specified (which would explicitly indicate that no arguments are to be passed).

### Why promises always execute before setTimeout callbacks
Alongside the task queue (aka ScriptJobs queue) that we learned about when studying the event loop, there is also the "microtask queue", aka PromiseJobs queue. Basically, all promise callbacks go into this queue when they are handed off by the Web API.

The important thing to note is that the event loop prioritises **emptying** the microtask queue before reaching for the next task. Another way of saying this is that all microtasks queued during a task will be prioritised before the next task is queued. 

**NOTE**: The event loop prioritising microtasks *includes* any microtasks that these current microtasks may queue. This means you need to be careful if you're going to queue microtasks from microtasks; you may get performance issues. 

## `undefined` vs `null` vs undeclared
A variable that has not been declared yet (isn't even in this script basically).

`undefined` indicates that the variable does exist, but has not been assigned a value yet.

`null` means that the variable HAS been assigned a value, but (intentionally or not) this value is "nothing", or the absence of a value. It's useful for purposefully assigning something as empty/not having a value. If you're debugging, `null` may indicate that an attempt to assign a value has occurred, but failed.

## `var`, `let`, `const`
`var` is **function scoped**. A `var` variable can be accessed anywhere within a function where it has been declared.

A variable declared with `var`, if accessed before it has been assigned a value, CAN be accessed, but will have the value of `undefined`. However this only applies if it is accessed within the correct scope (anywhere within a function where it exists).

    (function myFunc() {
        console.log(a) // Has value of 'undefined'
        if (true) {
            var a = "yeet"
        }
        console.log(a) // Will print "yeet"
    })();

    console.log(a) // ReferenceError: a is not defined

`let` and `const` are **block-scoped**. They can only be accessed WITHIN the block that they were declared in.

    (function myFunc() {
        console.log(a) // ReferenceError: a is not defined
        if (true) {
            let a = "yeet"
            console.log(a) // "yeet"
        }
        console.log(a) // ReferenceError: a is not defined
    })();

    console.log(a) // ReferenceError: a is not defined

The difference between `let` and `const` are that `const` values CANNOT be reassigned. e.g. `const array = [1, 2, 3]` you CAN change the values within the array, but you can't do `array = [4, 5, 6]`.

## THe `=` operator and assignment expressions

From [these questions](https://github.com/h5bp/Front-end-Developer-Interview-Questions/blob/main/src/questions/coding-questions.md), I was particularly interested in the solutions for these two problems:

1. What is the value of foo.x?

    var foo = {n: 1};
    var bar = foo;
    foo.x = foo = {n: 2};

Solution: `undefined`

2. What will the code below output to the console and why?

    (function(){
        var a = b = 3;
    })();

    console.log("a defined? " + (typeof a !== 'undefined'));
    console.log("b defined? " + (typeof b !== 'undefined'));

Solution: `a defined? false` and `b defined? true`

I didn't know the answers to either of these so I thought I'd take notes on them.

### Question 2, the `=` operator
Let's talk about 2 first. Inside the IIFE, we have `var a = b = 3`. The `=` operator has *right to left* precedence. So it's actually `b = 3`, `a = b`.

It's also important to know that in JavaScript, you CAN declare variables without a keyword in front of them. These variables are **global**. So what actually happened is that first, we evaluated `b = 3`, creating the global variable `b` and assigning it the value of `3`. Then, we assigned `a` the value of `b`, also 3.

So, outside of the IIFE, `var a` is `undefined`, because `var` variables are restricted by function scope. BUT, `b` IS defined, because it is a global variable.

### Question 1, assignment expressions
Even though `foo.x = foo = {n: 2};` has roughly the same structure as in Q2, this is actually an assignment expression, `(foo.x) = (foo = {n: 2});`. 

> At a high level, an expression is a valid unit of code that resolves to a value. There are 2 types: those with side effects (e.g. assigning values) and those that purely *evaluate*.
> `x = 7` is the first type, using the `=` operator to assign `7` to `x`. The expression itself evaluates to `7` (if you `console.log(x=7)`, you will see `7`).
> `3 + 4` is an example of the second type. It produces `7`, but if it's not part of a bigger construct (e.g. `const z = 3 + 4`), its result is immediately discarded. 

So, in 

    var foo = {n: 1};
    var bar = foo;
    foo.x = foo = {n: 2};

First we create `foo` with the assigned value (an object). `(var foo) = ({n: 1})` We create the object `{n:1}` first, then we create `var foo`, then `foo` is assigned the object.

Then, we create `bar`, a variable that references the same object that `foo` references. This is where I was confused for a bit. `bar` is only POINTING to the same object that `foo` is. The object's existence is not reliant on `foo` pointing to it just because `foo` was created first, and `bar` did NOT create its own independent (deep) copy of this object `{n:1}`. This is important later.

#### Chaining/nesting assignments
[This is the term](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#assignment_operators) for what's happening here. It's also discouraged coding practice because it creates surprising behaviour. I guess that's why it's part of this problem.

So looking at `foo.x = foo = {n: 2}`, this is actually not simply a right to left `=` statement, because it is an assignment expression. It is `foo.x = (foo = {n: 2})`.

In the same way, `var a = b = 3` is actually `var a = (b = 3)`.

**NOTE**: In the case of logical assignments, like `x &&= f()`, `x || f()`, `x ??= f()`, the return value is that of the logical operation without the assignment. e.g `x && f()`.

When chaining these expressions without parentheses or other grouping operations like array literals, they are **grouped right to left** (the assignment operator `=` is "right-associative"), but they are **evaluated left to right**.

Back to the question.

In `foo.x = foo = {n: 2}`:
1. First, `foo.x`, the left hand side, evaluates. So, the property `x` is created on the object assigned to `foo`.
2. Then, `foo = {n:2}` evaluates. To break it down further, the left hand side `foo` is evaluates (no change). Then `{n:2}` is evaluated, creating this object. Then the existing variable `foo` is now assigned to the object `{n:2}`. THEN, the entire expression `foo = {n:2}` evaluates, to a value of `{n:2}`.
3. Back to the whole line. `foo.x = {n:2}` Even though the value of `foo` is now `{n:2}`, `foo.x` evaluated FIRST, and it is referring to the `x` property on the original object that `foo` and `bar` were both pointing to *at the time of evaluation*.

> Because `foo.x` was evaluated FIRST, and it is a reference to where the value of the right hand expression will be assigned to, it is a valid reference within this statement, even though `foo` has been reassigned already.

4. Now, assign the result of the right hand operation `{n:2}` to the reference that `foo.x` resolved to in step 1. Which is the same as `bar.x`.

After all this, `bar` has the value of `{ n:1, x:{n:2} }` (the original object). But, `foo` is now `{n:2}`, so `foo.x` is `undefined` because it doesn't exist.

## Operators
Very tricksy! Don't forget that `++x` and `x++` are different. `x++` increments x FIRST, then evaluates the rest of the statement. `++x` evaluates the code, THEN increments `x`.

The AirBnB style guide actually discourages this because it can create silent errors, and recommends `x += 1` instead.

## `this` again >.>
VERY QUICK re-summary of the original article.

`this` is the context that a function executes within. Its value is not consistent and is determined by how a function is invoked.

1. Regular function invocation `myFunc()` - `this` is the global object (`undefined` in strict mode)
2. Object method invocation `obj.myFunc()` - `this` is the object that owns the method `myFunc()`
3. Constuctor invocation `new obj.myFunc()` = `this` is the object instance created by the `new` keyword
4. Indirect invocation `myFunc.bind(this)`/`myFunc.apply(this, [1, 2, 3])` - `this` is the value of the first argument you passed in

For arrow functions, their `this` resolves *lexically*, meaning it is the object that *defined* the arrow function. Basically, the outer block that the arrow function was defined in.

## Indirect invocation
`.bind(this)` - binds the `this` of a function to the given value.

`.apply(this, arraylikeArg)` - first argument is the specified `this` value, followed by ONE argument, an array or array-like.

`.call(this, ...args)` - first argument is the specified `this` value, followed by *individual* arguments

## Optimisation
Prioritise using JavaScript's higher-order functions instead of loops (e.g `array.map()` instead of `for...in` or `for` loop)

## Arrays
Arrays with empty elements `['a',, 'c']` (NOT undefined/null, but specifically empty) are valid in JS, and they are counted when counting an array's length. Holes in JS arrays are rare though, because JSONs do not support them.

`for...in` and `forEach()` skip empty array elements. `for` and `for...of` do NOT.

`forEach()` does NOT retain the outer scope's value of `this`, while `for/in`, `for` and `for/of` do.

You also can't use `await` or `yield` within a `forEach()`.

> `yield` is used to pause/resume a *generator* function

## Array.prototype.reduce()

Executes "reducer" callback function on each element in the array (in order), passing in the return value from the calculation on the *preceding* element. The final result of running this reducer is a SINGLE value.

The second argument (optional) is an "initial value" that you can supply. Otherwise the element at index 0 is used as the initial value, and iteration starts from the NEXT element (index 1 instead of 0).

> So, if you want the reducer to be run on ALL elements, supply an initial value. Otherwise, index 0 will be used as the initial value.

### Syntax
The accumulator is the result of all preceding executions of the reducer that have occurred so far.

    const array = [1, 2, 3, 4]

    const initialValue = 0;
    const sumWithInitial = array.reduce(
        (accumulator, currentValue) => accumulator + currentValue,
        initialValue
    );

    console.log(sumWithInitial)

Uses:

    reduce((accumulator, currentValue) => { })
    reduce((accumulator, currentValue, currentIndex) => { })
    reduce((accumulator, currentValue, currentIndex, array) => { })

## Switch vs If statements

It seems that the performance differences between `if` and `switch` statements are more or less negligible. But, there are differences:
- The expression to test is only evaluated once in a switch, while it's evaluated for each if. 
- All browsers handle these 2 statements differently, just as all browsers can handle the same piece of code with different performance outcomes. So it's best

## Clone an array in JS (shallow copies)
Arrays in JS are reference values, so copying using `=` like `const fakeSheeps = sheeps` will only copy the reference to the original array, and not the value of the array. They will occupy the same memory space basically. Changing `fakeSheeps` will change `sheeps`.

A shallow copy means *only the first level is copied*. Deeper levels are **referenced**. 

Most conventional ways of cloning arrays (equals `array[i] = sourceArray[i]`, spread `array = [...sourceArray]`, `Array.from()`, `Array.slice()`) will only create a shallow copy of the source array. That is, multi-dimensional arrays or array elements that are actually just references to data will not work as expected. If you change those, you'll have them change elsewhere too. 

To **deep copy** an array OR object, you can do `JSON.parse(JSON.stringify(array))`, which will turn the array into a JSON string and then back into an array. This will create a deep copy, which you can safely alter at any level. However you'll be limited by all the quirks of JSON (array can't contain `undefined` or functions, as they will be converted to `null`). HOWEVER, this method is extremely slow and should not be used unless you absolutely have to.

Note: multidimensional arrays don't actually exist in JS. Arrays/objects/functions/primitives can be inside other arrays. BUT, only primitives will be copied. For all others, the *references* to the object will be copied.

To properly deep copy an object/array, you will have to manually copy each value via recursion, or use a third-party helper like Lodash `cloneDeep`.

### Mutation

You can tell if a method will mutate by whether or not it returns a new array or object. (I assume it returns an object if it will mutate).

Mutations can be "bad" because you're changing the original reference of an object/array. If you use the original reference, you'll get a new value.

    const myObj = { key: "value" }
    const newObj = myObj
    newObj.key = "some other value"
    console.log(myObj) // { key: "some other value" }

You can avoid mutations by using `.slice(start, end)` (end is not included) instead of `.splice(start, deleteCount)` (number of elements you want to remove, inclusive of `start`). Slice returns a new array without mutating the original. Splice returns the new extracted array AND affects the original.

## Merging duplicates in array of objects

I found [this](https://stackoverflow.com/questions/72530675/merge-duplicates-in-array-of-objects-and-mutate-some-properties-javascript) while looking for some other info. It's a pretty specific case, but this kind of thinking seems useful.

## Pure Functions

An essential part of writing JS, for functional programming, reliable concurrency, and React+Redux. 

Functions can serve the following purposes:

- Mapping: produce some output based on given inputs. A function **maps** input values to output values.
- Procedures: A function may be called to perform a sequence of steps. This sequence is known as a procedure (programming in this style is called procedural programming)
- I/O: Some functions exist to communicate with other parts of the system, such as the screen, storage, system logs, or network.

A **pure function**:

- Given the same input, always returns the same output.
- Produces no side effects.

A side effect would be if a function that returns double of the number you pass in ALSO writes to the disk or logs to the console. Let's take `double(x)`, where `double(2)` returns `4`. If this function was pure, you could replace any instance of `4` with `double(2)`. BUT, if `double(2)` saved the value to disk, you could NOT simply replace `double(2)` with `4` without changing the meaning.

If you want referential transparency, you need to use pure functions.

> A giveaway that a function is impure is if it makes sense to call it WITHOUT using its return value. For pure functions you don't want that.

It's highly recommended to use pure functions above other options if practical. 

- They are the simplest building blocks of a program.
- Follows KISS: Keep It Simple, Stupid
- They are completely independent of outside state, and thus are immune to classes of bugs that have to do with shared mutable state.
- Their independent nature makes them great candidates for parallel processing across many CPUs/entire distributed computing clusters, which makes them essential for many resource-intensive computing tasks.

e.g. In the case of users typing in a search bar for a music web player, the user typed faster than the API autocomplete search response. So, race conditions would be triggered, where newer suggestions would be overwritten by outdated search suggestions, because each AJAX success handler was given access to directly update the suggestion list displayed to users. Thus, the SLOWEST AJAX request would always blindly overwrite the results, even when the replaced results might have been newer/better.

To fix this issue, create a suggestion manager - the single source of truth that controls the state of the query suggestions. It's aware of a currently pending AJAX request, and when the user types something new, the current pending request is cancelled before a new request is issued, so only a single response handler at a time would ever be able to trigger a UI state update.

Any async/concurrent operation could trigger race conditions, which happen if output is dependent on the sequence of uncontrollable events (network, device latency, user input, etc). If you're using shared state and that state is reliant on sequences which vary depending on indeterministic factors, then the output is essentially impossible to predict = impossible to test or understand properly.

It's important to note that pure functions DO NOT alter any existing data. They take input and return new output WITHOUT mutating the original input. e.g. take in a shopping cart, create a copy, push an item/quantity to the copy, RETURN the new cart.

Importantly, pure functions are readable, testable, **maintainable** code. 

## console.log

5 tips for using `console.log` better.

1. Use object shorthand in `console.log` to gather information better.

    const name = "fish"
    const age = 23
    const job = "developer"
    const hobbies = "reading"

    console.log({ name, age, job, hobbies })

This will output an easily-read object with corresponding key/value pairs.

You can also customize the console output using CSS!

    console.log('hello %cmy friend', 'color: blue; font-size: 30px')

2. `console.warn()` has a special yellow colour flag to make things stand out!

3. `console.error()` can be used to print HTTP request errors instead of `console.log`. It has a special red flag AND prints out the stack of function calls.

4. `console.time()` & `console.timeEnd()`:

You can get the execution time of a piece of code by calculating the 2 time intervals, like so:

    let startTime = Date.now()
    let count = 0

    for(let i = 0; i < 1000000000; i++) {
        count++
    }

    console.log(Date.now() - startTime)

BUT, you can do this instead.

    let count = 0

    console.time()

    for(let i = 0; i < 1000000000; i++) {
        count++
    }

    console.timeEnd()

You can also count multiple pieces of code at once by giving it a flag!

    let count = 0

    console.time('time1')

    for(let i = 0; i < 1000000000; i++) {
        count++
    }

    console.timeEnd('time1')

    console.time('time2')

    for(let i = 0; i < 1000000000; i++) {
        count++
    }

    console.timeEnd('time2')

5. `console.table()` can be used to print out object data into a nice and organised table instead of the raw object format.

## Promise methods

### Promise.all()

- Takes an iterable of promises as an input, and returns a single Promise that resolves to an array of the results of the input promises.

- This returned promise will fulfill when all of the input's promises have fulfilled, or if the input iterable contains no promises.

- It rejects immediately upon any of the input promises rejecting, or non-promises throwing an error, and will reject with this first rejection message/error.

### Promise.resolve()

`Promise.resolve()` "resolves" a given value to a Promise. If the value is a Promise, that promise is returned. If the value is a thenable, `Promise.resolve()` will call the `then()` method with 2 callbacks prepared; otherwise, the returned promise will be fulfilled with the value.

Writing the behaviour of Promise.resolve from scratch:

    Promise.myResolve() = function (value) {
        // If value is a Promise, return it directly
        if (value && typeof value === 'object' && (value instanceof Promise)) {
            return value
        }

        // Otherwise, all other cases are wrapped again by a Promise
        return new Promise((resolve) => {
            resolve(value)
        })
    }

### Promise.reject()
Returns a Promise object that was rejected with a given reason.

### Promise.race()
Takes an iterable of promises, and fulfills or rejects AS SOON AS one of the promises in the iterable fulufills or rejects.

### Promise.allSettled()

If you have an iterable of promises, and you want to know the results of all of them, use this. It takes an iterable of Promises and returns an array of objects that each describe the outcome of each Promise.

It can be used when you have multiple async tasks that are not dependent on one another to complete successfully.

### Why I can't do Promises properly

If you have a function

    function getData(url){
        return fetch(url)
                .then(response => response.json())
                .catch(error => console.error(error))
    }        

Note that we are **RETURNING** a fetch request. This means you're returning a Promise object, NOT the data itself. The function `getData()` will return a promise, so you'll need to `.then()` to access the data. [Article here](https://stackoverflow.com/questions/44644138/return-the-result-value-with-fetch-call-function-from-another-page-react-native).

However, this doesn't work when I introduce caching into the mix (i.e. cloning response, caching response, returning the response clone, then in the next step doing `.json()`). Even though the data is fetched, `response.clone()` returns `undefined`.

It seems better to just do `.then(result => processData(result))` as part of the promise chain. There's no confusion there at all.

#### `.then()`

I may have solved this dumb problem that I've been struggling with for ages.

BASICALLY, I can't do 

    let fetchReq = makeHTTPRequest(url)
    fetchReq.then(res => res)
    console.log(fetchReq)

This will return a Promise that never resolves (or undefined).

I wanted to make a reusable function that checks the cache and then returns `.json()` Promises so I can use the data in other places. Because otherwise you'll be locking the function to only one callback, which is dumb.

The solution is to do the Promise chain up until you return a JSON Promise. Then, whenever you want in your code, you can feed the data into the next callback using `.then()`, like `makeHTTPRequest(url).then(res => {processData(res)});`.

You CANNOT assign this to a variable and then do anything with that variable, unless you have an `async` function, as your variable will be `undefined` at the time of calling (because it's synchronous).

    form.addEventListener('submit', (event) => {
        event.preventDefault();

        makeHTTPRequest(url).then(res => {processData(res)});
    })

    function processData(data) {
        console.log(data);
    }

    const makeHTTPRequest = async (url) => {
        // First, check the cache
        const cache = await caches.open('cache');
        const existingResult = await cache.match(url);

        if (existingResult === undefined) {
            console.log('FETCHING...')

            return fetch(url)
                .then(response => {
                    let resCopy = response.clone();
                    cache.put(url, response);
                    return resCopy;
                })
                .then(response => {
                    return response.json()
                })
                .catch(error => {
                    console.error(error);
                    displayResults(error);
                })
        } else {
            console.log('cached data found');
            return existingResult.json();
        }
    }

**NOTE**: DON'T FORGET TO RETURN THE FETCH REQUEST!

You need to always return the Promise chain, otherwise the data will fly off into nowhere.

### Using `Promise.all()` actually

- Takes an iterable of Promises
- Returns an iterable of Promises
- Rejects when first Promise rejects, with that error message

    Promise.all(
        data.results.map(item => makeHTTPRequest(item.url))
    )
        .then(res => {
            return res.map(item => makeHTTPRequest(item.item.url))
        })
        .then(res => {
            console.log(res) // See output
            let result = res.map(item => item.sprites)
            return result
        })
    
    // Output: an array of Promises which will resolve to be undefined

This code DOES NOT work. This because in the first `.then()`, I'm returning an array of Promises, which is not a Promise in itself. So in the next step we can't do anything with the data. It's just an array.

The solution: **wrap all chained arrays of Promises inside Promise.all()**.

    Promise.all(
        data.results.map(item => makeHTTPRequest(item.url))
    )
        .then(res => {
            return Promise.all(res.map(item => makeHTTPRequest(item.item.url)))
        })
        .then(res => {
            let result = res.map(item => item.sprites)
            return result
        })

#1 most important thing to remember: always check that you're returning a Promise and following it up with `.then`.

## Array methods

- `.map()` - transforms an array into an array of the samem length with each item transformed
- `.filter()` - transforms an array into a smaller array based on criteria
- `.reject()` - the opposite of `.filter()`
- .`find()` - does the same as `.filter()` but only returns the first item

## Reducers

Array.reduce is commonly used to iterate a list, applying a function to an accumulated value along the way, then returning the singular result. But, it has many uses actually! It's important to understand reduce when working with datasets.

How to use:

    array.reduce(
        reducer: (accumulator: Any, current: Any) => Any,
        initialValue: Any
    ) => accumulator: any

Rewriting this bc I got confused:

    array.reduce(
        reducer: (accumulator, current) => reducer function ,
        initialValue
    ) => accumulator: any

Let's sum an array:

    [2, 4, 6].reduce((acc, n) => acc + n, 0) // 12

    [2, 4, 6].reduce(
        (acc, n) => acc + n,
        0
    )

The job of the reducer is to "fold" the current value into the accumulated value, via the reducer function. The reducer returns the new accumulated value, and `reduce()` moves on to the next value in the array.

Note: if an initial value is not supplied, the initial value used is the first array element. Meaning, the reducer function starts at the second array element.

In the above, we passed in an anonymous reducing function, but we can abstract it and name it:

    const summingReducer = (acc, n) => acc + n;

    [2, 4, 6].reduce(summingReducer, 0); // 12

By the way. JS also has `.reduceRight()`, which works right to left; as in, in the above, the first iteration would use `6` as `n`, then `4`, then `2`.

You can define `map()`, `filter()`, `forEach()` and other things using `.reduce()`.

Map:

    const map = (fn, arr) => arr.reduce((acc, item, index, arr) => {
        return acc.concat(fn(item, index, arr));
    }, []);

### [Reduce part 2](https://youtu.be/1DMolJ2FrNY)

Let's look at `Array.reduce()` from a different perspective:

    let newArray = oldArray.reduce(function(
            thing you want to create,
            thing you're iterating
        ) {
            // execute code on thing you want to create
            return thing you want to create
        },
            initial object
        )

It basically takes a function and a **starting object**. This can be a number, but it doesn't have to be! That's what makes `Array.reduce()` so powerful.

The function takes 2 arguments: the object you're creating (accumulator), and the thing you're iterating. After each iteration, you want to make sure you return the object you're creating/accumulator.

BUT, you can use it for more than just adding up all the values in an array!

Consider this data:

    mark johansson  waffle iron 80  2
    mark johansson  blender 200  1
    mark johansson  knife 10  4
    Nikita Smith  waffle iron 80  1
    Nikita Smith  knife 10  2
    Nikita Smith  pot 20  3

We can turn this data into an object literal using `Array.reduce()`.

    let output = `mark johansson\twaffle iron\t80\t2\nmark johansson\tblender\t200\t1\nmark johansson\tknife\t10\t4\nNikita Smith\twaffle iron\t80\t1\nNikita Smith\tknife\t10\t2\nNikita Smith\tpot\t20\t3\n`
        .trim() // remove any spaces or lines at the start and end of strings
        .split('\n') // Get array of lines between line breaks
        .map(line => line.split('\t')) // Split each array element into an array of words separated by tabs
        .reduce((customers, line) => {
            // Create keys for each customer
            // We end up with only 2 customers because this is overwritten every time
            // But, ensure that you don't accidentally overwrite any existing duplicates
            customers[line[0]] = customers[line[0]] || [] ;

            customers[line[0]].push({
                name: line[1],
                price: line[2],
                quantity: line[3]
            });

            return customers;
        }, {})

Good functional code is made out of small functions that do 1 thing, which are then chained together, like here.

For this line

    customers[line[0]] = customers[line[0]] || [] 

If you don't do this, and you instead do 

    customers[line[0]] = [] 

What you'll be doing is creating a NEW and empty key each time, regardless of whether or not `customers['mark johansson']` already exists with something in it!

If you write it with the `||` condition, then basically you'll be reassigning the values that already exist to that object property, resulting in no data loss... Unless it doesn't exist yet, **then** you'll create a new empty array to use. *This is especially important if your dataset has duplicate entries in it*.

That line is there because we want to check if that customer name exists or not, and create an object key for that person if they don't exist.

Once we establish the customer we're working on, we then `.push()` (because it's an array of objects) each line's data to that customer.

Just like that, super easy data processing from some plain text! (that I had to reformat by hand to simulate it actually being data from a server...)

Thus, you can process data with duplicate entries using `Array.reduce()`, and transform it into a workable Object.

## console.log trick

If you have an object you want to display nicely in the console (tested on terminal) (this may not work for Maps):

    console.log('output', JSON.stringify(output, null, 2))

The `2` is the indentation spacing.

## Maps

Maps are mostly similar to Objects. But not quite!!

- Do NOT use `Map[key]` to set/get Map properties. You won't be able to do anything with the data. Use `Map.get(key)` and `Map.set(key, value)`
- You can simply use `Map.size` to get the number of properties in a Map, unlike Objects with `Object.keys().length`
- Maps don't have a prototype; they aren't vulnerable to object injection hacks (which, if you want an object, can also be fixed with `Object.create(null)`)
- Keys that are primitive values are unique, e.g. you can only have 1 key that is the value `1`. Different types of the same value ARE allowed as duplicates, so keep that in mind.

I don't think anonymous functions are a good idea for Map keys. They don't return anything.

### Working with Maps
- `let myMap = new Map()` - create Map
- `Map.get(key)` - returns the corresponding value if `key` exists in the map, returns `undefined` if not
- `Map.set(key, value)` - set (unique) key/value pair, returns the Map object.
- `Map.size` - return map length
- `Map.has(key)` - returns `true` if `key` exists within the map, `false` if not
- `Map.delete(key)` - returns `true` and deletes the key if it exists. Returns `false` if it doesn't exist.
- `Map.clear()` - removes all key/value pairs from map (returns `undefined`)

Passing multiple key/value pairs to a new Map can be done using an array of arrays:

    let map = new Map(
        [
            ['Bobby', 13],
            ['Ziggy', 13]
        ]
    )

    let colors = new Map(
        [
            ["tomato", "#ff6347"],
            ["royalblue","#4169e1"],
            ["darkgreen","#006400"] 
        ]
    )

Converting a Map to an array:

- use a spread operator

    let array = [...map]

This will return an array of arrays with the key/value pairs.

    [
        [key, value],
        [1, 'hello'],
        [35, 'myValue]
    ]

- use the Array.from method

> `Array.from(array, function)` performs an operation on each array element and returns a new array with each of the values transformed. But here, it will just return the array of key:pair values.

    let array = Array.from(map)

## [Performance of DOM querying methods](https://dev.to/wlytle/performance-tradeoffs-of-queryselector-and-queryselectorall-1074)

The difference between DOM accessing methods is minimal, e.g `document.querySelector()` and `document.getElementById()`.

- `document.querySelector()` allows for complex/chained selectors
- `document.getElementById()` is actually the fastest one because browsers create a table with unique ids for each element with an `id` attribute, so it's simply a table lookup.

There's only a significant performance difference in large loops (basically, running the same method over and over a LOT, like 5000 times). `document.getElementsByClassName()` (or TagName) will return a HTMLCollection, whereas `document.querySelectorAll()` returns a NodeList.

NodeLists are made by storing each element, in order, in the list (creating the list, tehn filling it up with a loop). HTMLCollections are made by simply registering the collection in a cache. This is much lighter upfront, BUT, with `getElementsByTagName` and `getElementsByTagName`, you have to query the DOM for changes every time an element is accessed.

That said, that process still isn't that slow in the grand scheme of things.

Comment from the article:

    When there is a parent element containing all the queried elements then

        const parentElement = document.querySelector('#parentElementId');
        const childElements = parentElement.querySelectorAll('.selector');

    is about ten times faster than to use

        const childElements = document.querySelectorAll('.selector');

So you can store a parent in a `const` variable, then use that to access any children you need. It's probably more performant.

## Working with DOM NodeList and HTMLCollection

NodeList - from `node.childNodes`, returns ALL nodes including text etc. you can only use forEach

HTMLCollection - from `node.children`, returns elements ONLY. You can't use `forEach` with this, or basically any array method for that matter.

Both are arraylikes, NOT arrays, so to iterate from them, make a shallow copy array from the children using `Array.from(list)`.

## Optional chaining `?.`

Use `?.` so that JS checks if the reference is not `null` or `undefined` before running the rest of the code. Keep in mind that the whole operator is `?.` and is NOT the same thing as putting an `?` right before accessing an object property with `.` This is because you can use it to check if a function exists before calling it:

    let result = myObject.myMethod?.();

From [this question](https://stackoverflow.com/questions/28975896/is-there-a-way-to-check-for-both-null-and-undefined), this is a new feature, **optional chaining**, that uses **nullish coalescing** aka the `??` operator.

The optional chaining operator accesses an obect's property or calls a function. If the object accessed/function called is `undefined` or `null`, then the expression short-circuits and **evaluates to `undefined`, instead of throwing an error**.

e.g. 

    if (data > root.right?.data || data < root.left?.data)

Consider the old way of checking this:

    const nestedProp = obj.first && obj.first.second;

The value of `obj.first` is confirmed to be non-null before accessing `obj.first.second` (to avoid an error if `obj.first` was `null`, then we accessed `obj.first.second`).

However, it can get verbose if the chain is long, and it's not safe: if `obj.first` is a falsy value that is NOT `null` or `undefined`, e.g. `0` or an empty string, it would still short-circuit and make `nestedProp` become `0`, which may be an unintended side effect.

With the `?.` operator, you don't need to explicitly test/short-circuit based on the state of `obj.first` before trying to access `obj.first.second`.

    const nestedProp = obj.first?.second;

JS knows to implicitly check if `obj.first` isn't `null` or `undefined` before accessing `.second`. If `obj.first` is `null` or `undefined`, then the expression immediately short-circuits and returns `undefined`.

This is equivalent to:

    const temp = obj.first;

    const nestedProp =
        temp === null || temp === undefined 
            ? undefined
            : temp.second;

NOTE: `?.` can't be used on a non-declared root object, but can be used with a root object with value `undefined`.

**NOTE**: be aware of creating "silent errors" with this operator.

### Optional chaining with function calls
You can use `?.` to attempt to call a method which may not exist, e.g. using an API which may not be available either due to age of the implementation or a feature that is unavailable on the user's device.

    const result = someInterface.customMethod?.();

However, a property exists with that name that is NOT a function, `?.` will still raise a `TypeError` exception: "someInterface.customMethod is not a function".

Also, if `someInterface` itself is `null` or `undefined`, a TypeError exception will still be raised ("someInterface is null"), because you didn't check that object, only the property. If you expect `someInterface` to also be `null` or `undefined`, you'll have to do

    const result = someInterface?.myMethod?.();

Further reading: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining).

## 2d Arrays

- The first array stores the NAME of the 2D array, plus the height of the total 2D arrays, i.e. columns.
- Each array element is then populated by another array. These arrays are the "rows"
- Each element in a row is where you actually store the data. The initial array is basically the "container".
- Think of the "coordinates" of accessing a 2D Array like `2DArray[row][column]` instead of `2DArray[y][x]`. It's easier to visualise, because you're choosing WHICH ROW you want to to access first (going down the first array), then which column of that row you want (the array element of that row).

The standard way to create a 2D array:

    const board = [];

    for (let i = 0; i < boardSize; i++) {
        let secondArr = [];
        for (let j = 0; j < boardSize; j++) {
            // Init second dimension of arrays
            secondArr[j] = null;
        }
        // Assign each second dimension array to board[i];
        board[i] = secondArr;
    }

## RequestAnimationFrame

Native function. Takes only 1 arg, the callback.

Your callback routine itself must call `requestAnimationFrame()` again if you want to animate another frame at the repaint.

**NOTE**: You CAN use setInterval to do animations, but apparently `requestAnimationFrame()` is better and more optimised.

- Animations in inactive tabs will stop

    setInterval(() => {
        // animation here
    , 1000/60})

    function smoothAnim() {
        // animation here
        requestAnimationFrame(smoothAnim)
    }
    requestAnimationFrame(smoothAnim);

Just like setTimeout/Interval, `requestAnimationFrame()` **returns an ID you can call to stop it.** You can do this using `cancelAnimationFrame(id)`

    let animId;
    
    function smoothAnim() {
        // animation here
        requestAnimationFrame(smoothAnim)
    }

    function startAnim() {
        animId = requestAnimationFrame(smoothAnim);
    }

    function endAnim() {
        cancelAnimationFrame(animId);
    }

Much like with setTimeout/setInterval, `requestAnimationFrame`, when activated on an event, can be done repeatedly (you can have many IDs but only manage to store one). So be sure to disable this behaviour.

### Automatically passes a timestamp!

The callback method that you pass into requestAnimationFrame() is passed a single argument: a timestamp that indicates the current time, based on the number of ms since time origin (basically when the page was loaded or something like that).

So you can store the start time in an external variable (`if (!start) start = timestamp`), and then calculated total elapsed time using this value. Or, you get get time since last animation frame call by storing `prevUpdate = timestamp` externally.

Time between 2 calls: `currentElapsed = timestamp - prevUpdate`.