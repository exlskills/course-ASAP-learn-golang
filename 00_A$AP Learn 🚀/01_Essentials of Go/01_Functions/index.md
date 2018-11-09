### Define and Call Functions

Functions transform input arguments and do things for us in Go -- same is just about any other programming language. They are extremely flexible, as you'll soon find out!

#### Functions in Go are Awesome...

We can define functions that:

* Are usable only within our own package or globally (keep reading to find out how to expose global functions)

* Take multiple parameters

* ⭐ Return 0 or more values (i.e., we can have a 'void' function, or we could have a function that returns several values)

* Take a variable number of arguments (Explained in variadic functions extras)

* ⚠️ Remember that most arguments are 'passed by value' -- more on this in the 'Pass by Reference vs. Pass by Value' section as there are a number of considerations one must take into account

* ⭐ Whenever you have some functions for a specific data type (frequently a struct), you can create a "method" which is a function with a "reciever" (think class and instance method...) and then those functions will be bound to instances of that data type! Cool! (Read more in the "Methods" section)

... And functions can be passed around just like variables in Go! ⭐

```go
package main

import (
    "fmt"
    "strings"
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

    // Show me a use case for passing around functions!
    // NB: the `:=` operator is a short-hand way of declaring and defining variables, more on this in the Variables section!
    returnedFunc := funcFactory("Show", "how", "much", "cool.")
    fmt.Println(returnedFunc("Wow", "such", "factory."))
    fmt.Println(returnedFunc("See!", "There", "is", "a", "use", "case!"))
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

// By the way, this is also a variadic function, you can check that out in the extras in a section called "Variadic Functions"
func funcFactory(prefix... string) (func(suffix... string) string) {
    return func(suffix... string) string {
        return strings.Join(append(prefix, suffix...), " ")
    }
}

```

#### Keep it Idiomatic!

In Go, we generally:

* Always return the `error` (if applicable) from the function as the LAST value. I.e, `return result, err`

* If the return values could be non-obvious, it's always best to have named return values (even though you technically don't have to)

* Always remember to create functions that describe what the function does

* It's not _that_ common to pass functions around as arguments in Go, primarily since we prefer to use goroutines for concurrency, rather than 'callbacks' (like JS does, for instance), however it's still used for things like walking a file tree or applying a specific transformation to some data that another function is analyzing

#### Limitations of Functions in Go

The primary limitation that many Java developers will notice is the inability to overload a function, because Go only uses the package scope and the function name to identify functions uniquely. This also comes with some neat benefits, chiefly the fact that you get cleaner compiler errors and it's harder to pass the wrong things into a function, or worse yet, call the wrong function by accident!
