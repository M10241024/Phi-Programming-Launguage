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

You can use `var f_1 = {f(a, b)}` to pass the arguments without actualy calling the function.

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

f 1 + 2 // second argument is supposed to be a string so try grouping
f (1 +) 2 // (1 +) doesn't return a number so try more grouping
f (1 + 2) // works!
```

Functions can also have precedence to help with this:
```
1 + 2 * 3 // +(1, 2) works, but let's check the next operator. * has a higher priority than +.
+ 1 (2 *) 3 // 2(*) has a wrong type, so try grouping more
+ 1 (* 2 3) // works!
```
```
1 * "("2 + 3")' // ( has a wrong type and a higher priority
* 1 ("(" 2) + 3 // ("(" 2) is still wrong type
* 1 ("(" 2 + 3 ")") // works!
```

User created functions have the highest precedence except for parentheses.

Use `f.call_list([a], {"arg_2" = b})` to use the values from list/dictionary as arguments.

Everything is a function.

# Variables
Use the `var` keyword to create a variable: `var a`.

Use the `:` operator to decide what will be the type of your variable: `var a: number`.

Use the `=` operator to set the value of your variable: `var a: number = 5`.

You can the `_` special variable to reference the type: `var a: Dog = _.new("Pi")` == `var a: Dog = Dog.new("Pi")`.

Without using above `:` and `=` defaults are used: `var a: Any = _`.

Use the `=` operator again to change the value of the variable: `a = 6`. Other operators like `+=`, `++`, or `.=` can also be used.

Assigning a value will try to convert it into the correct type:
```
var a: number = true // true converts to 1
a = "abc" // "abc" cannot be converted into a number, throws an error
```

You can use `var` to create members of another object: `var dog.age: int = 3`.

Use `delete` keyword to delete a variable: `delete a`.

Everything is a variable.

## Setters and getters
Pass extra arguments to `var` in order to use setters and getters:
```
var a: int (
    set = function(this_scope, old_value, new_value) {...}, // called when `a = ...` is used, should return the new value
    get = function(this_scope, old_value, new_value) {...}, // called when `a` is used, should return the value you get
    on_set = function(this_scope, old_value, new_value) {...}, // called when `a = ...` is used, should return `this_scope` or `null`
    on_get = function(this_scope, old_value, new_value) {...}, // called when `a` is used, should return `this_scope` or `null`
    on_change = function(this_scope, old_value, new_value) {...}, // called when `a = ...` is used and the new value is different to the previous value, should return `this_scope` or `null`
)
```

## Constants
Use `const` instead of `var` for a constant:
```
const a: int = 5
var b: int (set = function(this_scope, old_value, new_value) {
    throw_error SetConstantError
    return old_value
} = 5
```

Members of a constant can't be changed:
```
const dog: Dog = _.new("Pi")
dog.name = "Darwin" // throws an error
```

## References
Use `ref` keyword to create a reference:
```
var a: int = 5
ref b: int = a
var d: Reference<int> = a
```

When you do anything with the reference, the orginal variable also changes and vice-versa:
```
...
b = 6 // a also changes
a = 7 // b also changes
var c: int = b // c is independent
```

Using `delete` on a reference will also delete the orginal variable, so use `delete_ref` instead.

In most cases using a reference is the same as typing the orginal variable name.
