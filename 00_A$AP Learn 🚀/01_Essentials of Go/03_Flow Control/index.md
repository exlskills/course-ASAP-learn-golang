### Building Logic and Controlling Execution Flow in Go

Flow control in Go will feel very familiar to many developers who already have an experience with a language that has a more or less C-style syntax. There are some neat syntactical benefits that Go offers, which allows to have cleaner and often terser code.

#### Available Statements

* `if`, `if`/`else`, `if`/`else if`/`else` are the most commonly-used logical statements

* `switch` (with `case`s and a `default`) is a common way to manage flow based on the value of a single variable

* `for` is the only way to loop in Go (we **don't** have `while` loops in Go)
    
    * To iterate over a slice/array/map we can use the `range` keyword (explained in the code below)

* `goto` is available in Go, however, it is infrequently used (although there are definitely some use cases for it, where it helps to simplify code)

#### Flow Control in Practice

```go
package main

import (
    "fmt"
)

func main() {
    showIfStatements()
    showSwitchStatements()
    showForStatements()
    fmt.Println("Got", showGotoStatements(), "from showGotoStatements()")
}

func showIfStatements() {
    t, f := true, false
    // Note that `if` statements don't have parentheses, only braces around the code block
    if true {
        fmt.Println("True is definitely true!")
    }

    if t == f {
        fmt.Println("Nope!")
    } else if t != f {
        fmt.Println("Makes sense")
    } else if t == !f {
        fmt.Println("Also true, but we didn't get this far")
    } else {
        fmt.Println("Nope ...")
    }
}

func showSwitchStatements() {
    // Sometimes it's best to use switch in order to avoid long chains of if/else if/else's
    myNum := 21
    switch myNum {
    case 1,2,42:
        // This would get called if myNum == 1 OR 2 OR 42 
        fmt.Println("Cool. myNum is either 1, 2, or 42")
        // ⚠️ Remember that in Go, cases DO NOT fall through by default!
        break // So this is actually redundant here, but just to show that we _can_ manually break as well
    case 3:
        // This is a no-op case, i.e., if we _want_ to do nothing when myNum == 3
    case 5:
        // ⭐ If you want to fall through, you can (the fallthrough must be the last line of that case)
        fallthrough
    default:
        // Optionally, we can have a default case that gets applied either via an explicit fallthrough or when no case matches
    }
}

func showForStatements() {
    // Setup some dummy data (these will be explained in "Core Datatypes" so don't worry if they are unfamiliar right now)
    colorsMap := map[string]string{
        "AliceBlue": "#F0F8FF",
        "AntiqueWhite": "#FAEBD7",
        "Aqua": "#00FFFF",
        "Aquamarine": "#7FFFD4",
        // NB: Go requires this trailing comma on the last element when initializing values over multiple lines
        "Azure": "#F0FFFF",
    }
    colorsSlice := make([]string, 0, len(colorsMap))

    // Here, we use the `range` keyword to have Go iterate over the `colorsMap` and set `name` to the "key" in
    // the map, while setting `hexStrValue` to the value for that particular key in the map.
    for name, hexStrValue := range colorsMap {
        colorsSlice = append(colorsSlice, fmt.Sprintf("%s:%s", name, hexStrValue))
    }

    // You can also use range on slices (as below) -- and even `chan`s (explained further in concurrency)
    // If you don't need the key or index, you can use the blank `_` to ignore it
    for _, colorPairValue := range colorsSlice {
        fmt.Println(colorPairValue)
    }

    // NB: Like `if` statements, `for` loops do not use parenthesis -- just braces around the inner block

    // Iteration in Go is essentially the same as in Java/C/C++
    for x := 0; x < 3; x++ {
        // Remember that variables declared in `for` and `if` statements or blocks are scoped to that block 
        fmt.Println("iteration", x)
    }

    // Since we don't have while loops, we can also use this form of `for` to create infinite loops
    for {
        // `break` allows us to leave the for loop
        break
        // `continue` allows us to move on to the next iteration in the loop (leaving the current iteration)
        continue // This wouldn't be reached in this case, as we used `break` to leave above
    }
}

func showGotoStatements() int {
    // This example isn't particularly useful (for clarify and brevity)
    fmt.Println("Let's see how goto works...")
    i := 0
    if i == 0 {
        goto namedBlock
    } else {
        goto otherBlock
    }
    // That's it, the goto will make one of these blocks finsh and return from the function

passBack:
    return i

namedBlock:

    fmt.Println("Hey, I was invoked via a goto statement!")
    i++
    goto passBack

otherBlock:

    fmt.Println("Hey, I'm another block. Did I get invoked??")
    i--
    goto passBack
}

```

#### Keep it Idiomatic!

In Go, we generally:

* Avoid the use of the `_` blank identifier unless we have a significant reason to do so, or are calling a function or using `range` in a for-loop where we have multiple assignment, but we don't need some of the returned values. By the way, you shouldn't just throw your `error`s from function calls into `_`, because if someone is sending you back a potential `error` value, you should check it!!!

* Using the `:=` is convenient and idiomatic, however, remember that you can sometimes end up with multiple copies of a variable if, for instance, you have an `err` outside of a for loop, and then you have another `err` that you create via the `:=` operator, you will actually be using a completely different variable on each iteration of your loop! Something to keep in mind! 

* It's not _that_ common to pass functions around as arguments in Go, primarily since we prefer to use goroutines for concurrency, rather than 'callbacks' (like JS does, for instance), however it's still used for things like walking a file tree or applying a specific transformation to some data that another function is analyzing

#### Limitations of Variables in Go

Perhaps the most annoying thing for some people coming from languages such as Python or JS, will be the fact that Go variables are statically-typed, which is actually pretty awesome in it's own way! People from statically-typed languages, such as Java or C will find this natural. Just remember that the type system -- and its somewhat rigid approach -- is actually there to help you build maintainable, self-documenting, and performant code! Once you get used to it, you might have a hard time going back to a dynamically-typed language!
