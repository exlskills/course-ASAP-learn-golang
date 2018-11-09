### How to Use Variadic Functions

#### What are varaidics and why do they exist?

Variadics exist because sometimes we want to take 0 or more arguments in a function. Imagine a string concatenation function, you would like the caller to easily be able to call your function with the strings that they want concatenated!

```go
package main

import (
    "fmt"
)

func main() {
    // Example invocation of a variadic with some string literals
    fmt.Println(exampleVariadic("This", "Will", "Accept", "However", "Many", "Args", "We", "Pass"))
    
    // Example invocation of a variadic without any args (this is totally valid)
    fmt.Println(exampleVariadic())
}

// This syntax is used to define a simple variadic
// NB: If you wanted to require at least one arg, then you could define it as:
//     func exampleVariadic(firstArg string, extraArgs... string)
func exampleVariadic(args... string) []string {
    return args
}

```
