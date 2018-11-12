### Most Commonly Used Basic Datatypes

This section covers some of Go's most common 'basic' types that are natively included in the language

```go
package main

import (
    "fmt"
)

func main() {
    // In Go, the bool type is used for storing true/false data
    myBool := true // Go infers the type as bool
    // Can also define explicitly, `var myBool bool = true`, which would be equivalent in this case
    fmt.Println("myBool:", myBool)
    
    // Despite the fact that Go offers non-nil defaults for most datatypes, pointers most notably, may
    // frequently be `nil` which is a specially state like null or None in many other languages
    // that implies the variable has no value. Variables with a non-nil default, may never be nil!
    var err error = nil
    fmt.Println("error?:", err)
    // NB: Since int has a non-nil default, then `var myInt int = nil` would not even compile

    // Strings in Go are very flexible, UTF-8 compatible, and they are stored as byte slices
    // NB: We use the \ for escaping " and for special characters, such as newlines
    var helloWorldStr = "Hello, 世界.\nThat means, \"Hello, World.\""
    fmt.Println(helloWorldStr)
    // We can also use backticks to create raw multi-line strings
    helloWorldStr = `Hello, 世界.
That means, "Hello, World."`
    fmt.Println("We created this with `` syntax:", helloWorldStr)

    // Integers are usually defined like this (Go infers the `int` type)
    myInt := 42
    fmt.Println("myInt:", myInt)
    // For specifying 64-bit integers specifically, we can use this notation
    var myInt64 int64 = 21
    fmt.Println("myInt64:", myInt64)

    // Similar terms go for floating point numbers
    myFloat := 42.42
    fmt.Println("myFloat:", myFloat)
    // For specifying 64-bit floats specifically, we can use this notation
    var myFloat64 float64 = 21.21
    fmt.Println("myFloat64:", myFloat64)
}

```

