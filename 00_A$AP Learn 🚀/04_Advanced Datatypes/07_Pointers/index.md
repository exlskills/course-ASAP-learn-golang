### Cool there are pointers in Go!

Idiomatic Go code -- such as the standard library (or even the Go runtime itself!) -- is full of use cases for pointers! Whenever we want to pass something by reference -- and avoid creating a copy of it -- we send pointers. Go also abstracts away all of the deep details of the pointer implementation, so developers can focus on leveraging the higher-level features. Remember that the Go runtime's garbage collector will manage the references and reclaim memory automatically.

### Basic Pointers Example

```go
package main

import (
    "fmt"
)

type FooStruct struct {
    Children []*FooChild 
}

type FooChild struct {
    SampleVal int
}

func main() {
    ptrToFoo := giveMePointer()
    // Expect something like: &{[0x416020 0x416024 0x416028 0x41602c 0x416030 0x416034 0x416038 0x41603c 0x416040 0x416044]}
    fmt.Println(ptrToFoo)
    // Expect something like: Children: [0x416020 0x416024 0x416028 0x41602c 0x416030 0x416034 0x416038 0x41603c 0x416040 0x416044]
    fmt.Println("Children:", ptrToFoo.Children)

    for _, child := range ptrToFoo.Children {
        // Child (ptr): &{42} Dereferenced: {42}
        fmt.Println("Child (ptr):", child, "Dereferenced:", *child) // The * followed by a pointer, 'dereferences' a pointer and yields the value directly
    }

    // When you're playing with pointers, watch out for nil-pointer panics
    var thisIsNil *FooStruct
    // 💣  Boom! Nil-pointer panic and it's game over (this might be a good time to review panic/recover!)
    fmt.Println(thisIsNil.Children)
}

func giveMePointer() *FooStruct {
    // Initialize a FooStruct with an empty slice for children
    foo := FooStruct{
        // NB: The []*FooChild syntax means that we're going to have the slice be of pointers to FooChild's -- not directly FooChild's.
        //     Careful readers will note that this means we are *only* allocating the memory required to store 10 pointers, not 10 FooChild's
        Children: make([]*FooChild, 10),
    }
    // Populate the foo.Children slice with pointers to FooChild's
    for i := range foo.Children {
        // The &FooChild syntax initializes the object and gives us the pointer -- as opposed to the struct directly (short-hand notation)
        foo.Children[i] = &FooChild{
            SampleVal: 42+i,
        }
    }
    // Here, we use the &foo to return the pointer to foo, instead of a copy of foo
    return &foo
}

```

### What if I want to go crazy with pointers?

Go's `unsafe` packages offers 'unsafe' utilities for working with pointers. The warning is the name of the package. Have fun!

### Keep it Idiomatic!

* Use pointers liberally, they are frequently the most idiomatic way to pass data around

* Generally avoid pointers to slices, maps, or channels -- that's redundant (since those are already reference-based) and actually might make things harder, for instance, you can't use `range` with a pointer to a slice.
