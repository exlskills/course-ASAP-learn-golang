### Casting Compatible Basic Types

Type casting in Go is natively supported -- as in many other statically-typed languages -- and serves not to really convert data, but more allow us to seamlessly use the various numerical types (such as `int`, `int32`, etc.) interchangably when needed. Type casting **does not** do things like convert a `string` of "42" into an `int` of `42` -- and the Go compiler will actually fail to compile that, because `string`s and `int`s are not compatible types. P.S. If you need to do things like that, check out the utilities in the standard library -- such as the `strconv` package!

```go
package main

import (
    "fmt"
)

func main() {
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

```
