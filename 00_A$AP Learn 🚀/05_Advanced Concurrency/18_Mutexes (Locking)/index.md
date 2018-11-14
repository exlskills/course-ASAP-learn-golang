### Avoid Data Race Conditions

What happens when multiple goroutines need to concurrently read and write data that's not concurrent-safe (most datatypes in Go aren't concurrent-safe, by the way...)? We generally use mutexes -- they are a lightweight and idiomatic solution. You may also apply channels to this use case, however, frequently the control flow of mutexes is easier for developers to understand and it's simpler to implement.

```go
package main

import (
    "fmt"
    "sync"
)

// We'll need to increment this from each goroutine that's computing n*n
var sumOfSquares = 0
// This our mutex -- it will help us to queue up calls that might come in concurrently, and then execute them synchronously
var sumOfSquaresMutex sync.Mutex

func main() {
    // Create a new WaitGroup -- this will help us to wait until all goroutines are done computing n*n and incrementing our result var
    // To learn more about WaitGroups checkout the "WaitGroup" section
    var wg sync.WaitGroup

    // Let's pretend we're doing something useful here -- like iterating over a slice of datasets
    for i := 1; i < 11; i++ {
        wg.Add(1)
        go addNumber(i, i*10+i, &wg)
    }

    wg.Wait()
    // Yay we finished!
    fmt.Println("All goroutines done. The sum of squares is", sumOfSquares)
}

func addNumber(idx, n int, wg *sync.WaitGroup) {
    // Best practice is to defer this call, so that regardless what happens, even if there's a panic elsewhere in the goroutine,
    // the main goroutine will not be stuck forver (waiting on the wg.Wait() to return)
    defer wg.Done()
    // Compute the square
    sq := n*n
    // NB: In the event that you might want to be able to notify the main goroutine of an error, the idiomatic way to do that would be
    //     to pass errors into a `chan` that the main goroutine would check for errors after all of the goroutines have returned
    fmt.Println("In goroutine number", idx, "and we found that n*n =", sq, " and we're adding it to the result variable")
    // Call our helper function to update the sum of squares
    incrementSumOfSquares(sq)
}

func incrementSumOfSquares(n int) {
    defer sumOfSquaresMutex.Unlock() // Ensure that once we're done, we unlock (regardless if a panic occurs)
    sumOfSquaresMutex.Lock()
    sumOfSquares += n
}

```
