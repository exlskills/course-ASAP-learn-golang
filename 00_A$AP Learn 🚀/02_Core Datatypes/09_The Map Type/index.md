### Key-Value Data in Go

Go's built-in `map` type is a performant, flexible, and idiomatic way of storing key-value data -- kinda like JSON objects or HashMaps in Java! This type will also expand automatically -- thanks to the Go runtime -- in order to fit your data.

```go
package main

import (
    "fmt"
)

// NB: Across these examples, we frequently use the built-in `len(something)` function,
//     that gives us the number of elements in our array, map, or slice

func main() {    
    // This is a map in Go, don't rely on it's order, because that's not guaranteed
    // NB: `map` will only allow one value per key (definition of a map)
    // NB: `map` capacity can be defined on creation via `make` (if used) and then it will be automatically
    //      scaled by the Go runtime during program execution. If you know the planned size of the map, that
    //      capacity hint help to improve performance if that's a key concern for you.
    colorsMap := map[string]string{
        "AliceBlue": "#F0F8FF",
        "AntiqueWhite": "#FAEBD7",
        "Aqua": "#00FFFF",
        "Aquamarine": "#7FFFD4",
        // NB: Go requires this trailing comma on the last element when initializing values over multiple lines
        "Azure": "#F0FFFF",
    }

    // Extract a value with the key
    fmt.Println("AliceBlue:", colorsMap["AliceBlue"])

    // Set a value for a key (if the key already exists, the value will be overwritten,
    // if the key doesn't yet exist, then Go will create it for us and scale the map if/as needed)
    colorsMap["Beige"] = "#F5F5DC"

    // If a key doesn't exist, go will return the default value (based on the value type), in this case
    // an empty string ("")
    fmt.Println("This key 'Bisque' doesn't exist: ", colorsMap["Bisque"])

    // Check whether an element exists
    if val, exists := colorsMap["Foobar"]; exists {
        // Since 'Foobar' isn't a key in our map, this will never execute
        fmt.Println("Foobar is a color? With the hex value:", val)
    }

    // Remove an element
    // NB: delete will actually remove the element from *your* map! (if the key doesn't exist, then this is a no-op)
    delete(colorsMap, "Azure")

    // NB: maps can be used in `range` for-loops, e.g., `for key, value := range myMap { ... }`,
    //     just remember that you can't count on the order of the elements!
    
    fmt.Println("colorsMap:", colorsMap)
}

```
