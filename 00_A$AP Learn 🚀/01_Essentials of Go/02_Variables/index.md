### Declare and Define Variables

Variables are how we store the vast majority of our data in memory while a Go program is executing. Variables in Go are statically-typed, so they all have a type and that type cannot be changed during execution. Go gives us a number of 'basic' datatypes that are a part of the language, some higher-order types for storing lists and maps, and some pointer awesomeness!

### Some key things to remember about declaration/assignment in Go

Remember that:

* We frequently use the `:=` operator to have Go declare, initialize, and infer the type of our variable based on the value used on the right side of the `:=` operator

* Variables are mutable (we can change them as much as we want after we create them)

* ⭐ Go has a built-in garbage collector, so when variables are no longer accessible, the Go runtime will reclaim the memory that was previously allocated. In the same stride, memory is also allocated automatically when you create new variables, expand slices, etc.

* ⭐ We have pointers in Go (discussed in more detail in the "Pointers" section), however, remember we don't need any pointer arithmetic

* Functions can also be defined, assigned, and passed around like variables, however, this isn't really that common of a use case for variables in most Go applications

### Want to read more about types???

We thought so, check out the section called "Datatypes Overview," as this section is more about using variables than the specific data types.

### Let's do some stuff with variables!

```go
package main

import (
    "fmt"
)

func main() {
    // We use the `var` keyword, followed by the variable name and type to declare a variable
    // NB: Go will also initialize the variable to its default value, which for an int is 0
    //     but for other types, that might be `nil`. So watch out! More on defaults in the "Datatypes Overview"
    var i int
    
    // We can assign a value to our variable
    i = 21

    // And we can re-assign as much as we want
    i = 42
    
    // NB: The type of a variable, once declared, cannot be changed
    //     e.g., this will cause an error `i = "Something that's not an int"`

    // NB: We must use a variable (not just declare and/or assign it), otherwise Go will
    //     give us a compile-time error! In the case that you just want to let the compiler
    //     know that you're smarter than it, you can basically use your variable in a 'throw away'
    //     assignment to a special character in Go (the "blank identifier")

    // The `_` symbol in Go is not a variable and cannot be accessed, it's just a sentinel value that
    // we sometimes use to tell the compiler, "hey, I know what I'm doing" (it's a no-op,
    // like writing to /dev/null on a *nix system)
    _ = i

    // NB: the `_` can also be used to import a package for a side-effect (like having that package's `init`
    //     function executed). This is somewhat common, however, you shouldn't just use this import a ton of 
    //     libs that you don't need as that will just slow down your compilation and increase the
    ///    size of your executable...

    // The short-hand := operator (very commonly used) allows us to declare and assign the variable, while having Go
    // infer the type for us. This doesn't mean that it doesn't have a type!
    y := 4
    _ = y

    num, err := getSomeNumber()
    // `nil` is a keyword that's similar to `null` or `None` in other languages
    if err != nil {
        fmt.Println("Oops! We got an error:", err)
        return
    }
    fmt.Println("Successfully got our num:", num, "err (is <nil>):", err)

}

// Sample function to show how to assign multiple values from a function call
func getSomeNumber() (int, error) {
    return 42, nil
}

```

### Keep it Idiomatic!

In Go, we generally:

* Avoid the use of the `_` blank identifier unless we have a significant reason to do so, or are calling a function or using `range` in a for-loop where we have multiple assignment, but we don't need some of the returned values. By the way, you shouldn't just throw your `error`s from function calls into `_`, because if someone is sending you back a potential `error` value, you should check it!!!

* Using the `:=` is convenient and idiomatic, however, remember that you can sometimes end up with multiple copies of a variable if, for instance, you have an `err` outside of a for loop, and then you have another `err` that you create via the `:=` operator, you will actually be using a completely different variable on each iteration of your loop! Something to keep in mind! 

* It's not _that_ common to pass functions around as arguments in Go, primarily since we prefer to use goroutines for concurrency, rather than 'callbacks' (like JS does, for instance), however it's still used for things like walking a file tree or applying a specific transformation to some data that another function is analyzing

### Limitations of Variables in Go

Perhaps the most annoying thing for some people coming from languages such as Python or JS, will be the fact that Go variables are statically-typed, which is actually pretty awesome in it's own way! People from statically-typed languages, such as Java or C will find this natural. Just remember that the type system -- and its somewhat rigid approach -- is actually there to help you build maintainable, self-documenting, and performant code! Once you get used to it, you might have a hard time going back to a dynamically-typed language!
