### Array-huh?

Sometimes we want to fix the length of an array forever -- and do so at compile time. If you want to do that, then check out arrays in Go!

```go
package main

import (
    "fmt"
    "strings"
)

// Across these examples, we frequently use the built-in `len(something)` function,
// that gives us the number of elements in our array, map, or slice

func main() {
    // NB: Again, only look at the array-related stuff if you want to...
    // It's not really recommended or needed for beginning Go developers as it usually just confuses
    
    // The difference in the syntax compared to slices, is that array initializations/declarations will always have a
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

### Keep it Idiomatic!

In Go, we:

* Almost always use slices instead of arrays... It's quite rare to see fixed length arrays used in Go applications

### Limitations of Arrays in Go

The primary feature -- and limitation -- of arrays in Go is the same: their size is defined (and fixed) at compile time. You can't use language features such as `append` on them, etc. So arrays do have their particular use cases, but it's generally a narrow range of applications
