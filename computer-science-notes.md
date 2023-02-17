# Computer Science Notes

Notes for concepts that are not web-specific.

## Debouncing
If something is triggered multiple times in very short succession, but you don't actually need that thing to be triggered that many times (e.g. a callback while scrolling), you can debounce it to improve performance. That is, delaying when you can next trigger something either by time, or something else like a boolean switch.

## Lazy evaluation
Lazy evaluation, or call-by-need, is when you delay the evaluation of an expression until the value is actually needed.

Often combined with *memoization*. After a function's value is computed for that parameter or set of parameters, the result is stored in a lookup table, indexed by the value of those parameters. Then, the next time the function is called, the table is consulted to determine whether or not the result for this combination of parameters already exists. If so, the stored result is returned. If not, the function is evaluated and the result is added as another entry in the table.

Many languages have mechanisms for lazy evaluations called "promises". But not JavaScript. To do lazy evaluation in JS, consider using a function with no arguments like `const f = () => expression` (using `f()`) to evaluate immediately).

## What are algorithms
For most websites, you probably won't need algorithms. BUT, if your website gains a large amount of users (e.g. a search engine), it will struggle to handle the volume of requests.
Algorithms let you figure out **efficient ways to handle large amounts of data**.

They also let you handle complicated transformations of data. e.g. developing a CAPTCHA: you can generate an image and output wavy letters with basic programming skills. BUT, with algorithms, you'll be better equipped to generate images with blur/ripples/reflection/noise etc. Or, collaborative editing in Google docs. It's too complex to bust out a solution just like that - you need to have a process to get you from one state to another.

Due to the nature of the web today, algorithms are an essential characteristic of modern web apps. Everything needs to be highly scalable to accommodate for a large user base. You also need to support a lot of data searching, browsing, updating, deleting. These processes need to be extremely efficient per case.

## Pseudocode
An English-like way to state an algorithm. 

e.g. Write a pseudocode solution for taking in two integer values and outputting the sum and product of those numbers.

    Prompt the user for number 1
    Save input to num1
    Prompt user for number 2
    Save input to num2

    sum = num1 + num2
    product = num1 + num2

e.g. Write pseudocode for: take in integer and determine whether it's odd or even

    Prompt user for number
    Save number to num
    Divide num by 2

    odd: num % 2 = 1
    print "odd"

    even: num % 2 = 0
    print "even"

It's important to be able to think in pseudocode.

## Recursion
Recursion is *the idea that a function calls itself*. It's used to take a bigger problem and start breaking it down into smaller and smaller pieces, continuing to feed their solutions back into the original function until some sort of answer is achieved. Thi is the concept of "Divide and Conquer" algorithms (D&C).

- Multi branched recursion
- Break a problem into two or more sub-problems of the same/related type, until they become simple enough to solve directly
- Then, the solutions to the sub-problems are combined to give a solution to the original problem

There is a *right* and a *wrong* way to use recursion. Any problem that you can solve recursively can also be solved using iterators. If you find yourself saying "why didn't I just use a `while` loop here?" then you probably should have. Some problems also break down into far too many pieces and overwhelm the computer's memory. So there's a balance.

When to use recursion:
- When a task can naturally be split into several tasksk of the same kind, but simpler
- When a task can be simplified into an easy action + a simpler variant of the same task
- To deal with certain data structures

### 2 ways of thinking

Consider the function `pow(x,n)`.

    pow(2,2) = 4
    pow(2,3) = 8
    pow(2,4) = 16

There are 2 ways to implement this.

1. Iterative thinking: the `for` loop

    function pow(x, n) {
        let result = 1;

        // multiply result by x n times in the loop
        for (let i = 0; i < n; i++) {
            result *= x;
        }

        return result;
    }

2. Recursive thinking: simplify the task and call self:

    function pow(x, n) {
        if (n === 1) {
            return x;
        } else {
            return x * pow(x, n -1);
        }
    }

For the recursive function, when `pow(x, n)` is called, the execution splits into 2 branches:

                if n==1, = x
                /
    pow(x, n) = 
                \
                else, = x* pow(x, n-1)

