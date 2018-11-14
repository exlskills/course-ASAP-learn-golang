### Let's disect a common confusion around functions...

### Pass by reference vs. pass by value explained

Whenever we pass a 'basic' type in Go, which is one of the following:

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```

The above types, in addition to arrays, are passed by **value**. This means that when the variable is passed to the function, Go actually sends a copy of the value to the function. This means that if you modify that argument in the function, the caller will not see that change. This is also true for all `struct`s that aren't passed as a pointer. When you are taking an `interface{}` in a function, the behavior is ultimately determined by the value that the caller passes, i.e., if they pass a pointer, then you can get the reference, however, if they pass an `int` then you just get the copy of the value.

**Before** we get into passing by reference, remember that if a caller passes a pointer to a value, and you change the **pointer itself** (like `ptrFromCaller := &foobarThing`) then the caller will **NOT** see your change! Because in reality, that actual pointer is a copy...

**Beware** slices, channels (`chan`s), and `map`s are passed by **reference**! However, if the function does something like `sliceFromCaller = []int{1, 2, 3}` where an entirely different reference is introduced, then the caller **will not** see the changes, this is because the underlying concept is the same as for passing pointers: your reference to a value is passed by value.

**Should I pass a pointer?** Obviously this is a somewhat nuanced answer, but here's a general guideline:

* If you want the function to be able to modify your variable directly, then pass a pointer. However, remember, if you're passing something that's already passed by reference, such as a slice, then passing a pointer is redundant and should generally be avoided since then you're passing a reference to a reference! In those cases, you can just pass the slice in directly, for instance, since that way the caller will still be modifying your 

* If you don't want the function to be able to modify your variable directly, then you can usually just pass the value in and Go will automatically send the function a copy! In the event that you want to, for instance, pass a slice without having the function modify your slice, then you would need to make a copy manually and pass that copy into the function.

Hopefully you're sufficiently bewildered now. Let's see this in practice:

```go
package main

import (
    "fmt"
)

type MyFoo struct {
    amount int
}

func main() {
    // Setup our demo variables
    mySliceOfNums := []int{0, 42, 21, -1}
    myStruct := MyFoo{amount: 42}
    
    fmt.Println("mySliceOfNums before call to changeMySlice:", mySliceOfNums)
    // Let the slice be changed!
    changeMySlice(mySliceOfNums)
    fmt.Println("mySliceOfNums after call to changeMySlice:", mySliceOfNums)
    // Can't change the reference to the slice I pass...
    youCantTouchThisSlice(mySliceOfNums)
    fmt.Println("mySliceOfNums after call to youCantTouchThisSlice:", mySliceOfNums)

    fmt.Println("myStruct before call to youCantChangeThisFoo:", myStruct)
    youCantChangeThisFoo(myStruct)
    fmt.Println("myStruct after call to youCantChangeThisFoo:", myStruct)
    // The & is used to pass a pointer to myStruct
    changeFooAmount(&myStruct)
    fmt.Println("myStruct after call to changeFooAmount:", myStruct)
}

func changeMySlice(numbers []int) {
    if (len(numbers) > 0) {
        numbers[0] = 42
    }
}

func youCantTouchThisSlice(numbers []int) {
    // I'm going to try to change the slice itself, but I can't because I'm actually just changing 
    // a reference to the slice -- as opposed to the slice itself -- which is passed by value
    numbers = []int{}
}

func youCantChangeThisFoo(foo MyFoo) {
    // So this will only change my copy of `foo` -- and the caller will not see any changes
    foo.amount = -1
}

func changeFooAmount(refToFoo *MyFoo) {
    // So, I can change a field in the refToFoo, because I'm modifying the actual value that my pointer points to
    // and the caller will subsequently see their `amount` as 21
    refToFoo.amount = 21
    // However, if I just change the pointer, nothing will happen and the caller will still see `amount` as 21...
    refToFoo = &MyFoo{amount:0}
}

```
