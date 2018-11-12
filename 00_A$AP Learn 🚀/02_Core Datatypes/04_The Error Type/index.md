### What the `error`??

Go's built-in `error` type is the cause of a fair bit of controversy in the development community, but the fact is that the type is the idiomatic way to deal with non-fatal error conditions and it's definitely here to stay. It is distinctly different from the notion of `try`/`catch` or `throw`ing exceptions in other languages (like Java), because `error` types do not actually impact execution.

In Go, there is another error handling system of `panic`/`recover`, however, that is primarily for handling more fatal error conditions, like seg faults, and should not be used for higher-level errors, such as failing to parse a `string` into an `int` (for example).

This section will not discuss panics -- to read more about `panic` and `recover`, check the "Panics" section.

```go
package main

import (
    "fmt"
    "encoding/json"
    "errors"
)

// NB: Across these examples, we frequently use the built-in `len(something)` function,
//     that gives us the number of elements in our array, map, or slice

func main() {
    // Frequently, we'll be dealing directly with the errors like this
    err := doSomethingThatMightFail(43)
    if err != nil {
        fmt.Println("Oops! Something went wrong:", err)
    }
    fmt.Println("Wow everything worked!?")

    // Sometimes when delegating to a function wholly, we can let it handle the errors directly (this may often be common for top-level functions in your own code)
    printTheJSONIfPossible()
}

func doSomethingThatMightFail(someNum int) error {
    // Let's pretend we're actually doing something useful...
    if (someNum != 42) {
        // This is how we create our own errors -- as you can see, the primary feature of an error object is the error message that we provide
        return errors.New("invalid number passed for checking")
    }
    return nil
}

func printTheJSONIfPossible() {
    // Don't worry if you haven't seen some of the syntax below before, it will be explained in upcoming sections
    jsonObj := map[string]string{}
    err := json.Unmarshal([]byte("This isn't valid JSON!!!"), &jsonObj)
    // Main point to note here is how a standard library function (json.Unmarshal) returns an error, we store it in a variable called, 'err'
    // and subsequently check if it's not equal to `nil`. If that's the case, then that means there was an error and we should do something (like notify the user)
    if err != nil {
        // We can also call `err.Error()` to get the error message as a string in our code -- or we can just let fmt print it out for us 
        fmt.Println("There was an error parsing the JSON:", err)
        return
    }
	fmt.Println("Succesfully parsed the JSON:", jsonObj)
}

```

#### Keep it Idiomatic!

In Go, we:

* Always check the `error` value that a function returns. The assumption is that if a function is returning an `error`, then it's something that you should be checking!!

* Typically, if a function has a potential error state, it's good practice to forward that to the caller and let the caller deal with it. This also helps to avoid silent failures that could cause issues -- particularly fatal panics due to `nil` values or, even worse, silently sending bad data on to a DB or a user :(

* A `nil` error, is no error! (Also the only way to return without an error!)

* Always make sure that if a function returns an error, that the error is the right most (i.e., last) value that it returns

* When checking for errors, the most idiomatic format is as follows:

```go
err := doSomethingThatMightFail()
if err != nil {
    // Do something, frequently it means returning like this
    return err
}
// Otherwise, keep on keeping on...
```

--- Or a slightly shorter option if you want:

```go 
if err := doSomethingThatMightFail(); err != nil {
    // Do something, frequently it means returning like this
    return err
}
// Otherwise, keep on keeping on...
```

#### Limitations of the Error Type

The greatest upside -- yet at the same time drawback -- of the `error` type for managing errors is that it's implementation and checking is totally up to the function author and caller, respectively. On one hand it's awesome, because you have full control and can do whatever you want, but, of course, on the other hand, you have full control and can do whatever you want! The ultimate blessing/curse!

The other concern occasionally raised is how repetitive the error checking might feel -- it is quite frequent to see that 'if err != nil' block several times over in a function. This is how Go works, so the best answer is adding a tmeplate auto complete to your IDE and keep Go-ing ;)
