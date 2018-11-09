### Declare and Define Variables

Variables are how we store the vast majority of our data in memory while a Go program is executing. Go gives us a number of 'basic' datatypes that are a part of the language, some higher-order types for storing lists and maps, and some pointer awesomeness!

#### Some key things to remember about declration/assignment in Go

Remember that:

* Frequently type is inferred during the initialization by using the `:=` operator

* Variables are mutable (we can change them as much as we want after we create them)

* ⭐ We have pointers in Go (discussed in more detail in the "Pointers" section), however, remember we don't need any pointer arithmetic

* Functions can also be defined, assigned, and passed around like variables, however, this isn't really that common of a use case for variables in most Go applications

#### Want to read more about types???

We thought so, check out the section called "Types Overview," as this section is more about using variables than the specific data types.

#### Let's do some stuff with variables!

```go
package main

import (
    "fmt"
)

// We use this syntax to define a function in Go
// The function `main()` is reserved for the entry-point of our program
func main() {
    // Invoke the sayHelloAndReturnNothingness function without any parameters
    sayHelloAndReturnNothingness()
    // Call getMyNumber() and print out the response using fmt
    fmt.Printf("getMyNumber() -> %d\n", getMyNumber())
    // Use the combined type inferrence, declaration, and assignment operators to get the results of the getMyFavoriteNumber func
    myFaveNum, reason := getMyFavoriteNumber()
    // NB: Remember that fmt.Println will put spaces between arguments for us!
    fmt.Println("My Favorite Number:", myFaveNum, "For The Reason:", reason)
    // Let's pass a function to another function as an argument
    fmt.Println(giveMeSomeFunction(getMyNumber))
}

// This straightforward syntax defines a *package-wide* function that doesn't return anything
func sayHelloAndReturnNothingness() {
    fmt.Println("I'm a F U N C T I O N in Go! Cool.")
}

// Let's return an `int` from this function
func getMyNumber() int {
    return 42
}

// Let's return more than one value from a function (OMG, we can really do that? You bet!)
// NB: whenever using 'named' return values, they are (1) automatically defined with their default values and;
//     (2) we must use parenthesis around them to let Go know what we're up to!
func getMyFavoriteNumber() (num int, reason string) {
    // Since Go defined `num` and `reason` from the function signature above, we can use the assignment operator `=`
    num = 42
    reason = "#TheAnswerToLife"
    // Below, Go implicitly returns `num` and `reason`, however, the following would also be equivalent:
    // `return num, reason` which in this case would also be equivalent to:
    // `return 42, "#TheAnswerToLife"`
    return
}

func giveMeSomeFunction(f func () int) int {
    return f()
}

```

#### Keep it Idiomatic!

In Go, we generally:

* Always return the `error` (if applicable) from the function as the LAST value. I.e, `return result, err`

* If the return values could be non-obvious, it's always best to have named return values (even though you technically don't have to)

* Always remember to create functions that describe what the function does

* It's not _that_ common to pass functions around as arguments in Go, primarily since we prefer to use goroutines for concurrency, rather than 'callbacks' (like JS does, for instance), however it's still used for things like walking a file tree or applying a specific transformation to some data that another function is analyzing

#### Limitations of Functions in Go

The primary limitation that many Java developers will notice is the inability to overload a function, because the Go only uses the package scope and the function name to invoke functions
