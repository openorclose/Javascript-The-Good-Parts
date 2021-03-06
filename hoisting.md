# Hoisting in ECMAScript2015

## Block Scope

A simple but not technically robust explanation of block scope is this: A block scope is defined by an opening `{` and a closing `}`, such as in `if`, `while` statements or function declarations. Whenever I say scope, I am referring to the block scope.

## Hoisting
To keep things simple, I'm not going to be discussing `var` declarations for now, as they're a remnant of pre-ES2k15 days and kept to maintain backwards compatability. I'll only be discussing declarations using `const`, `let`, and `function functionName(param) {}`.

### The basics

```javascript
const PI = 3.14;
// PI = 2; is disallowed, constants cannot be redefined.
let sum = 0;
sum += 1;

function square(number) {
    return number ** 2;
}
```
In essence,
- `const` declares a constant that is read only and can never be reassigned nor redeclared with `function`, `const`, or `let`
- `let` declares a variable that can can both be read from and reassigned. It cannot be redeclared with `let`, `const`, nor `function`
- `function` declares a variable that can both be read from and reassigned. It cannot be redeclared with `let` nor `const`, but declaring a function with the same name is allowed.
```javascript
function a() { 
    return 10;
}
function a() { //this is allowed
    return 20;
}
```

Now as far as hoisting rules go, `const` and `let` are the same so I'll just talk about `let` from now on, but remember that they apply to both.

### Hoisting: Function Declarations

```javascript
function example1() { 
    alert(getHello()); 
    function getHello() {
        return "Hello!";
    }
    function redundantFunction() {
        return "Hello!";
    }
}
```
`getHello` is defined only after `alert(getHello());`, but `example1()` still alerts `Hello!`. This is because function declarations are *hoisted* to the top of the scope. The interpreter will move *all* function declarations in the current scope to the top of the scope as follows.

```javascript
function example1() { 
    function getHello() {
        return "Hello!";
    }
    function redundantFunction() {
        return "Hello!";
    }
    alert(getHello()); 
}
```

### Hoisting: `let` Declarations

```javascript
let a = 1;
function example2() { 
    alert(a);
    
    let a = 20;
    alert(a);
}
```

Reading the code line by line, one might expect `example2` to first alert 1 and then 20. But similar to function declarations, the left part of `let` statements are also hoisted to the top of their scope:

```javascript
let a = 1;
function example2() { 
    let a; //hoisting, note that only the declaration but not the assigment is hoisted.
    alert(a);
    
    a = 20; //now a is being assigned.
    alert(a);
}
```

The most important difference between function declarations and `let` statements is that the entirety of the function declaration gets hoisted to the top, whereas only the `let name` part of `let name = value;` statement gets hoisted. This is why when trying to read from `a` in `example2` there is an error stating that `a` is not defined.

### Hoisting, altogether

```javascript
function example3() { 
    let a = 20; 
    alert(a);
    function a() { //there
        return 100;
    }
}
example3();
```
It looks like the code above should alert 20, and then raise an error due to redeclaring `a` at `there`. But firstly, function declarations are hoisted. Then, `let` statements are hoisted. So the above code would look like this after hoisting:
```javascript
function example3() { 
    function a() { //function declarations moved to the top first
        return 100;
    }
    let a; //let statements moved to the top next, error here
    a = 20; //assignment part of the let
    alert(a);
    
}
example3();
```
As can be seen now, function `a` has already been declared and so cannot be declared again, resulting in an error before the alert can happen.
