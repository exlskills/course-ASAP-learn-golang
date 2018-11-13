### Defining immutable values

Similar to many other languages, Go offers a way to declare and define 'variables' that are immutable.

### Example

```go
package main

import (
    "fmt"
)

// A package-wide const
const foobar = "FoObAr"
// A global const
const ThisIsGlobalFoo = "FOO"

func main() {
    const thisWorksToo = 42
    fmt.Println("foobar =", foobar)
    fmt.Println("ThisIsGlobalFoo =", foobar)
    fmt.Println("thisWorksToo =", foobar)
}

```

### Keep it Idiomatic

In Go we generally:

* Use const only for values that we want to define throughout our codebase when we're coding the project -- it's not suitable for storing a configuration that you would load in via env vars, for example...

### Limitations

In Go, constants must be defined at compile time -- so you must know their values prior to compilation, thusly they are not suitable for creating runtime 'constants' as some other languages offer (such as Java/JS).
