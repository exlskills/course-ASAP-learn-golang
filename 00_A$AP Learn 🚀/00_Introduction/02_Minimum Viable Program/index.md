### Minimum Requirements for a Valid Program

#### Our Minimal Go Program

This is roughly the minimal, "Hello World," Go program. There are certainly ways to make it smaller, but we're just trying to make sure that we write more or less "idiomatic" Go code, which means code that generally adheres to the "Go way of doing things." This makes it much easier to join real Go projects, since Go developers try to use consistent constructs and approaches to common problems. 

```go
// All .go files must have a package declaration as the first line of source code
// NB: The `main` package is a special name that's used to build an executable, as opposed to a library
package main

// Go uses `import` to specify which packages this file requires
// NB: You will get a *compile-time* error if you import a package that you do not actually used
import (
    "fmt"
)

// We use this syntax to define a function in Go
// The function `main()` is reserved for the entry-point of our program
func main() {
    fmt.Println("This is a teeny tiny Go program!")
}
```

Now that you've got your first Go program running, let's really get started...
