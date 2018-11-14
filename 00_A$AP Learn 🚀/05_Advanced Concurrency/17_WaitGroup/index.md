### Wait for Goroutines to Return

In Go, we use the `sync.WaitGroup` structure in order to wait for a group of goroutines to finish their task prior to doing something else -- like maybe sending an email with a summary report to a user.

### In Practice

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    // Create a new WaitGroup
    var wg sync.WaitGroup
    // Let's pretend we're doing something useful here -- like iterating over a slice of datasets
    for i := 1; i < 11; i++ {
        // In this example, we increment one-by-one, however, you can also increment once by the total, e.g., `wg.Add(10)` in this case
        // NB: remember that the `wg.Add` call must occur before the corresponding `wg.Done`, because if `wg.Done` count exceeds the number added,
        //     we will encounter a panic in the goroutine that calls `wg.Done`
        wg.Add(1)
        go addNumber(i, i*10+i, &wg)
    }

    // This will block this goroutine until all other goroutines have called `wg.Done` and the count of 'outstanding' routines is therefore 0
    // NB: If you want to 'timeout' you can use Go's context timeout channel or even just the Timer chan factory in the "time" package
    wg.Wait()
    // Yay we finished!
    fmt.Println("All goroutines done, exiting...")
}

func addNumber(idx, n int, wg *sync.WaitGroup) {
    // Best practice is to defer this call, so that regardless what happens, even if there's a panic elsewhere in the goroutine,
    // the main goroutine will not be stuck forver (waiting on the wg.Wait() to return)
    defer wg.Done()
    // NB: In the event that you might want to be able to notify the main goroutine of an error, the idiomatic way to do that would be
    //     to pass errors into a `chan` that the main goroutine would check for errors after all of the goroutines have returned
    fmt.Println("In goroutine number", idx, "and we found that n*n =", n*n)
}
```

In the event that you need to 'throttle' the waitgroups, or process data in chunks, the SizedWaitGroup section is a great reference!