1. if `n===1`, it immediately produces the obvious result. This is called the "base" of recursion.
2. Otherwise, perform the recursive step. We represent `pow(x, n)` as `x * pow(x, n -1)`. In maths, one would write `(x^n) = x * (x^n)-1`. We are transforming the task into a simpler action (multiplication by `x`) and a simpler call of the same task (`pow` with lower `n`). Next steps simplify it further and further until `n` reaches `1`.

In other words, `pow` recursively calls itself until `n === 1`.

To calculate `pow(2,4)`...

    pow(2, 4) = 2 * pow(2, 3)
    pow(2, 3) = 2 * pow(2, 2)
    pow(2, 2) = 2 * pow(2, 1)
    pow(2, 1) = 2

So the recursion reduces the function call to a simpler one, then even simpler, until the result becomes obvious.

> A recursive solution is usually shorter than an iterative one. 

> Here, we can rewrite the same function using `?` instead of `if` to make `pow(x, n)` more terse and still readable.

    function pow(x, n) {
        return (n === 1) ? x : (x * pow(x, n - 1));
    }

The maximal number of nested calls (including the first one) is *recursion depth*. In our case, it will be exactly `n`.

The maximal recursion depth is limited by the JS engine. We can rely on it being 10,000 - some engines allow more, but 100,000 is probably out of limit for the majority. There are optimizations that help alleviate this ("tail call optimizations"), but they aren't supported everywhere and only work in some simple cases.

That limits the application of recursion, but it still remains wide. There are many tasks where a recursive way of thinking gives simpler/more maintainable code.

### Recursive functions
A recursive function is like a loop, in the sense that ther is an *exit condition*.

    function countDown(n) {
        for (let i = n; i > 0; i--) {
            console.log(i)
        }
            console.log('Done')
    }

    function countDownRecursive(n) {
        if (n <= 0) {
            console.log('Hooray')
            return
        }
        countDownRecursive(n - 1)
    }

So what's happening here:

    countDownRecursive(3)
        countDownRecursive(2) // These happen INSIDE the previous call
            countDownRecursive(1)
                countDownRecursive(0) // if condition fulfilled
                return
            return // Each of the existing function calls are now returning as well
        return
    return

Obviously this example is not the kind of thing you'd want to use a recursive function on. A more common use is where you take an input and constantly grow it inside your recursive function.

e.g This function returns the sum of all integers where `n >= 0` e.g. sumRange(3) = 6

    function sumRange(n) {
        let total = 0
        for (let i = n; i > 0; i--) {
            total += i
        }
        return total
    }

Keep in mind that any variable you set inside a recursive function is *only available to that instance of that function*. So to rewrite the above as recursive, pass the total into the function too.

We want to have total start at 0 too.

    function sumRangeRecursive(n, total = 0) {
        // exit condition
        if (n <= 0) {
            return total;
        }
        // recursive call
        return sumRangeRecursive (n - 1, total + n);
    }

    sumRangeRecursive(3, 0) // passing the total is optional
        sumRangeRecursive(2, 3)
            sumRangeRecursive(1, 5)
                sumRangeRecursive(0, 6)
                return 6
            return 6 // Subsequent functions return the result of the next call
        return 6
    return 6

You don't use recursion all the time. These 2 examples are ones that you would want loops for, because it's just easier to follow.

BUT, now let's try this use case. We want to print ALL children of this object `tree`.

    const tree = {
        name: 'John',
        children: [
            {
                name: 'Jim',
                children: []
            },
            {
                name: 'Zoe',
                children: [
                    { name: 'Kyle', children: [] },
                    { name: 'Sophia', children: [] }
                ]
            }
        ]
    }

    function printChildren(t) {
        // ??? this is too wild for a normal function
    }

This would be pretty hard to do with a `for` loop.

    function printChildrenRecursive(t) {
        // Exit clause
        if (t.children.length === 0) {
            return
        }
        t.children.forEach(child => {
            console.log(child.name)
            printChildrenRecursive(child)
        })
    }

