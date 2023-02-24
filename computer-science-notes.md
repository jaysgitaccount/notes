# Computer Science Notes

Notes for concepts that are not web-specific. I will be solving all presented problems in JavaScript, but they (usually) aren't JavaScript specific.

- Superscript characters: ¹²³⁴⁵⁶⁷⁸⁹⁰
- Subscript characters: ₀₁₂₃₄₅₆₇₈₉
- Superscript small case letters: ᵃᵇᶜᵈᵉᶠᵍʰⁱʲᵏˡᵐⁿᵒᵖʳˢᵗᵘᵛʷˣʸᶻ
- Subscript small case letters: ₐ ₑ ₕ ᵢ ⱼ ₖ ₗ ₘ ₙ ₒ ₚ ᵣ ₛ ₜ ᵤ ᵥ ₓ
- Big O/Theta: Θ

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

Loop: We can't explicitly go back, so we need to store the values in an array, then reverse for loop to print the values

    function printListInReverse(list) {
        let array = [];
        let tmp = list;

        while(tmp) {
            array.push(tmp.value);
            // Set tmp so we continue through levels
            tmp = tmp.next;
        }

        for (let i = array.length - 1; i >= 0; i--) {
            console.log(array[i])
        }
    }

Recursive:

    function printListInReverse(list) {
        // go to the lowest level first and work back up from there
        if (list.next) {
            printListInReverse(list.next)
        }
        console.log(list.value)
    }

    // this printed 2 3 4 1 gfkhgdkhgdkur

    printListInReverse(list)
        printList(list.next)
            printList(list.next.next)
                printList(list.next.next.next)
                console.log("4")
            console.log("3")
        console.log("2")
    console.log("1")

