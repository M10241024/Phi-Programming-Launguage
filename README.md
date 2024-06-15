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

You can use `f(arg_2 = b, arg_1 = a)` to pass arguments out of order.

Some functions are called operators which means that they need the first argument to be on the left: `(a)op(b)`. Operators can still be called normaly: `op(a, b)`.

What actualy happens is that  the first operand recognizes that you pass in an operator and calls it in reverse order: `f(op)` == `op(f)`.
```
function f(a) {
    if a is operator {
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
a.=absolute_value
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

Using `var` again after creating a variable will do nothing or asign a new value an existing variable:
```
var a: number      // a is 0
var a: number = 5  // a is now 5
var a: number      // does nothing, a is still 5
var a: bool        // a is now converted to bool (when converting 5 it gives true)
```

You can use `var` to create members of another object:
```
var dog.age: number = 3
```

All data types are inmutable and creating a variable just means copying them.

Everything is a variable.

## Setters and getters
...

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

You can also use `const` to change from variable to a constant:
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
// above line is equivalent to
var b: Reference<number> = a
```

When you do anything with the reference, the orginal variable also changes and vice-versa:
```
var a: number = 5
ref b: number = a
b = 6              // a also changes
a = 7              // b also changes
var c: number = b  // c is independent and doesn't change with a and b
```

In most cases using a reference is the same as typing the orginal variable name.

# Functions
Use `function` to create a function:
```
function f(arg) {}
// is equivalent to
var f: function = function(arg) {}
```
Use `const function` insetead of just `function` to create a function that can't be overriden:
```
function f(arg) {}
// is equivalent to
var f: function = function(arg) {}

// but
const function g(arg) {}
// is equivalent to
const g: function = function(arg) {}
```

Use names in the parentisis for arguments. Use `:` to signify the type of an argument and `=` for the default value:
```
function(arg_1, arg_2: number, arg_3: number = 5) {}
```

Use `return` to return a value and `:` after the argument list to signify the return type:
```
function add(a: number, b: number): number {
    return a + b
}
```

All functions return `null` by default.

Functions whith zero arguments don't actualy exist - you can create one but it will just equivalent to its return value:
```
function() {return 5} == 5
```

You can create an operator with `operator` instead of `function`. Operators work the same, bat can be called with the first argument before the function instead of after it.

You can change the operator/function precedence with `f.precedence = ...`. Higher value means it will be evaluated first.

Everything is a function.

## Variable arguments
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

Rember that when not all arguments are provided, the function just waits for more arguments so functions with variable argument counts bechave differently depending on what type they are assigned to:
```
var a: number = sum(1, 2, 3)    // a is 1 + 2 + 3 == 6
var b: function = sum(1, 2, 3)  // b is be a function that expects more arguments
var c: number = a(4, 5, 6)      // c uses function a which had some numbers stored already, so it returns 1 + 2 + 3 + 4 + 5 + 6 = 21
```

## One argument
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

## Pure functions
Pure functions are functions that:
1. Don't affect the rest of the program other than returning a value.
2. Their output depends only on their input.

All functions in Phi are pure functions.

This means that when you are creating a function all variables created outside of the function work like constants:
```
var a: number = 5
function f(arg) {
    return a
}
f()  // returns 5
a = 6
f()  // still returns 5
```

It also means that functions can't change variables created outside of the function:
```
var a: number = 5
function f(arg) {
    a += 1  // throws an error
}
```

So in order to have methods that act on objects in-place you need to use `=` or `.=`:
```
var a: list<number> = [1, 2, 3]
list            // is [1, 2, 3]
list.reversed   // returns a new list [3, 2, 1], but doesn't modify the orginal
list            // still [1, 2, 3]
// to reverse the list you need to use `=`
list = list.reversed   // or
list = _.reversed      // or
list.=reversed

list // will now be [3, 2, 1]
```

But everything is a function, including `=` so how does it work? The answer is: it takes the rest of the code as an argument and modifies the variable there:
```
operator ."=" (variable: phi.Variable, value: Any, the_rest_of_the_code: TheRestOfTheFunction, ...): Any {
    var new_variable_set: Any = variable.parent
    new_variable_set.=set_variable(variable)
    return the_rest_of_the_function(new_variable_set)
}
// I still have to decide how exactly will it work
```

## Flag arguments
You can use `Flags` type to capture a list of flags:
```
function f(flags: Flags, ...): list<str> {
    return flags
}
f(a, b, c) // returns ["a", "b", "c"]
```

`Flags` can also accept names of flags inside `<>`, and will use them to create boolean arguments:
```
function f(flags: Flags<a, b, c>, ...) {
    return [a, b, c]
}

f(a, c) // returns [true, false, true]
```

Here is a more practical example:
```
function open_file(path: string, flags: Flags<read, write, create, erase>, ...) {
    if read {
        print "The file is readable"
    }
    if write {
        print "The file is writeable"
    }
    if create {
        print "If there is no file with this name, create a new one"
    }
    if erase {
        print "Erase the contents of the file"
    }
}

open_file("file.txt", read, create) // will print "The file is readable" and "If there is no file with this name, create a new one", but nothing else.
```
