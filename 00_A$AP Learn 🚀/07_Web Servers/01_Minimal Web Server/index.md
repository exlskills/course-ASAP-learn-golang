### Tiny Web Server

In Go, the standard `net/http` library provides all of the core utilities for building both HTTP clients and servers. In this section, we'll write the absolute minimum amount of Go code that you need to listen on a TCP port, route HTTP requests, and respond to those requests.

### Example

```go
package main

import (
    "fmt"
    "net/http"
    "os"
)

type miniHTTPServer struct {}

// Fit the `http.Handler` interface by implementing the standard ServeHTTP method
func (s *miniHTTPServer) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    // Send a string back to the requester
    w.Write([]byte("Mini HTTP server in Go!"))
}

func main() {
    // The http.ListenAndServe function will listen on the port and route all HTTP requests to our http.Handler implementation: miniHTTPServer
    if err := http.ListenAndServe(":8080", miniHTTPServer{}); err != nil {
        fmt.Println("An error occurred listening on port 8080:", err)
        os.Exit(1) // Exit with an error code
    }
    fmt.Println("Exited gracefully") // This won't happen, because either the program will exit or there will be an error
}

```

Moving up from this example, we would usually setup a router (like Gorilla MUX) which provides a wrapper for us to use that then implements the `http.Handler` function.