What's happening here:

    printChildrenRecursive('John')
        printChildrenRecursive('Jim')
        return // Jim has no kids
        printChildrenRecursive('Zoe')
            printChildrenRecursive('Kyle')
            return
            printChildrenRecursive('Sophia')
            return
        return
    return

The output:
    
    Jim
    Zoe
    Kyle
    Sophia

Please note that the function prints all CHILDREN, so John himself is not printed.

### Execution context and stack
To fully understand recursive functions, let's look at how functions work.

One function call has one execution context (e.g. current variables, `this`, where the control flow is) associated with it. When a function makes a nested call:
- The current function is paused
- The execution context associated with it is remembered in a special data structure called *execution context stack*
- The nested call executes
- After it ends, the old execution context is retrieved from the stack

Look at this example:

    function pow(x, n) {
        if (n == 1) {
            return x;
        } else {
            return x * pow(x, n - 1);
        }
    }

    alert( pow(2, 3) );

With the call `pow(2, 3)`, the execution context will store variables `x = 2, n = 3`. The execution flow is at line `1` of the function. You can sketch this as `Context: { x: 2, n: 3, at line 1 } (call: pow(2, 3))`

Next, `n == 1` is falsy, so we go to the second branch of `if`. There, we have a nested function call in `x * pow(x, n - 1)`, which is `pow(2, 2)`. 

Context stack when we entered the subcall pow(2, 2):
- `Context: { x: 2, n: 2, at line 1 } (call: pow(2, 2))`
- `Context: { x: 2, n: 3, at line 5 } (call: pow(2, 3))`

After we finish the subcall, we can easily resume the previous context because all the info was stored. Continue this with all other subcalls.

> Note: you can have multiple "subcalls" per line of code, e.g. `pow(…) + pow(…) + somethingElse(…)`

Once a function call finishes, its execution context is removed from memory and the previous one is restored from the top of the stack.

Throughout this code, there is a recursion depth of `3` (maximal number of context in the stack), because there were 3 total function calls within this one function.

Note the **memory requirements**; contexts take memory. In our case, raising to the power of `n` actually requires the memory for `n` contexts, for all lower values of `n`. A loop based algorithm is more memory saving:

    function pow(x, n) {
        let result = 1;

        for (let i = 0; i < n; i++) {
            result *= x;
        }

        return result;
    }

The iterative `pow` uses a single context changing `i` and `result` in the process. Its memory requirements are small, fixed, and don't depend on `n`.

Any recursion can be rewritten as a loop, and the loop can usually be made more effective. BUT, sometimes the rewrite is non-trivial, especially:
- when a function uses different recursive subcalls depending on conditions and merges their results
- when the branching is more intricate

Also, the optimisation may be unneeded and not worth the effort.

Recursion can give shorter code that is more readable and maintainable. In cases like that, it's worth the memory trade off.

### Recursive traversals

If we have an object with multiple nested objects inside (as we saw in the previous section of notes), an iterative approach would be really hard because the structure is not simple.

Another example:

    let company = {
        sales: [{
            name: 'John',
            salary: 1000
        }, {
            name: 'Alice',
            salary: 1600
        }],

        development: {
            sites: [{
                name: 'Peter',
                salary: 2000
            }, {
                name: 'Alex',
                salary: 1800
            }],

            internals: [{
                name: 'Jack',
                salary: 1300
            }]
        }
    };

- A department may have an array of staff
- A department may split into subdepartments
- A subdepartment may split into sub-sub-departments (teams)

This is wild!

An iterative approach would not be easy because the structure is not predictable or simple. You'd have to write all these nested sub loops to potentially cover future teams.

OR, you could do recursion.

    function sumSalaries(department) {
        // prev = previous salaries added up, current = current array element (object)

        if (Array.isArray(department) {
            // If department is an array, sum array
            return department.reduce((prev, current) => prev + current.salary, 0) 
        }) else {
            // If department is an object
            let sum = 0
            for (let subdep of Object.values(department)) {
                sum += sumSalaries(subdep); // recursively call subdepartments
            }
            return sum
        }
    }

