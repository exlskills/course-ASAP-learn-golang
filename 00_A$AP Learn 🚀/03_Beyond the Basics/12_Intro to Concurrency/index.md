### Umm multi-threading?

Aside from a lot of the other awesome features that we get with Go, concurrency is probably the most critical -- especially when it comes to building scalable web services and applications.

### Goroutines

In Go, a goroutine is a 'lightweight thread of execution' that is managed by the Go runtime. Unlike 'raw' multi-threading, such as via the APIs in Java/C++, Go actually abstracts away the notion of a thread at the system level, and instead exposes the goroutines API that subsequently gets mapped (at runtime) into an optimized set of threads that the Go runtime manages. This means that unlike threads -- which typically require no less than 1MB of RAM and require a substantial setup/teardown overhead -- Go allows us to create goroutines that:

* Require only about 2KB of RAM per routine

* Can block without affecting any other goroutines (or locking actual threads!)

* Communicate with other routines via the native `chan` (channel) API which is a first-in, first-out queue for sending data between routines

* Are just normal functions! (Yup, there's nothing special about them -- which means it's super easy to build concurrent routines!)

This also means that it's 'idiomatic' to create thousands of goroutines at once (such as for HTTP requests!) that the runtime will optimize and schedule into actual threads on the system. It's also worth noting that, as with any other concurrent application, you must still consider and mitigate the potential for data races and faults that might result from writing/accessing a single segment of memory at the same time.

### Minimal example

```go
package main

import (
    "fmt"
)

func main() {
    // The `make function also initializes chan's for us -- similar to how it does so for maps and slices!
    // NB: By default chans have a queue of 1, which means once there is a value in the queue, it will **BLOCK** all further write actions, until the value is read.
    //     This is a common misconception for new Go devs and is remedied by supplying an integer length for the queue so that you can have a number of values stored
    //     in the FIFO queue prior to any of the operations being blocked.
    // NB: Conversely, read operations will be BLOCKING if there isn't a value to be read from the chan.
    //     This is remedied via select statements with a default (explained below)
    myIntsChan := make(chan int)
    
    // Start three concurrent goroutines. Numbers will be incremented
    // concurrently, perhaps in parallel if the machine is capable and
    // properly configured. All three send to the same channel.

    // Below we start three increment goroutines. This step is non-blocking, so execution in this function will continue without waiting for the incrementInt funcs to return
    // NB: Since the Go runtime manages their execution, we can't be sure which goroutines will start in what order and which or how many actual threads will be used
    // NB: Chans are passed by reference
    // NB: The go notation is how we invoke goroutines
    go incrementInt(21, myIntsChan)
    go incrementInt(42, myIntsChan)
    go incrementInt(12, myIntsChan)

    // Receive our 3 values via the myIntsChan
    // NB: This **will** block until Go is able to read three values from the myIntsChan!
    // NB: <- is "receive" operator that is only used on channels
    fmt.Println(<-myIntsChan, <-myIntsChan, <-myIntsChan)

    myStringsChan := make(chan string)
    myOtherStringsChan := make(chan string)
    // For clarity, since goroutines are really *just* functions, we can even write them inline like so
    go func() { myIntsChan <- 0 }()
    go func() { myStringsChan <- "woohoooo we're concurrent!" }()

    // The select statement looks a lot like a switch statement, however, it's only used for choosing a value from a set of channels
    // NB: It will select the case based on which chan it's able to get a value from first, and it's blocking (unless you give it a default case that would execute if none of the channels already have values that are ready to be read)
    select {
    case myInt := <-myIntsChan:
        // The above statement reads a value from the myIntsChan and writes it to our variable myInt
        fmt.Println("My int =", i)
    case <-myStringsChan:
        // The above statement reads a value from the myStringsChan, however, it discards it
        // This is useful if you just care about knowing a value *was* received -- not necessarily what that value is
        fmt.Println("boom got something on the myStringsChan channel!")
    case <-myOtherStringsChan:
        // Since we never write anything to the myOtherStringsChan channel, this case would never happen
        fmt.Println("didn't happen.")
    }

    // We now know that one value was read from either myIntsChan or myStringsChan
    // The chan that hadn't had a value read off of it will remain blocked
}

// This will be a function invoked as a goroutine to increment numbers
// c is the channel that we'll write the incremented value to
// NB: chans are concurrency-safe, so it safe (and idiomatic) to have many routines writing to a single chan from which values are subsequently read
func incrementInt(i int, c chan int) {
    // <- is the "send" operator that will send a value to our channel
    c <- i + 1
}

```

### Keep it Idiomatic!

In Go we:

* Always use channels to communicate amongst concurrent goroutines

* Remember to use mutexes to safely read and write datatypes that are not natively concurrent-safe (covered in Advanced Concurrency)

* Encourage creating many goroutines if your use case demands it -- since they are so lightweight, there's no reason to limit yourself to say 100 go routines. Create however many thousand you need and the Go runtime will manage the scheduling. The upper limit is most realistically defined by the RAM/CPU constraints of your system.

