### Working with Structured Data

So far we've covered the range of 'built-in' data types, however, the most powerful data types are the ones that you create! Go offers the `type Name struct {}` syntax for creating your own structs that work a lot like classes in Java/C++. 

### Note: is Go Object-oriented?

Sort of. Throughout the sections on structs, interfaces, and methods, you will find that Go offers what is in essence a 'lightweight' object-oriented programming paradigm, coupled with many functional programming features, such as the ability to pass functions around like variables. Since Go doesn't really support 'type hierarchy' (aside from embedding, covered below) most users will find that Go is a most 'lean' implementation of core OOP.

### Structs

```go
package main

import (
    "fmt"
)

// Warning: Boring obligatory OOP-ish example ahead :/
type Vehicle struct {
    Make  string
    Model string
}

type Car struct {
    // You know... The doors that open up instead of out to the side ¯\_(ツ)_/¯ 
    VerticalDoors bool
    // Embedding, so for example, users will be able to access carObj.Vehicle.Make and carObj.Make now!
    // NB: This is *NOT* subclassing, so you won't be able to put a Car into a slice of type Vehicle, for instance!
    //     For use cases like that, you must utilize methods and interfaces (covered in the Methods and Interfaces sections, respectively)
    Vehicle
}

func main() {
    teslaModelX := Car{
        VerticalDoors: true,
        // NB: You can't set embedded fields in-line here, you must use the following explicit syntax
        Vehicle: Vehicle{
            Make: "Tesla",
            Model: "X",
        },
    }
    fmt.Println(teslaModelX) // Prints out {true {Tesla X}}
    // Access an embedded field from the parent
    fmt.Println(teslaModelX.Model) // Prints out X
}

```

### Keep it Idiomatic!

* Structs (and subsequently methods and interfaces) are a HUGE part of Go, so utilize them liberally

* Generally when we want to expose an API for other developers, we try to accept interfaces rather than structs, because interfaces in Go are implicitly evaluated, whereas structs must be explicitly used (also further complicated by your dependency management tool, where sometimes you're just using a different copy of the same library and Go will refuse to compile your source, since it's not *exactly* the same struct per se)

* Don't overuse struct embedding. It is not really a 'core' feature of the Go language, occasionally has some awkward/unexpected behaviors, and can often be avoided without introducing too much other overhead