Let's go through the call stack for this.

    sumSalaries(company) // company is an object
        sumSalaries(sales) // sales is an array
            sales.reduce()
        return 2600
    (sum += 2600)
        
        sumSalaries(development) // object
            sumSalaries(sites) // array
                sites.reduce()
            return 3800

            sumSalaries(internals) // array
                internals.reduce()
            return 1300
        return 0 + 1300 + 3800 // result of recursive call
    (sum += 5100)

    return 7700

            

Due to the structure of the given data, the function performs subcalls for an object, and performs addition for arrays (the "bottom level").

> `Object.values` returns an array of objects which we iterate through in the `for` loop. If the current "level" of data is an object, we recursively perform subcalls for each 

I don't think this function accounts for if there are subdepartments AND staff members who exist on the same level (e.g. general development staff who aren't part of a team). As soon as you encounter an array, you know that you'll be able to loop through the staff objects and add the salaries there. 

## Recursive Structures

A recursive (recursively defined) data structure is one that replicates itself in parts. e.g. the company structure above, a company department is:
- either an array of people
- or an object with departments

For web, there are other examples: HTML and XML documents.

In a HTML document, a HTML tag may contain a list of:
- text pieces
- HTML-comments
- Other HTML-tags (that may in turn contain text pieces/HTML comments/other tags)

This is a recursive definition.

## Linked List

Let's cover a data structure that may be a better alternative for arrays in some cases: **linked lists**.

Let's say we want to store an ordered list of objects. The natural choice would be an array:

    let arr = [ obj1, obj2, obj3 ]

BUT, there are issues. The "delete element" and "insert element" operations are expensive. e.g. `arr.unshift(obj)` has to renumber all elements to make room for the new `obj`, and if the array is big, it takes time. Same with `arr.shift`.

The only structural modifications that do NOT require mass renumbering are `push` and `pop` (these work with the end of the array). So an array can be quite slow for big queues if you need to work anywhere else except the end.

If you really need fast insertion/deletion, you can choose a *linked list*, which is recursively defined as an object with:
- `value`
- `next` property referencing the next *linked list* or `null` if it's the end.

For example:

    let list = {
        value: 1,
        next: {
            value: 2,
            next: {
                value: 3,
                next: {
                    value: 4,
                    next: null
                }
            }
        }
    };

Basically:

         value  next  value  next  value  next  value  next 
    list    1    ->     2     ->     3     ->     4     ->    null

Alternative code for creation:

    let list = { value: 1 };
    list.next = { value: 2 };
    list.next.next = { value: 3 };
    list.next.next.next = { value: 4 };
    list.next.next.next.next = null;

You can see that there are multiple objects, each with the `value` and `next` pointing to the neighbour. The variable `list` is the first object in the chain, so following `next` pointers from it, we can reach any element.

The list can easily be split into multiple parts and joined back.

    let secondList = list.next.next;
    list.next.next = null;

Or,

               value  next  value  next 
          list    1    ->     2     ->    null

               value  next  value  next 
    secondList    3    ->     4     ->    null

To join:

    list.next.next = secondList;

To prepend (stick on the front) a new value, we need to update the head of the list.

    let list = { value: 1 };
    list.next = { value: 2 };
    list.next.next = { value: 3 };
    list.next.next.next = { value: 4 };

    // prepend the new value to the list
    list = { value: "new item", next: list };

Now, it's

          value  next  value  next  value  next  value  next  value  next 
    list   "new   ->     1     ->     2     ->     3     ->     4    ->    null
          item"
    
To remove a value, change `next` of the previous one:

    list.next = list.next.next;

Now `1` is excluded from the chain. If it's not stored anywhere else, it will automatically be removed from the memory.

          value  next  value  next  value  next  value  next 
    list   "new   ->     2     ->     3     ->     4    ->    null
          item"
        
Unlike arrays, there's no mass-renumbering, so it's easy to rearrange items.

