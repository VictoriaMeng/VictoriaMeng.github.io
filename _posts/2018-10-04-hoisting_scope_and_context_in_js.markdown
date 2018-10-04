---
layout: post
title:      "Hoisting, Scope, and Context in JS "
date:       2018-10-04 04:41:32 +0000
permalink:  hoisting_scope_and_context_in_js
---

## Why You Should Use 'let' and 'const'


One thing I've learned since starting my Javascript journey is that a modern dev should always use ES6 `const` or `let` keywords to define variables instead of `var`.

Why? It has to do with Javascript hosting and scope.

## What is hoisting?

Remember the two-phase nature of the Javascript engine.

1. The **compilation** phase, where the Javascript engine will allocate memory space to first save function declarations, and then variable declarations.

2. The **execution** phase, where the Javascript engine then starts executing the code. performing all other actions besides saving the function and variable declarations. This includes invoking functions, printing to the console, and *assigning values to variables*.

To understand hoisting, the big takeaway here is that the *Javascript engine will not assign values to variables as part of the first compilation phase.*

Instead, Javascript will instead 'hoist' function and variable pointers to the top of their current scopes, above all other code.

This means that if you fail to declare a variable before invoking it, you might get odd results in your code.  For instance, in the below line of code...

`console.log(a);

var a = 1`

...instead of printing `1` or a `ReferenceError`, the console will print out the value of `a` as `undefined`.

![](https://media.giphy.com/media/V5lM6OffQXOgg/giphy.gif)

It looks confusing, but the reason is because the Javascript engine actually compiles and executes the above line of code like this:

`var a;

console.log(a);

a = 1;`

When it sees the `var` keyword, it hoists the `a` variable pointer to the top of the variable's scope without the definition. When the Javascript engine then invokes the `console.log(a)` function and looks for an `a` variable, it successfully finds and prints it out even though it's undefined.

Two other important `hoisting` facts to know is that *functions are hoisted before variables during the compilation phase* and *duplicate function declarations override each other, but duplicate `var` declarations are discarded.*

So when you have a function and variable with the same name...

`foo(); 

var foo;

function foo() { console.log( 1 ); }

foo = function() {  console.log( 2 ); }`

...The Javascript engine will interpret the code like this:

`function foo() { console.log( 1 ); }

foo(); // 1

foo = function() { console.log( 2 ); };`

Invoking `foo()` will print `1` because the Javascript engine first hoists the first `function` definition and then assigns the value of the `foo` variable, discarding the `foo` variable declared with `var` because functions are hoisted before variables.

Another important hoisting fact to remember is that `function declarations are hoisted, but not function expressions`. If you assign a function as a value of a variable, it won't be hoisted. For example:

`foo();

var foo = function bar() { alert("Hello"); };`

In the above line of code, `foo()` will return a `TypeError`, not a `ReferenceError` because the engine will interpret the code as:

`var foo;

foo();
bar();

var foo = function bar() { alert("Hello"); };`

So invoking `foo()` throws a `TypeError` because foo as been declared, but isn't yet pointing to a function. Invoking `bar()` will throw a `ReferenceError` because the ` bar` function expression in the the variable assignment isn't hoisted.

The way to avoid issues caused by hoisting is to:

1. Define your functions and variables at the top of your code so they won't be invoked before they're defined.

2. Use `let` and `const` keywords to define variables instead of `var`. Variables assigned with those two keywords are technically hoisted, but can't be invoked before they're defined without an error message.

3. Do not you duplicate names for your functions or variables.

## Hoisting and Scope

You might've heard that hoisting is **per-scope**, but what is **scope**?

Scope refers to whether or not a variable method is available at different points of the code. Javascript has a *global-execution context* where functions and variables declared in the global-execution context are available to the rest of code.

But *declaring a function creates a function scope where the variables declared in the function aren't available outside the function.*

For example:

`const one = 1;

function two() { return one + 1; }; // 2`

In the above code block, function `two()` has access to the `one` variable because `one` was declared in the global scope.

But in the below code:

`function test() { const one = 1; };

one // Reference error`

Invoking the `one` variable in the global scope throws a `ReferenceError` because `one` is declared inside the function scope of `test()`.

What does scope have to do with hoisting? When we say hoisting is per-scope, we mean the Javascript engine only hoists functions and variable declarations to the top of their current scope. For instance, the engine will read the below code:

`function test() {
    foo;
		
		var foo = "foo";
}`; 

As:

`function test() {
    var foo;
		
		foo // undefined;
		
		foo = "foo";
};`

...only hoisting the `foo` variable to the top of the function scope, not the global scope.

When a function is declared inside a function, all variables declared in the parent functions are available to the child functions in what is called the `scope-chain`. For example,

`function one() {
    const foo = "foo";
		
		function two() {
		    const foobar = foo + "bar"
		};
};`

In the above code block, function `two()` has access to the `foo` variable declared in parent function `one()`.

However, *invoking a function inside another function doesn't make it part of an existing scope chain. Only declaring it does, not invoking it.* For example:

`const word = "foo";

function one() {
    console.log(word);
};

function two() {
    const word = "bar";
		
		one();
};`

In the above code block, invoking `one()` in `two()` will print out `foo`, not `bar`. Because `two()` and `one()` are not declared in each other scopes (only invoked), the only `word` variable that `two()` has access to is the one declared in the global scope.

Blocks have slightly different scoping rules from functions. That difference is yet another reason to use `let` and `const` instead of `var`. In Javascript, variables declared in a block with `var` aren't blocked-scoped, but variables declared with `let` and `const` are.

Futhermore, be careful when assigning a variable without a keyword. Variables declared without a keyword are available in the global scope even when declared in a function.

## Context vs Scope and this

Talking about scope is also a good time to talk about context, since the words are often incorrectly used interchangeably.

If you're not sure on the difference between scope and context, scope is function-based and context is object-based.

*Scope* refers to what variables and functions can be accessed at different points in the code.

*Context* refers to the value of the keyword `this`.

The `this` keyword can refer to several objects depending on how it's used.

* Outside of any function, `this` refers to the global object. In web browsers, that would be the window.

* Inside an object method (*an action that can be performed on an object*), `this` refers to the object receiving the method, the caller.

* Without strict mode, in a standalone function, `this` refers to the global object.

* With strict mod, `this` becomes `undefined` in a standard function.

* Arrow functions don't have their own `this`, instead taking the value of `this` from its enclosing scope.









