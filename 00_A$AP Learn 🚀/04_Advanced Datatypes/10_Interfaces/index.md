### Methods in Go

Now that we've covered structs and functions, we're ready to dig into 'methods' in Go. They allow us to 'link' functions to an instance of a struct -- similar to instance methods in Java. This is a really useful way to essentially 'scope' functions to reduce clutter of functions and create a more coherent project structure.

### Interfaces

This is best to learn by digging through a thorough example:

```go
package main

import (
    "fmt"
    "errors"
)

// This is a sample interface defintion
// NB: Go *only* allows you to specify methods (not fields) on an interface
type YMover interface {
    MoveY(int64) error
}

type XMover interface {
    // We can also add param/return value names (purely for documentation/clarity -- implementers don't have to use the same names)
    MoveX(amount int64) (err error)
}

type ZMover interface {
    MoveZ(amount int64) (err error)
}

// Interfaces can also be composed
type FlatMover interface {
    YMover
    XMover
}

type Mover interface {
    YMover
    XMover
    ZMover
}

// Since cars only move left/right and forward/backwards, it's going to be a FlatMover
// NB: You never have to explicitly 'implement' an interface. Go will figure that out for us
type Car struct {
    x, y int64
}

func (car *Car) MoveX(amt int64) (err error) {
    car.x += amt
    return
}

func (car *Car) MoveY(amt int64) (err error) {
    car.y += amt
    return
}

type Airplane struct {
    x, y, z int64
}

func (plane *Airplane) MoveX(amt int64) (err error) {
    plane.x += amt
    return
}

func (plane *Airplane) MoveY(amt int64) (err error) {
    plane.y += amt
    return
}

func (plane *Airplane) MoveZ(amt int64) (err error) {
    if plane.z + amt > 50000 {
        // Let's say that maybe your airplane can't go above 50,000
        return errors.New("error unable to move above 50,000")
    }
    if plane.z + amt < 0 {
        // Let's say that we don't want our plane to crash into the ground
        return errors.New("error unable to move below z=0")
    }
    // It's safe, so let's update our z
    plane.z += amt
    return
}

// NB: we never take pointers to interfaces -- that's up to the implementer. We can check if the value we got is a pointer, however, and throw an error if need-be
func PerformFlatMovement(flatMover FlatMover) error {
    fmt.Println("[FLAT/FULL] Move y by +42")
    if err := flatMover.MoveY(42); err != nil {
        return err
    }
    fmt.Println("[FLAT/FULL] Move x by -42")
    if err := flatMover.MoveX(-42); err != nil {
        return err
    }
    return nil
}

func PerformFullMovement(fullMover Mover) error {
    // Woah. Now this is cool. Since a Mover is a superset of FlatMover, we can do this!
    if err := PerformFlatMovement(fullMover); err != nil {
        return err
    }
    fmt.Println("[FULL] Move z by +21")
    if err := fullMover.MoveZ(21); err != nil {
        return err
    }
    return nil
}

func main() {
    // All of our int64's will default to 0
    myPlane := Airplane{}
    myCar := Car{}

    fmt.Println("myCar before movement:", myCar)
    // Remember that we need to be passing a pointer since our methods are pointer receivers
    PerformFlatMovement(&myCar)
    fmt.Println("myCar after movement:", myCar)

    fmt.Println("myPlane before movement:", myPlane)
    PerformFullMovement(&myPlane)
    fmt.Println("myPlane after movement:", myPlane)
}

```

### Keep it Idiomatic!

* Use interfaces whenever you can and it's reasonable to increase the usability of your library/APIs

* Adhere to Go's 'standard' interfaces (like the `io.Reader`, `io.Writer`, etc.)

* Accept Go's 'standard' interfaces whenever possible. For instance, if you need to share a reader API, why not use Go's standard `io.Reader` interface?