Obviously, lists are not always better than arrays. The main drawback: you can't easily access an element by its index number, like you can with `arr[i]`. You need to start with the first item and add `.next` `n` times to get the nth element.

In instances like a queue or a "deque" (as in a double-ended queue, NOT "dequeue" the operation), you only need to add things to either end of the list and not the middle, but you need very fast adding/removing from both ends. That's probably where a linked list would come in.

> A double-ended queue is an abstract data type that generalizes a queue, for which elements can be added can be added to either the front/head or the tail/back.

Lists can be enhanced:
- We can add the property `prev` in addition to `next` to reference the previous element, to move backwards easily.

    let list = {
        prev = null,
        value: 1,
        next: {
            prev: list,
            value: 2,
            next: {
                prev: list.next,
                value: 3,
                next {
                    prev: list.next.next,
                    value: 4,
                    next: null
                }
            }
        }
    }

    list.next.next.prev.value = 2

- We can make a variable `tail` referencing the last element of the list (update this when adding/removing elements from the end)
- Vary the data structure according to your needs

### Tasks

From [here](https://javascript.info/recursion#tasks)

**Sum all numbers till the given one**

Write a function `sumTo(n)` that calculates the sum of numbers `1 + 2 + ... + n`.

Make 3 solution variants:
1. `for` loop

    function sumTo(n) {
        let sum = 0;
        for (let i = 0; i <= n; i++) {
            sum += i;
        }
        return sum;
    }

2. Recursion: cause `sumTo(n) = n + sumTo(n-1)` for `n > 1`

    function sumTo(n) {
        if (n > 1) {
            return n + sumTo(n-1);
        }
        return n;
    }

Test:
    value = 3
    sumTo(3)
        3 + sumTo(2)
            2 + sumTo(1)
            return 1
        return 3
    return 6

3. Using the **arithmetic progression** formula

#### What is

An arithmetic progression/sequence is a sequence of numbers such that the difference from any succeeding term to its preceding term remains constant throughout the sequence. The constant difference is called the "common difference" of that arithmetic progression.
If the initial term of an arithmetic progression is called `a` and the common difference is `d`, the `n`th term of the sequence (a(small n)) is given by:

    a(small n) = a + (n - 1)d

If there are `m` terms in the AP, then a small m represents the last term, given by:

    a small m = a + (m + 1)d

The sum of the members of a finite AP is called an arithmetic series.

e.g. 2 + 5 + 8 + 11 + 14 = 40

You can find the sum by taking the number `n` of terms being added (here 5), multiplying it by the sum of the first and last number in the progression (here 2 + 14 = 16) and dividing by 2.

#### The actual answer

Use this formula: 

    (n(first term + last term))/2

In this case, the first term is always 1, because we are adding 1 each time. Also due to this, `n` is the argument, and the last term is also `n`.

    function sumTo(n) {
        return ( (n * (1 + n)) / 2 )
    }

**P.S. Which solution variant is the fastest? The slowest? Why?**
The formula (#3) uses only 3 operations for any number `n`, making it the fastest. Followed by the loop variant. As the recursion involves nested calls and execution stack managment, which also require resources, it's slower.


**P.P.S. Can we use recursion to count sumTo(100000)?**
It depends on the engine but probably not, since recursion is usually limited to about 10000.

Note: some engines support "tail call" optimization: if a recursive call is the last one in the function with no other calculations performed, then the outer function will not need to resume the execution, so the engine doesn't need to remember the execution context, thus saving memory. Most JavaScript engines don't support this though, so you will have an error for maximum stack size exceeded.

**Calculate factorial**

The factorial of a natural number `n` is the number multiplied by `n - 1`, then `n - 2`, and so on until 1. It is written as `n!`

    n! = n * (n - 1) * (n - 2) * ... * 1

Write a function `factorial(n)` that calculates `n!` using recursive calls.

Note: apparently you can't do negative factorials so I won't worry about that.

Hint: `n!` can be written as `n * (n - 1)!`. e.g. `3! = 3 * 2! = 3 * 2 * 1! = 6`

    function factorial(n) {
        if (n === 1) {
            return 1;
        }
        return n * factorial(n - 1);
    }

Oh we can do ternary operators???? Ok

    function factorial(n) {
        return (n === 1) ? 1 : (n * factorial(n - 1))
    }

The basis of recursion is `1`. Making it 0 wouldn't change the outcome, but would add an extra step for no reason.

**Fibonacci numbers**

The sequence of Fibonacci has the formula F(small n) = F(small (n - 1)) + F(small (n - 2))

In other words, the next number is a sum of the two preceding ones. Apparently the first 2 numbers are 1?

    1, 1, 2, 3, 5, 8, 13...
    (1 + 0),(1 + 0), (1 + 1), (2 + 1), (3 + 2), (5 + 3), (8 + 5)

Write the function `fib(n)` that returns the `n`th Fibonacci number. Also the function should be fast??

We know fibonacci is a set sequence.

Each iteration needs to return the `n`th number

What is the exit condition/ the end of the chain? We're working from n until 1, and adding the 2 previous numbers together

My first solution:

    function fib(n) {
        return (n <= 1) ? Math.abs(n) : fib(n - 1) + fib(n - 2)
    }

Well I'm pretty sure this isn't going to be fast. But at least I did it.

So this initial recursive solution is going to be really slow. `fib(77)` may hang for a bit of time. This is because the function makes many subcalls, re-evaluating the same values over and over.

You can optimize this by remembering already-evaluated values: if a value of `fib(3)` is calculated once, we can reuse it in future computations.

OR, you can give up recursion and use a different loop-based algorithm. This feels like it's defeating the purpose of this article but whatever.

Instead of going from `n` to lower values, make a loop that starts from `1` and `2`, then gets `fib(3)` as their sum, then `fib(4)` as the sum of two previous values, then `fib(5)` etc... Until it gets to `n`. 

Written out:

    // a = fib(1), b = fib(2)
    let a = 1, b = 1;

    // c = fib(3) as their sum
    let c = a + b

Now, to get fib(4), let's shift the variables: `a = fib(2)`, `b = fib(3)`, `c = fib(4)`

    a = b; // now a = fib(2)
    b = c; // b = fib(3)
    c = a + b; // c = fib(4)

And so on, until `c = fib(n)`. This is much faster than recursion.

    function fib(n) {
        let a = 1
        let b = 1

        for (let i = 3; i <= n; i++) {
            let c = a + b;
            a = b;
            b = c;
        }

        return b;
    }

The loop starts with `i = 3`, because the first and second sequence values are hard coded.

This is called *[dynamic programming](https://en.wikipedia.org/wiki/Dynamic_programming) bottom-up*.

**Output a single linked list**

Let’s say we have a single-linked list (as described in the chapter Recursion and stack):

    let list = {
        value: 1,
        next: {
            value: 2,
            next: {
            value: 3,
            next: {
                value: 4,
                next: null
            }
            }
        }
    };

Write a function printList(list) that outputs list items one-by-one. Make two variants of the solution: using a loop and using recursion. What’s better: with recursion or without it?

Loop:

    function printList(list) {
        Object.values(list).forEach(item => {
            if (typeof item === 'object') {

                Object.values(item).forEach(item => {
                    if (typeof item === 'object') {

                        Object.values(item).forEach(item => {
                            if (typeof item === 'object') {
                                
                                Object.values(item).forEach(item => {
                                    console.log(item)
                                })

                            } else {
                                console.log(item)
                            }
                        })

                    } else {
                        console.log(item)
                    }
                })

            } else {
                console.log(item)
            }
        })
    }

Recursive:

    function printList(list) {
        console.log(list.value)
        if (list.next) {
            printList(list.next)
        }
    }

**Output a single-linked list in the reverse order**

Use the list from the previous task and output the list in the reverse order. Make 2 solutions: a loop and a recursion.

Recursive:

    function printListInReverse(list) {
        // go to the lowest level first and work back up from there
        if (list.next) {
            printList(list.next)
        }
        console.log(list.value)
    }

    // this printed 2 3 4 1 gfkhgdkhgdkur