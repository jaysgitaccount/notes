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

