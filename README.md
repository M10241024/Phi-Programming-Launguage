# Calling functions
Functions can be called like this:
```
f(a, b)
f a b
f a, b
f(a)(b)
// all these forms are equivalent
```

You can call the function with only some of its arguments and pass the rest of the arguments later:
```
var g = f(a)
g(b) // same as f(a, b)
```

You can use `var f_1 = {f(a, b)}` to pass all the arguments without actualy calling the function.

You can use `f(arg_2 = b, arg_1 = a)` to pass arguments out of order.

Some functions are called operators which means that they need the first argument to be on the left: `(a)op(b)`. Operators can still be called normaly: `op(a, b)`.

What actualy happens is that  the first operand recognizes that you pass in an operator and calls it in reverse order: `f(op)` == `op(f)`.
```
function f(a) {
    if a is Operator {
        return a(f)
    }
    ...
}
```

## Grouping
If an argument has incorrect type it will be grouped with another argument:
```
function f(a: number, b: string) {...}

f 1 + 2    // second argument is supposed to be a string so try grouping
f (1 +) 2  // (1 +) doesn't return a number so try more grouping
f (1 + 2)  // works!
```

Functions can also have precedence to help with this:
```
1 + 2 * 3    // (1 + 2) works, but let's check the next operator. * has a higher priority than +.
1 + (2 *) 3  // (2 *) has a wrong type, so try grouping more
1 + (2 * 3)  // works!
```
This is also how pharenteses work internaly:
```
                 // square brackets are used instead of parenteses to aviod confusion
1 * [2 + 3]      // [ has a wrong type and a higher priority
1 * ([ 2) + 3]   // ([ 2) is still wrong type
...              // several groupings later...
1 * ([ 2 + 3 ])  // works!
```

User created functions have the highest precedence except for parentheses.

Use `f.call_list([a], {"arg_2" = b})` to use the values from list/dictionary as arguments.

Everything is a function.

# Variables
Use the `var` to create a variable:
```
var a
```

Use `:` to decide what will be the type of your variable. Not assigning a type will throw a warning, using `Any` instead.
```
var a: number
```

Use `=` to set the value of your variable:
```
var a: number = 5
```

You can `_` to reference the type:
```
var a: Dog = _.new("Pi")
// is equivalent to
var a: Dog = Dog.new("Pi")
```

Without using above `:` and `=` defaults are used: `var a: Any = _`. Not assigning a type will throw a warning.

Use `=` again to change the value of a variable. Other operators like `+=`, `++`, or `.=` can also be used:
```
var a: number = 5
a = 6
a += 7
a++
a.=absolute_value()
```

Use `_` to reference old value of the variable:
```
a = a + 5
a = _ + 5
a += 5
// all these are equivalent
```

Assigning a value will try to convert it into the correct type:
```
var a: number = true  // true converts to 1
a = "abc"             // "abc" cannot be converted into a number, throws an error
```

You can use `var` to create members of another object: `var dog.age: number = 3`.

Use `delete` to delete a variable:
```
var a: number = 5
var a: number = 6  // error: this variable already exists
delete a
a = 6              // error: there is no variable `a`
var a: number = 6  // no error
```

Everything is a variable.

## Setters and getters
Pass extra arguments to `var` in order to use setters and getters. The exact details are subject to change.
```
var a: number (
    set = function(this_scope, old_value, new_value) {...},
        // called when `a = ...` is used, should return the new value
    
    get = function(this_scope, old_value, new_value) {...},
        // called when `a` is used, should return the value you get
    
    on_set = function(this_scope, old_value, new_value) {...},
        // called when `a = ...` is used, should return `this_scope` or `null`
    
    on_get = function(this_scope, old_value, new_value) {...},
        // called when `a` is used, should return `this_scope` or `null`
    
    on_change = function(this_scope, old_value, new_value) {...},
        // called when `a = ...` is used and the new value is different to the previous value, should return `this_scope` or `null`
)
```

## Constants
Use `const` instead of `var` for a constant:
```
const a: number = 5
var b: number (set = function(this_scope, old_value, new_value) {
    throw SetConstantError
    return old_value
}) = 5
```

Members of a constant can't be changed:
```
const dog: Dog = _.new("Pi")
dog.name = "Darwin"          // throws an error
```

You can also use `const` to convert a variable to a constant:
```
var a: number = 5
a = 6
const a
a = 7             // error
```

## References
Use `ref` to create a reference:
```
var a: number = 5
ref b: number = a
var d: Reference<number> = a
```

When you do anything with the reference, the orginal variable also changes and vice-versa:
```
var a: number = 5
ref b: number = a
b = 6              // a also changes
a = 7              // b also changes
var c: number = b  // c is independent
```

Using `delete` on a reference will also delete the orginal variable, so use `delete_ref` instead. `delete_ref` can't be used on a regular variable, only references.

In most cases using a reference is the same as typing the orginal variable name.

# Functions
Use `function` to create a function:
```
function f() {}
// is equivalent to
const f: function = function() {}
```
Use `var function` insetead of just `function` to create a function thet can be overriden:
```
function f() {}
// is equivalent to
const f: function = function() {}

// but
var function g() {}
// is equivalent to
var g: function = function() {}
```

Use names in the parentisis for arguments. Use `:` to signify the type of an argument and `=` for the default value:
```
function(arg_1, arg_2: number, arg_3: number = 5) {}
```

Use `return` to return a value and `:` to signify the return type:
```
function add(a: number, b: number): number {
    return a + b
}
```

All functions return `null` by default.

Use `...` after a comma for functions with potentialy infinite argument count:
```
function sum(numbers: set<number> = [], ...): number {
    var result: number = 0
    for num: number in numbers {
        result += num
    }
    return result
}
```

In reality every function has just one argument:
```
function(a, b, c) {
    return a + b + c
}
```
is actualy just
```
function(a) {
    return function(b) {
        return function(c) {
            return a + b + c
        }
    }
}
```
This allows for many useful stuff, like dynamic argument count and types:
```
function(type: Any) {
    return function(value: type) {...}
}
// this function uses the first argument as a type for the second argument!
```
This also how `...` works internaly.

Everything is a function.

## Pure functions
...
