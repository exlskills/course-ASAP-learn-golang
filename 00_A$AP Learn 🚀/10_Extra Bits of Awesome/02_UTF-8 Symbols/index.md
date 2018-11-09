### So yeah... You can do that in Go

#### ⚠️ 世界 is a valid name for a symbol (variable, function, etc.) in Go

```go
package main

import (
    "fmt"
)

func main() {
    // ⚠️ So this is technically legal, will compile, and will work
    世界 := 42
    fmt.Println(世界)
    fmt.Println("No. Way. Don't do that to me :p")
}

```

#### Probably don't do this...

This is a cool thing to know about Go, but probably best to keep your variable and function names ASCII (basically a-z,A-Z,0-9). It is most definitely **not** an idiomatic way to develop software in Go.