## Recursion
From [this video](https://youtu.be/mz6tAJMVmfM)

Write a recursive function that uses the Collatz conjecture (positive integers only) to calculate how many steps you need to take to get back to 1:

- if `n` is 1, return the number of steps.
- if `n` is even, repeat this process on `n/2`
- if `n` is odd, repeat this process on `3n + 1`

Function that takes `n` and the number of steps that have occurred so far, default 0.
If n = 1, return n
If n is even (n % 2 === 0), pass n/2 into the next step
If n is odd (n % 2 === 1), pass 3n+1 into the next step
I

    function getSteps(n, steps = 0) {
        if (n === 1) {
            return steps
        } else if (n % 2 === 0) {
            return getSteps(n / 2, steps + 1)
        } else if (n % 2 === 1) {
            return getSteps( (3 * n) + 1, steps + 1 )
        }
    }

Oh, the video has a different way of doing it?

    function collatz(n) {
        if (n === 1) {
            return 0
        } else if ((n % 2) === 0) {
            return 1 + collatz(n/2)
        } else {
            return 1 + collatz (3*n + 1)
        }
    }

Interestingly, instead of defining a variable or storing the steps anywhere, each recursive call returns `1 +  ` the next subcall. So after all the subcalls are done, they would have all resolved to `0`, so EACH CALL that occurred in the stack would then return `1`, and back up the chain they would each add 1 until finally, in the original call, the total number of steps is returned. Very clever!

#### Dynamic programming for overlapping subproblems

For some problems, the branched recursion may end up re-evaluating the same sub-problem over and over, which is not efficient. e.g the Fibonacci problem I did. In these cases, it's better to identify and save the solutions to these overlapping subproblems (memoization). Followed to the limit, it leads to bottom-up divide-and-conquer algorithms like dynamic programming. 

### [Recursion in JavaScript](https://www.codingame.com/playgrounds/5422/js-interview-prep-recursion)

#### Question 1: Sum all numbers

Write a function called `sumRange`. It will take a number and return the sum of all numbers from 1 up to the number passed in. (this question implies n >= 1)

    function sumRange(n) {
        if (n === 1) {
            return 1
        }
        return n + sumRange(n - 1)
    }

(I used the neat trick I just learned about not having to store an extra variable!)

#### Question 2: Power function

Write a function called `power` which takes in a base and an exponent. If the exponent is 0, return 1.

A power is basically base * base (repeated n times). 

    16 ^ 2 = 16 * 16
    2 ^ 3 = 2 * 2 * 2
    base ^ 3 = (base * base) ^ 2

    function power(base, exponent) {
        // case 1: exit
        if (exponent === 0) return 1

        return base * power(base, exponent - 1)
    }

#### Question 3: Calculate factorial

Write function that returns `factorial` of a number.

    function factorial(n) {
        if (n === 1) return n;

        return n * factorial(n - 1)
    }

#### Question 4: Check all values in an array

Write function `all` which takes an array and a callback and returns true if every value in the array returns true when passed as a parameter to the callback.

array.length === array.filter().length, if so, then return true, if not, return false

    function all(array, callback) {
        let filtered = array.filter(item => callback(item))

        return array.length === filtered.length
    }

Ok I didn't do recursion but like. why would you. My answer is great.

    function all(array, callback) {
        let copy = copy || array.slice();

        // exit case
        if (array.length === 1) {
            return callback(array[0]);
        } else if (callback([0]) { // Test if true
            copy.shift(); // If so, remove that element
            return all(copy, callback); // Recursive call
        } else {
            return false; // Stop the chain
        }
    }

#### Question 5: Product of an array

`productOfArray` takes array of numbers and returns the product of all of them

I assume array.shift is also in this one. I'd rather use pop tho. (Executing pop on the same line as the rest of the return statement works)

    function productOfArray(array) {
        if (array.length === 1) return array[0];

        return array.pop() * productOfArray(array);
    }

#### Question 6: Search JS Object

Write a function called `contains` that searches for a value in a nested object. It returns true if the object contains that value.

    function contains(object, value) {
        let values = Object.values(object);

        if (values.includes(value)) return true

        for (let i = 0; i < values.length; i++) {
            if (typeof values[i] === 'object') {
                return contains(values[i], value)
            }
        }

        return false;
    }

Given solution:

    function contains(object, value) {
        for (let key in object) {
            if (typeof object[key] === 'object') {
                return contains(object[key], value)
            } else if (object[key] === value) {
                return true
            }
        }

        return false;
    }

Some notes:
- You can't return false in an else statement within the false loop, or it will prematurely exit.
- I tested it with an additional level of object AFTER a value

    const nestedObject = {
        data: {
            info: {
                stuff: {
                    thing: {
                        moreStuff: {
                            magicNumber: 44,
                            something: {
                                string: 'foo2'
                            }
                        }
                    }
                }
            }
        }
    }
- I think their solution was a bit better because I forgot that `for in` exists, which you can then incorporate into the if statement. However, I wonder if checking for the value at the start makes it more efficient, as in the last loop you don't have to iterate through every value to check.

#### Question 7: Parse a multi-dimensional array

Given a multi-dimensional integer array, return the total number of integers stored inside this array. I will use `Number.isInteger(num)` to check if a number is an integer or not, since that's what the question is asking.

    let seven = [
        [
            [5], 3
        ], 0, 2, ['foo'], [], 
        [
            4, [5, 6]
        ]
    ];

    function totalIntegers(array) {
        let count = 0;
        array.forEach(item => {
            if (Array.isArray(item)) {
                count += totalIntegers(item)
            } else if (Number.isInteger(item)) {
                count += 1;
            }
        })
        return count;
    }

Their given solution differed from mine in that they did not use a loop at all. They used `first = array.shift()` as the value to check within each function call.

Other than that, it was fairly similar in structure.

    function totalIntegers(array){
        if(array.length === 0) return 0;

        let total = 0;
        let first = array.shift();

        if (Array.isArray(first)){
            total += totalIntegers(first); 
        } else if (Number.isInteger(first)) {
            total += 1;
        }

        return total + totalIntegers(array);
    }

In my answer, I decided not to explicitly state an exit condition as count would always be returned anyway, and if the `item` isn't an array, then subcalls are not continually added, so the chain would not be infinite.

#### Question 8:
Write a function that sums squares of numbers in list that may contain more lists

Er, so I think I'm being asked to get every number in all nested arrays. Each number must be squared, THEN added up at the end. I'll try writing it more like the previous example.

It's good practice to clone arrays when you're altering them, so I will.

Store count = 0
Firstly, clone array with `clone = array.slice()`
Exit clause: if clone.length === 0, return 0
If not, remove last element, clone.pop()
Check if last element is array, if so, recursive call on it
If last element is integer, square it (num * num), then return it
After this, return count + recursive call with clone

    function sumSquares(array) {
        if (array.length === 0) return 0;

        let count = 0;
        let clone = array.slice();
        let item = clone.pop();

        if (Array.isArray(item)) {
            count += sumSquares(item);
        } else if (Number.isInteger(item)) {
            count += (item * item)
        }

        return count += sumSquares(clone)
    }

I worry about the memory implications of me cloning `array` every time. But also nobody works directly on arrays anymore, you have to consider them immutable.
Also the given solution used a for loop so, ok I guess

#### Question 9:
The function should return an array containing repetitions of the number argument. For instance, replicate(3, 5) should return [5,5,5]. If the times argument is negative, return an empty array.

- Why would you ever do this recursively.
- This function should always return an array, so now I have to work around this arbitrary requirement
- If multiplier <= 0, return... nothing?? array???
- Because of the recursive nature of this task, AND the need for it to return an array with each call, I have to return an array no matter what, then add each value in subcall arrays to the outer function's array and return that
- So each call of this should result in [`number`], then call (multiplier - 1, number), add all the individual values of that result to this array, then return that???
- steps:
- create array.
- exit clause, return empty array
- if multiplier >= 1 (this is implied so i guess it always happens), array.push(number), once. 
- THEN, if number > 1, we need to recursively call the function and add each individual value of the RESULTING array to this one's array.
- I could concat the arrays though, that might be better
- then return array.

    function replicate (multiplier, number) {
        if (multiplier <= 0) return [];

        return [number].push(...replicate(multiplier - 1, number));
    }

Learnings from this:
- The fastest way to merge 2 arrays, in JavaScript, is to do `array1.push(...array2)` (which will alter `array1`). This `...` is called **spread syntax**. This won't work if you have a large number of calls (over 10,000 I think), so you should have a decent idea of the largest number of elements you expect to move with this. The spread syntax loads the entire source array `array2` onto the call stack.
- Otherwise, `let result = array1.concat(array2)` works (also, creates a new array & doesn't alter either existing array).
- `...` can also be used to clone arrays `const cloneSheeps = [...sheeps]` BUT will only do a *shallow copy*. So changing `cloneSheeps` will affect `sheeps`.
- You can automatically create an array containing `number` by just writing `[number]`!

## Shallow copying arrays
Arrays in JS are reference values, so copying using `=` like `const fakeSheeps = sheeps` will only copy the reference to the original array, and not the value of the array. They will occupy the same memory space basically. Changing `fakeSheeps` will change `sheeps`.

A shallow copy means *only the first level is copied*. Deeper levels are **referenced**. 

## Dynamic programming
For a problem to be able to be solved via dynamic programming, there are two key attributes it must possess:
- optimal substructure
- overlapping sub-problems

If a problem can be solved by combining optimal solutions to *non-overlapping* subproblems, then the strategy is "divide-and-conquer" (this is why merge-sort and quick-sort are not dynamic programming problems).

### Optimal substructure
The solution to a given optimization problem can be obtained by the combination of optimal solutions to its subproblems.

## Merge Sort
Sorting algorithms are a way to get to grips with recursion. One such algorithm is **Merge Sort**, a type of sort that lends itself to recursion and can be much faster than other algorithms like bubble sort on the right data sets.

Keep in mind that merge sort is a "divide and conquer" algorithm.

1. Divide the unsorted list into `n` sublists, each containing one element (a list of one element is considered sorted).
2. Repeatedly merge sublists to produce new sorted sublists until there is only one sublist remaining. Ta-da, sorted list.

### Video

Notes from [this video](https://youtu.be/Ns7tGNbtvV4):

Merge sort is basically: you have an array with 6 elements. Make them into 6 arrays with 1 element each. Now sort.

Pseudocode:
1. Sort the left half of the array (assuming n > 1)
2. Sort the right half of the array (assuming n > 1)
3. Merge the 2 halves

Consider the array `[5, 2, 1, 3, 6, 4]`. First let's divide into 2 halves, so the left half is `5, 2, 1`. Then we halve this again, arbitrarily deciding that the left "half" will be the smaller half bc odd number of elements, so `5`. Now, single element arrays are already sorted. So the left half of the left half is sorted.

Back to the right half of the left side, `2, 1`. Now, halve this again. The left half of this is `2`, which is sorted. Now the right half is `1`, also sorted woohoo.

Now, we are getting to the 3rd step of merge sort. Consider the halves `2` and `1`. Which has the lower element? `1`, right, so in a new hypothetical array, put that in first, then put `2` after it.

At this point, we have:

               3, 6, 4
    5, 1, 2

In which, looking at the left side only (because recursion), both our left and right halves are sorted. NOW, we want to merge the 2 halves of the left side. We do this by comparing the **first** element of the left half and the **first** element of the right half, and *seeing which one is smaller*, making the smaller one the first part of our new sorted array. So, `5 > 1`, so now `1` is the first element of our new array. Then, is 5 or 2 lower? The answer is 2, so now the array is `1, 2`. Then, the only option left is 5, so now we have `[1, 2, 5]`.

Now, we have done step 1 of the overall merge sort of this array.

Split into halves: `3`, `6, 4`. 3 is already sorted. Divide `6, 4` into halves. Each half is already sorted because 1 element. So now merge: is 6 lower or 4 lower? 4, so put 4 in our new sub array. Then, 6. So now the right half of the overall array is `3, 4, 6`.

So now, let's merge the left and right halves of the right side. Is 3 lower, or 4? 3, so put it into the hypothetical sorted array. Then, because there's nothing left to compare with 4, we know that everything on the right has to be bigger than everything on the left. So because we know that, we know that everything on the right side must go at once. 

So now we are at step 3 of the overall process, as both sides are sorted:

    1, 2, 5 | 3, 4, 6

So now, let's ask ourselves which is lower: 1, or 3? It's 1, so add 1 to the new sorted array. NOW, compare the new "first" element of the left side to the first element of the right side: 2 or 3? It's 2, so put 2 in. Then, which is lower: 5, or 3? We add 3 to the new array.

          5 |    4, 6
    1, 2, 3

So now 5 is the "first" element of the left side, and 4 is the "first" on the right. Which is lower, 4 or 5? Add 4 to the array. Then, compare 5 and 6, add 5. Then finally, add 6.

So after breaking everything down into sub-arrays, we've sorted the array into `[1, 2, 3, 4, 5, 6]`.

#### Why merge sort

Worst case scenario:

We have to split up `n` elements, then recombine them, effectively doubling the sorted subarrays as we build them up. (combining 1-element arrays into 2-element arrays, combining 2-element arrays into 4-element arrays etc.)

Best case scenario:

The area is already sorted, but we don't know this until we split it up and divide it. So we go through the process regardless.

So we have `n` elements, and we might have to combine them `log(n)` times. In the worst case scenario, the runtime of merge sort will be `O(n log n)`, which is faster than `n squared`. The runtime is the same for the best case scenario.

Comparing this to the runtime of other sort algorithms, bubble sort/insertion sort/selection sort, which all have a "worst case scenario" runtime of `n squared`.

So on the average/worst case, merge sort will be faster than bubble sort (unless the array is already sorted), at the expense of taking up more memory, because we have to recombine/create new segments of memory.

### [Other video](https://www.youtube.com/watch?v=uEbdK2CG_B8&feature=youtu.be&t=1h2m)

Consider this, a recursive approach to searching for a name in a phone book:

    0 pick up phone book
    1 open to middle of phone book
    2 look at names
    3 if Smith is among names
    4   call Mike
    5 else if Smith is earlier in the book
    6   search for Mike in left half of book (divide left half in half)
    7 else if Smith is later in book
    8   search for Mike in right half of book (divide right half in half)
    9 else
    10  quit

Pseudocode:
    On input of `n` elements
        if n < 2 (if n = 1 or 2, in other words these are already sorted)
            return
        else
            sort left half of elements
            sort right half of elements
            merge sorted halves

I think the most important thing for me to keep in mind is the "merge" step. To do this, take the FIRST element of each side and compare them, then add the smallest/lowest/earliest element to the new sorted array. Then repeat this until one or both sides are empty. From the previous video: if one side is empty and the other side isn't, you can assume that everything on the remaining side is larger, so just move that whole side to the sorted array.

So we have e.g. 8 elements -> 4 elements -> 2 elements -> 1 elements. The runtime of diving stuff in half is generally `log n`.

Every time we merge sort, we have to work through each element `n` times (n being the length of the array). 

- We did `log n` things
- Every time we did that, we incurred `n` steps of merging
- So `n` * `log n` will give you "Big O of n log n"???? `Θ(n log n)`



### [Merge sort video 3](https://youtu.be/6pV2IF0fgKY?t=241)

Apparently merge sorts can be performed irrespective of data structure so that's good to know.

> **Big O or Theta**: apparently when people talk about "Big O", they mean "theta", `Θ`, the Greek letter.

So when you have 2 lists (irrespective of data structure), A and B, let's say that A has `m` number of elements, and B has `n` number of elements. The merged list C has `m + n` elements. The amount of time taken to move all the elements to the merged list are `θ(m + n)`. Apparently this `θ(m + n)` has become a notation for merging, as usually time is notated using `n` only.

#### The algorithm for merge (A, B, m, n)

Start with: 

    i = 1, j = 1, k = 1

    if (A[1] < B[1])
    {
        C[k++] = A[i++]
    } else {
        C[k++] = B[j++]
    }

Basically, if `A[i]` is smaller than `B[j]`, move `A[i]` to `C[k]` position. Then increment `i` and `k` (moving to the next "slots"). Likewise for `B[j] < A[i]`.

Do this for as long as `i <= m` and `j <= n`.

    while (i <= m && j <= n) {
        if (A[1] < B[1])
        {
            C[k++] = A[i++]
        } else {
            C[k++] = B[j++]
        }
    }

Once this completes, we know that either of the lists will have remaining elements (because it only runs while BOTH lists fulfill the condition). But, we don't know which list will have remaining elements.

    // For copying elements up until one list empties
    while (i <= m && j <= n) {
        if (A[1] < B[1])
        {
            C[k++] = A[i++]
        } else {
            C[k++] = B[j++]
        }
    }

    // If there are any remaining in A, copy to C
    for (i <= m; i++) {
        C[k++] = A[i]
    }

    // If there are any remaining in B, copy to C
    for (j <= n; j++) {
        C[k++] = B[j]
    }

#### What if more than 2 lists: m-way merging

Where `m` is the number of lists you have.

    A  B  C  D
    4  3  8  2
    6  5  10 4
    12 9  16 18

The way that you would do this is, of course, to split into halves until you get sorted lists. In this case, you can merge A/B and C/D right away as they are already sorted (although in a code setting you would be splitting these down to single-element arrays regardless). 

#### The difference between "2-way merge sort" and "merge sort"

2-way merge sort is an iterative process using loop. Merge sort is a recursive procedure.

#### 2-way merge sort

Consider the list: 9, 3, 7, 5, 6, 4, 8, 2.

1. Assume each element is its own "list", with a single element (the base case)
2. Compare first 2 lists & merge them. Repeat for each pair of lists:

    9, 3, 7, 5, 6, 4, 8, 2

    3, 9 | 5, 7 | 4, 6 | 2, 8

Now we have 4 lists of 2 elements.

Remember: when you merge, the result has to be kept in a separate array. So if the original list is A, we need a new array B to store the result. If you only want to have one array, you can copy the results from B to A... (that's what the guy in the video is saying)

3. Merge again: second pass

    3, 9 | 5, 7 | 4, 6 | 2, 8
    3, 5, 7, 9 | 2, 4, 6, 8

4. Third pass:

    3, 5, 7, 9 | 2, 4, 6, 8
    2, 3, 4, 5, 6, 7, 8, 9

Then, you can copy this back to array A if you only want one array. I guess.

Total number of work:

    if number of elements in the list is n = 8,
    We started with 8 separate lists of 1 element
    First we did 8 / 2 to get 4 lists
    Then we divided by 2 again to get 2 lists
    Then we divided by 2 again to get 1 list.

    8/2/2/2 = 1
    8 / 2³ = 1
    8 = 2³
    log₂ 8 = 3, which means log of 80 gives 3
    So we can know the number of passes done by taking log of n

So, number of passes = log n

In each pass, there are `n` merges (each individual number being moved to new array). And in total we are doing `log n` passes.

So the total time taken is Θ(n log n).

## Common Data Structures & Algorithms

The point of a data structure is to store data in a way that meets the needs of your particular application. e.g. storing things in one giant array would be incredibly inefficient when you wanted to locate a specific value.

Each data structure type has its own tradeoffs, and are differentiated by how long it takes to first populate the structure, how long it takes to find/add elements, and how large it is in memory.

Sorting algorithms are pretty common (searching through large batches of data seems like a common problem to encounter). Another use for algorithms is search, where milliseconds count. When you're searching through very large troves of data, the quality of your search algorithm is important. Traversing a data tree looking for a particular element is a common occurrence in data intensive applications.

Luckily, these problems have all been solved for us multiple times in the past. All we need to do is understand *how* they are solved, so we can take this knowledge and apply it to similar problems that we encounter.

Some types of algorithms:
- Linear search: starting from the top, look through every single entry and compare to what we're looking for.
- Chunking search: moving at arbitrary increments (e.g. 100 pages/200 pages/75 pages), open pages of a phone book to see if you're getting closer to finding what you're looking for. This is a human behaviour and is not efficient.
- Binary search: split book in half, determine which half of the phone book the entry is in, split in half again, etc. This is very scalable: with a 400 page book, it may take you about 8 passes. With a 4 million page book, it would take at most 22 passes.

The number of passes you need to make when doing binary search can be calculated by `log₂(n)`. With a 400-page book:

    log₂(400) = 8.64...

So in the worst case, rounding up, it would take you about 9 passes. How about 4 million?

    log₂(4000000) = 21.93...

At most, 22.

With a 4 million page book, the worst case scenario when using a linear approach would take 4 million passes. With the halving approach, it would only take 22!

Learning algorithms is learning how to break problems down in the manner outlined here: break them down into different steps, where there is exactly one behaviour that someone could take given a scenario. Understanding a process is harder than putting it into code.

### Tricks for the tool belt
Knowing the right trick for the right scenario (e.g. binary search vs linear search) can save you a lot of time and troubleshooting.

Some useful algorithms to learn:
- depth-first search
- breadth-first search
- writing sorting algorithms

First you must understand how they work intuitively. Then, translate them into code.

### Data structures

In order to implement some algorithms, you need additional tools in your belt. e.g. to do the breadth-first search algorithm, you need to understand **queues**. If you don't understand the various data structures, you're missing essential tools and will have a bad time.

Let's talk about queues. Think about a ticket system at a deli. You pull a number and wait for your number to be called, i.e. joining the "line". This is called **enqueuing**, when you "start waiting".

When it's your turn to be served (and you'd have been in line the longest at this point), your number will be called and you won't be in the queue anymore. This is called **dequeuing**.

You can implement a queue with an array and 2 simple methods (array.push and array.shift).

square root of c c = square root of a squared + b squared

## Some more algorithms

Integer multiplication:

    Input: 2 n-digit numbers, x and y
    Output: product of x * y
    The primitive operation: add or multiply 2 single digit numbers
    
    The number of primitive operations needed to multiply 2 4-digit integers: n * n, or n²

If you want to be a good algorithm designer, you should always be asking: **can we do better?** As in, can you do better than the "obvious"/naive solution. Let's apply this to the multiplication.

### Recursive algorithm

    Write x = 10^(n/2) * (a + b) and y = 10^(n/2) * (c + d)
    Where a, b, c, d are all (n/2)-digit numbers
    e.g. a = 56, b = 78, c = 12, d = 34
    Then x*y = (10^(n/2)*(a + b)) * (10^(n/2) * (c + d))
             = 10ⁿac + 10^(n/2) * (ad + bc) + bd (let's call this expression *)
    
    Idea: recursively compute ac, ad, bc, bd from the * expression. Evaluate each individual expression, then simply add them up.

### Karatsuba Multiplication

An algorithm for multiplication of integers.

In `10ⁿac + 10^(n/2) * (ad + bc) + bd`, we only care about ac, ad, bc, bd as their sum, not as individual values. Thus, can we perform only 3 recursive calls instead of 4, evaluating `ad + bc`. 

1. Recursively compute ac
2. Recursively compute bd
3. Recursively compute `(a+b)(c+d)`, which can be written as `ac + bd + ad + bc`
The trick: the result of the third recursive call, `3 - 1 - 2`: in line 3, `ac` and `bd` cancel out the `ac` and `bd` from the 1st and 2nd recursive calls.

So the result of the 3rd call minus the result of the 1st and 2nd call = `ad + bc`.

So I guess this means you can multiply using only 3 calls instead of 4. I'm not sure what the point of this was.

## [Binary Search](https://youtu.be/T98PIp4omUA)

Binary search requires that a special condition be met beforehand. But, if it is, it's way more efficient than linear search. It is a "divide and conquer" algorithm, where you reduce the search area by half with each pass. In order to do so, the data MUST be sorted, otherwise it's a mess and you can't just discard half the elements. But if the data IS sorted, then you know that everything to the left of where you are is lower than your current position, and everything to the right is greater.

Pseudocode:
- Repeat until the (sub)array is of size 0:
    1. Calculate the middle point of the current array.
    2. If the target is at the middle, stop.
    3. Otherwise, if the target is less than what's at the middle, repeat, changing the end point to be just to the left of the middle.
    4. Otherwise, if the target is greater than what's at the middle, repeat, changing the end point to be just to the right of the middle.

### Example

Consider this sorted array: 6 7 8 9 10 11 14 15 17 19 22 23 25 28 30. The length is 14, the target is 19.

Let's first calculate the midpoint value: `(0 + 14)/2` = 7, which is the element `15`. 15 !== 19, but 19 > 15, so we can change the start point to `array[8]`, and the endpoint is still `array[14]`. New endpoint: `(8 + 14)/2 = 11`, which is the element `23`. 19 < 23, so now we eliminate everything to the RIGHT of array[11]. 

Our start point is `array[8]` and our end point is now `array[10]` (all that's left is 17, 19, 22).

New midpoint: `8 + 10 = 18`, `18/2 = 9`. array[9] === 19, yay.

### What if the target is NOT within the array?

If we look for `16` instead.

Midpoint = (0 + 14)/2 = 7 = element `15`. 16 > 15, so eliminate the left side of the array.

New start point: 8. (8 + 14)/2 = 11 - `23`. 16 < 23, eliminate right side of the array.

At some point you'll end up with start = 8, and end = 7. Obviously, you can't do that! But you've already eliminated everything else in the array.

This is actually the situation of having a subarray with size 0. If you reach that, you know your target isn't in this array.

### Runtime

Worst case: divide a list of `n` elements in half repeatedly to find the target element, either because it will be found at the end of the last division, or it doesn't exist at all. This is `θ(log n)` times.

Best case: `Ω(n)` times, as in, you'll find the target right at your first midpoint. e.g. in the above example, if target was `15`.

