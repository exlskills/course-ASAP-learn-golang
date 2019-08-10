### The Essential Datatypes

This section covers some of Go's more advanced 'basic' types that are natively included in the language

```go
package main

import (
    "fmt"
)

func main() {
    // There are also the following variations of unsigned integers:
    // uint8 uint16 uint32 uint64 uintptr
    var unsignedIntegerDefault uint = 100
    fmt.Println("unsignedIntegerDefault = ", unsignedIntegerDefault)

    // Bytes are used quite frequently when dealing with strings in Go, and they are
    // provided as an alias for uint8 (0-255)
    var myByte = 65
    // Since 65 is 'A' in ASCII, we'll see it is easily represented as both a number and character
    // We use type convertion (casting) of an int to a byte and then we use that byte as an object of the byte slice
    // so we can convert that byte slice to a string (65(int) -> 65(byte) -> []byte{65(byte)} -> "A"(string)
    fmt.Println("My byte as a number:", myByte, "My byte as a character:", string([]byte{byte(myByte)}))
    
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

```

### Keep it Idiomatic!

In Go, we:

* Quite frequently cast `string` values to `[]byte` and vice-versa for many operations that involve buffers, datasets, etc.

* Often use the explicitly sized type (i.e., `int64` or `float64`), except when indexing an array/slice where must the `int` type
