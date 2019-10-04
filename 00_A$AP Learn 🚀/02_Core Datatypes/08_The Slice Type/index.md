### Storing Ordered Lists of Data

Go has a unique type system in the sense that it has two ways of storing ordered lists of data -- slices and arrays -- neither of which, for your information, are actually keywords in the language... So what's the difference? Arrays have a pre-defined length **at compile time (!)** whereas slice length is managed at runtime! Also, these two types are totally incompatible (as in you can't cast an array of a type to a slice of that type), so you're either using a slice or an array.

Due to the aforementioned concerns, most Go applications, libraries, and the Go standard packages utilize slices almost exclusively, so for beginners it's best to just focus on slices. Arrays are covered in this course, but really just to make you aware of them -- they aren't used in any other examples, etc. This will give you a consistent behavior and later when you're ready to explore more Go, you can consider learning more about arrays!

```go
package main

import (
    "fmt"
)

// NB: Across these examples, we frequently use the built-in `len(something)` function,
//     that gives us the number of elements in our array, map, or slice

func main() {
    colorsMap := []string{"white", "black", "yellow", "blue", "green", "red", "pink", "purple", "lime", "grey"}
    
    // We use `make` to create an empty slice of our desired type, length, and, optionally, capacity
    // NB: Below, 0 is the length of our slice, and 10 is the capacity. This means that Go
    //     will allocate memory for 10 elements and `append()` will be a cheap operation until we exceed
    //     that capacity (where the Go runtime would automagically expand the slice for us -- expensive though!)
    // NB: `make([]string, 10)` would create a slice with 10 elements (each of which would be initialized 
    //     to their default value based on their type) and the capacity would be equal to the length.
    demoSlice := make([]string, 0, len(colorsMap))

    // Slice will be [] and len will be 0
    fmt.Println("Empty slice:", demoSlice, "with len:", len(demoSlice))

    // We can also create slice literals, like so
    demoSlice = make([]string, 10)

    // Slice with defaulted elements
    fmt.Println("Slice filled with defaults:", demoSlice, "with len:", len(demoSlice))

    // Set values for specific indices
    demoSlice[0] = "21"
    demoSlice[9] = "42"

    // If you use an index, like 10 in this case, that is out of bounds, you will get a panic (similar
    // to an exception in some other languages)
    // e.g., `demoSlice[10]` would cause a panic, and in this case cause the program to exit with a failure status code

    fmt.Println("Get a slice of a slice:", demoSlice[:3])
    fmt.Println("Another slice of a slice:", demoSlice[3:])
    fmt.Println("Yet another slice of a slice:", demoSlice[2:6])
    fmt.Println("Valid, yet useless, slice of a slice:", demoSlice[:])

    letsAppendSlice := []string{"foo", "appendable"}
    // The append function will not modify the slices, so if you want to update the slice with the appended version, then you must also assign the result
    // NB: the letsAppendSlice... notation just expands that slice to send to the variadic append function
    demoSlice = append(demoSlice, letsAppendSlice...)
    // Would've also been equivalent to: `demoSlice = append(demoSlice, "foo", "appendable")`
    // demoSlice is now larger, letsAppendSlice is unchanged
    fmt.Println("Updated slice:", demoSlice)
}

```

### Keep it Idiomatic!

In Go, we:

* Almost always use slices instead of arrays... It's quite rare to see fixed length arrays used in Go applications outside of some very specific and deliberate use cases

### Common Limitations of Slices in Go

Remember that although slices are expandable -- this only means that Go will help you to allocate new chunks of memory automatically for you, e.g., it's nothing more than an abstraction. For this reason, changing the size of a slice must be explicitly performed via `append`.

Remember that on initialization, you can set the initial capacity of the slice. If you plan on adding a lot of elements to it in quick succession, it's best to pre-set the capacity to optimize all of your appends. This will actually make the appends happen in-place in memory, as opposed to making the Go runtime actually allocate memory chunks and move the slices around

