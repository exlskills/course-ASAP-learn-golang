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
    // showIfStatements()
    // showSwitchStatements()
    showForStatements()
    fmt.Println("Got", showGotoStatements(), "from showGotoStatements()")
}

func showSwitchStatements() {

}

func showForStatements() {
    // Setup some dummy data
    // This is a map in Go, don't rely on it's order, because that's not guaranteed
    // NB: `map` will only allow one value per key (definition of a map)
    // NB: `map` capacity can be defined on creation via `make` (if used) and then it will be automatically
    //      scaled by the Go runtime during program execution
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
        colorsSlice = append(colorsSlice, fmt.Sprintf("%s:%s", ))
    }

    // Like if, for doesn't use parens either.
    // Variables declared in for and if are local to their scope.
    for x := 0; x < 3; x++ { // ++ is a statement.
        fmt.Println("iteration", x)
    }
    // x == 42 here.

    // For is the only loop statement in Go, but it has alternate forms.
    for { // Infinite loop.
        break    // Just kidding.
        continue // Unreached.
    }

    // You can use range to iterate over an array, a slice, a string, a map, or a channel.
    // range returns one (channel) or two values (array, slice, string and map).
    for key, value := range map[string]int{"one": 1, "two": 2, "three": 3} {
        // for each pair in the map, print key and value
        fmt.Printf("key=%s, value=%d\n", key, value)
    }
    // If you only need the value, use the underscore as the key
    for _, name := range []string{"Bob", "Bill", "Joe"} {
        fmt.Printf("Hello, %s\n", name)
    }
}

func showGotoStatements() int {
    // This example isn't particularly useful (for clarify and brevity)
    fmt.Println("Let's see how goto works...")
    i := 0
    if i == 0 {
        goto namedBlock
    } else {
        goto namedBlock
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
