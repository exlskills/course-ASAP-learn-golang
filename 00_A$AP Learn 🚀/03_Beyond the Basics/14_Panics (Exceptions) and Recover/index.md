### "Exceptions" in Go

Having already covered Go's `error` type, you think that we might be done talking about error handling, however, that's not quite it! Go also has the concept of `panic` and `recover` which is for handling what are generally critical/fatal errors, such as segmentation faults, impossible type casts, or nil pointer exceptions.

### Panic

In Go, `panic(string)` is a built-in function that stops the ordinary flow of control and begins panicking. When the function F calls panic, execution of F stops, any deferred functions in F are executed normally, and then F returns to its caller. To the caller, F then behaves like a call to panic. The process continues up the stack until all functions in the current goroutine have returned, at which point the program crashes. Panics can be initiated by invoking panic directly. They can also be caused by runtime errors, such as out-of-bounds array accesses.

### Recover

In Go, `recover()` is a built-in function that regains control of a panicking goroutine. Recover is only useful inside deferred functions. During normal execution, a call to recover will return nil and have no other effect. If the current goroutine is panicking, a call to recover will capture the value given to panic and resume normal execution.

### In Practice

Here's an example program that demonstrates the mechanics of `panic`, `recover`, and `defer`:

```go
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}
```

The function `g` takes the `int i`, and panics if `i` is greater than `3`, or else it calls itself with the argument `i+1`. The function `f` defers a function that calls `recover` and prints the recovered value (if it is non-nil). Try to picture what the output of this program might be before reading on.

The program will output:

```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
Recovered in f 4
Returned normally from f.
```

If we remove the deferred function from `f` the panic is not recovered and reaches the top of the goroutine's call stack, terminating the program. This modified program will output:

```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
panic: 4
 
panic PC=0x2a9cd8
[stack trace omitted]
```

For a real-world example of panic and recover, see the json package from the Go standard library. It decodes JSON-encoded data with a set of recursive functions. When malformed JSON is encountered, the parser calls panic to unwind the stack to the top-level function call, which recovers from the panic and returns an appropriate error value (see the `error` and `unmarshal` methods of the `decodeState` type in `decode.go` in `encoding/json`).

### Keep it Idiomatic!

* The convention in the Go libraries is that even when a package uses panic internally, its external API still presents explicit error return values.

* Other uses of defer (beyond the `file.Close` example given earlier) include releasing a mutex (covered in greater detail in Advanced Concurrency):

```
mu.Lock()
defer mu.Unlock()
```

Or printing a footer:

```
printHeader()
defer printFooter()
```

and more!

### Attribution

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License.

