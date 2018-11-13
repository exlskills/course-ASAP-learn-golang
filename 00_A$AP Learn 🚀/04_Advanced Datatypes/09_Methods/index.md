### Methods in Go

Now that we've covered structs and functions, we're ready to dig into 'methods' in Go. They allow us to 'link' functions to an instance of a struct -- similar to instance methods in Java. This is a really useful way to essentially 'scope' functions to reduce clutter of functions and create a more coherent project structure.

### Methods

```go
package main

import (
    "fmt"
    "errors"
)

type TeslaCar struct {
    // You know... The doors that open up instead of out to the side ¯\_(ツ)_/¯ 
    VerticalDoors bool
    Model         string
    // Let's say that we want other packages to be able to modify the serial number...
    serialNo      *int64
}

// You can take a copy of the TeslaCar instance like this (without the pointer *), but usually we do take a pointer (as below in the SetSerialNo func)
// NB: The (car TeslaCar) part is called the receiver
func (car TeslaCar) IsCoolerThan(compareModel string) bool {
    // Nothing's cooler than the Roadster (yet)
    if car.Model == "Roadster" {
        return false
    }
    if car.Model == "S" {
        if compareModel == "Roadster" {
            return true
        } else {
            return false
        }
    }
    if compareModel != "X" {
        return true
    }
    return false
}

// Receive a pointer to the TeslaCar instance, so that when we modify it, the caller will see the change reflected!
// NB: When receiving a pointer, you don't have to check that the receiver is non-nil, as if it were nil, then the method would not have been invoked
//     because the caller would've incurred a nil-pointer panic
func (car *TeslaCar) SetSerialNo(no int64) error {
    // If the car already has a serial no, then we shouldn't be setting it!
    if car.serialNo != nil {
        return errors.New("error serial number already assigned")
    }
    // Check if it's valid (based on our made up rules!), then we can set it
    if no % 42 != 0 {
        return errors.New("error invalid serial number, must be divisible by 42")
    }
    car.serialNo = &no
    return nil
}

// It's typical to accept a pointer even if we don't intend to modify the receiver
func (car *TeslaCar) GetSerialNo() (int64, error) {
    if car.serialNo == nil {
        // Return a sentinel value for the serial number and an error
        return -1, errors.New("error serial number not set")
    }
    // Return the value of the serial number and no error
    return *car.serialNo, nil
}

func main() {
    modelX := TeslaCar{
        VerticalDoors: true,
        Model: "X",
    }
    err := modelX.SetSerialNo(84)
    if err != nil {
        fmt.Println("Ooops! An error occurred setting the serial number:", err)
        return
    }
    serNo, err := modelX.GetSerialNo()
    if err != nil {
        fmt.Println("Ooops! An error occurred getting the serial number:", err)
        return
    }
    fmt.Println("Serial number:", serNo)
}

```

### Keep it Idiomatic!

* Use methods whenever you have functions that are specific/unique to a particular struct

* Remember that methods play nice with embedding (they essentially look like they're part of the embedding struct)

* It's fine to generally accept pointer receivers

* When putting together a struct with methods to adhere to an interface, remember that if you define your methods as pointer receivers, then only the pointer to your struct will adhere to the interface. The converse is also true.
