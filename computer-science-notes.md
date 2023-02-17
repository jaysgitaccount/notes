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
        // case 1, the exit clause
        // prev = previous salaries added up, current = current array element (object)
        if (Array.isArray(department) {
            return department.reduce((prev, current) => prev + current.salary, 0) // sum the array
        })
    }

