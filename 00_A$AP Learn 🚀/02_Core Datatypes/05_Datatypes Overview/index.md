### The Essential Datatypes

Go is a statically-typed language with some 'basic' datatypes, arrays and slices, pointers, and maps. We also have structs and interfaces, however, those are covered in their respective sections, "Structs" and "Interfaces"

#### ⚠️ BEWARE Potential Confusion Ahead! ⚠️ 

* ⚠️ Many new Go developers struggle discerning arrays from slices because the syntax and apparent use case seems so similar, so the best advice for new Go developers is just don't use arrays! Call everything a slice, and always use slice notation when you need an 'array-like' thing. This will give you a consistent behavior and later when you're ready to explore more Go, you can consider learning more about arrays

* ⭐ For more complex data structures (or if you're looking for something similar to a 'class' in Java/C++), then head over to the dedicated "Structs" section. Structs are not discussed here

* ⭐ For learning about interfaces, check out the "Interfaces" section as they are not discussed here

* ⭐ For learning about `chan` (channels), check out the "Channels" section as they are not discussed here

* ⭐ For learning about the `error` type, check out "The Error Type" section as it's not discussed here

* ⚠️ Go doesn't offer generics. The most 'generic-like' option would be `interface{}` which is an empty interface, and since everything (`nil` included) satisfies the empty interface type, then this becomes a catch-all for 'send me anything.' This is described in further detail in the "Interfaces" section

* Go types `int`, `uint`, `float`, `complex64`, and `complex128` have numeric suffixes that specify how many bits they fit. For instance, `uint8` is an 8-bit unsigned integer, while `complex128` stores 128-bits (two `float64`s worth of data).

* Go types `int`, `uint`, and `float` offer 'default' versions which are runtime-specific. On 32-bit systems, these types are all equivalent to their 32-bit versions, while on 64-bit systems they are all equivalent to their 64-bit versions. Many Go developers (and a lot of the standard library) uses the 64-bit versions explicitly for consistency and predictability, i.e., `int64`. **However,** remember that lengths, indices, and capacities are always of the `int` type, so keep that in mind!

```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    // The most common basic types are covered here
    showCommonBasicTypes()

    // Show basic type casting
    showBasicTypeCasting()

    // Across these examples, we frequently use the built-in `len(something)` function,
    // that gives us the number of elements in our array, map, or slice
    showSlices()
    showMaps()

    // More advanced basic types are explained here
    showAdvancedBasicTypes()

    // Again, only look at the array-related stuff if you want to...
    // It's not really recommended or needed for beginning Go developers as it usually just confuses
    showArrays()
}

func showCommonBasicTypes() {
    // In Go, the bool type is used for storing true/false data
    myBool := true // Go infers the type as bool
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

func showBasicTypeCasting() {
    // As with most other languages, casting between compatible types is supported, however,
    // it may cause data leakage (this isn't the case for string<->[]byte since a string
    // is actually stored as a []byte anyways)

    var myString = "Hello, 世界"
    fmt.Println([]byte(myString))
    // And you'll see that this loss-less
    fmt.Println(string([]byte(myString)))

    // This one is over 32 bits
    var i64 int64 = 9999999999999
    // This will not be 
    var f64 float64 = 99.95
    // Smaller numbers are fine!
    var smallInt int32 = 42

    // Since f64 has a digits behind the decimal it gets truncated and we'll see just 99 printed
    fmt.Println("Float to int (works but could lose data - truncation/fit):", int32(f64))
    // Since i64 is too large for an int32, we will see 1316134911 printed
    fmt.Println("Int64 to in32 (works but could lose data if it doesn't fit):", int32(i64))

    // *However*, those are edge cases, usually it works just fine!
    // NB: Also if you're going up in size you won't lose data (apart from decimal truncation on float->int casts)
    fmt.Println("Small to large casting is fine:", int64(smallInt))
    fmt.Println("Int to float casting may lose data if too large, but usually fine:", float64(smallInt))
}

func showSlices() {
    // We use `make` to create an empty slice of our desired type, length, and, optionally, capacity
    // NB: Below, 0 is the length of our slice, and 10 is the capacity. This means that Go
    //     will allocate memory for 10 elements and `append()` will be a cheap operation until we exceed
    //     that capacity (where the Go runtime would automagically expand the slice for us -- expensive though!)
    // NB: `make([]string, 10)` would create a slice with 10 elements (each of which would be initialized 
    //     to their default value based on their type) and the capacity would be equal to the length.
    demoSlice := make([]string, 0, len(colorsMap))

    // Slice will be [] and len will be 0
    fmt.Println("Empty slice:", demoSlice, "with len:" len(demoSlice))

    // We can also create slice literals, like so
    demoSlice = make([]string, 10)

    // Slice with defaulted elements
    fmt.Println("Slice filled with defaults:", demoSlice, "with len:" len(demoSlice))

    // Set values for specific indices
    demoSlice[0] = 21
    demoSlice[9] = 42

    // If you use an index, like 10 in this case, that is out of bounds, you will get a panic (similar
    // to an exception in some other languages)
    // e.g., `demoSlice[10]` would cause a panic, and in this case cause the program to exit with a failure status code

    fmt.Println("Get a slice of a slice:", demoSlice[:3])
    fmt.Println("Another slice of a slice:", demoSlice[3:])
    fmt.Println("Yet another slice of a slice:", demoSlice[2:6])
    fmt.Println("Valid, yet useless, slice of a slice:", demoSlice[:])
}

func showMaps() {
    // This is a map in Go, don't rely on it's order, because that's not guaranteed
    // NB: `map` will only allow one value per key (definition of a map)
    // NB: `map` capacity can be defined on creation via `make` (if used) and then it will be automatically
    //      scaled by the Go runtime during program execution
    colorsMap := map[string]string{
        "AliceBlue": "#F0F8FF",
        "AntiqueWhite": "#FAEBD7",
        "Aqua": "#00FFFF",
        "Aquamarine": "#7FFFD4",
        // NB: Go requires this trailing comma on the last element when initializing values over multiple lines
        "Azure": "#F0FFFF",
    }

    // Extract a value with the key
    _ = colorsMap["AliceBlue"]

    // Set a value for a key (if the key already exists, the value will be overwritten,
    // if the key doesn't yet exist, then Go will create it for us and scale the map if/as needed)
    colorsMap["Beige"] = "#F5F5DC"

    // If a key doesn't exist, go will return the default value (based on the value type), in this case
    // an empty string ("")
    fmt.Println("This key 'Bisque' doesn't exist: ", colorsMap["Bisque"])

    // NB: maps can be used in `range` for-loops, e.g., `for key, value := range myMap { ... }`,
    //     just remember that you can't count on the order of the elements!
    
    fmt.Println("colorsMap:", colorsMap)
}

func showAdvancedBasicTypes() {
    // There are also the following variations of unsigned integers:
    // uint8 uint16 uint32 uint64 uintptr
    var unsignedIntegerDefault uint = 100
    fmt.Println("unsignedIntegerDefault = ", unsignedIntegerDefault)

    // Bytes are used quite frequently when dealing with strings in Go, and they are
    // provided as an alias for uint8 (0-255)
    var myByte = 65
    // Since 65 is 'A' in ASCII, we'll see it is easily represented as both a number and character
    fmt.Println("My byte as a number:", myByte, "My byte as a character:", string([]byte{myByte}))

    // Complex numbers have two parts -- the real and imaginary. In Go we represent them as a pair of either
    // `float32`s or `float64`s, depending on which complex datatype is used. See below:

    // complex64 allows `float32` real and imaginary parts
    var c64 complex64
    fmt.Println("c64 =", c64)
    
    // Also c64 by default
    c64Default := 21 + 42i
    fmt.Println("c64Default =", c64Default)
    
    // complex128 allows `float64` real and imaginary parts
    var c128 complex128
    fmt.Println("c128 =", c128)

    // Notice the syntax for the rune is a single-quoted character. This is specific to runes. You may not use ""
    var aRune rune = '🚀'
    // A rune is a single Unicode code-point and is actually just an alias for `int32`, so this will print a number
    fmt.Println("Rocket ship code-point =", aRune)
    // Since it's an integer, we can also just set it as such
    // 128640 == 🚀, whereas 11088 == ⭐
    aRune = 11088
    fmt.Println("Star code-point =", aRune)
}

func showArrays() {
    // The difference in the syntax is that array initializations/declarations will always have a
    // number (or ellipsis, if inferrred) in the brackets to specify length or to have the length inferred from the
    // list of values provided in the {} after the type

    // When declaring an array with this format, Go initializes each value to its type's default (0 in this case)
    var firstArray [10]int

    // When declaring an array literal, we can use the [...] syntax to have Go infer the length of the literal
    // For example, this array will have a length of 3
    anotherArray := [...]int{21, 42, -21}

    // NB: Since arrays are fixed length (the length is immutable), we cannot append to arrays,
    //     and you also cannot append an array to slice, so use arrays at your own risk... :)
}

```

#### Keep it Idiomatic!

In Go, we:

* Almost always use slices instead of arrays... It's quite rare to see fixed length arrays used in Go applications

* Quite frequently cast `string` values to `[]byte` and vice-versa for many operations that involve buffers, datasets, etc.

#### Limitations of Functions in Go

The primary limitation that many Java developers will notice is the inability to overload a function, because Go only uses the package scope and the function name to identify functions uniquely. This also comes with some neat benefits, chiefly the fact that you get cleaner compiler errors and it's harder to pass the wrong things into a function, or worse yet, call the wrong function by accident!
