### Common Stdlib Packages

Go offers an incredible amount of functionality to developers directly via the stdlib -- which is really awesome! Doing everything from handling JSON, XML, HTML templates, and even entire webservers can be done with relative ease, exclusively with the standard lib.

### Some Popular Packages to Note

Note, the entire standard lib ships with your Go installation, hence, the name 'standard lib', which means that you can just import these in your code and start using them!

* `fmt`: this is likely the first Go library you ever saw, it is pronounced 'fumt', and contains utilities for I/O, such as printing to the console and formatting strings -- similar to the C-style `printf` and `scanf`

* `encoding/json`: the only JSON library you'll probably ever need in Go, this library offers a full-suite of JSON-related utilities that cover the entire gammut of JSON tasks

* `net/http`: the base package that offers your `http.Server` type and `http.ListenAndServe` function that form the basis of almost all Go HTTP servers (even if you choose to use a 3rd party router or middleware, etc.)

* `errors`: this package offers the `errors.New(string)` function that you will need whenever you wish to create your own error to return to a caller of a function

* `io/ioutil`: this package offers the core (and simplest) functions for file IO in Go, chiefly `ioutil.ReadFile` and `ioutil.WriteFile`

* `log`: this may or may not be your standard logging library, however, many Go developers choose to use a 3rd-party library (such as `logrus`)

* `math`: this package will fulfill most of your core mathematical requirements (such as trigonometry, logarithms, exponentiation, and rounding)

### Other Frequently-used Packages

* `os`: useful particularly if you find yourself looking for OS env vars via `os.Getenv` or you wish to have more granular file access APIs like `os.Open`

* `os/exec`: if you need to run a system command, like `mkdir -p /demo/foo`, in Go (which you might need to do with tools that don't have neat Go/TCP/HTTP APIs)

* `sort`: offers your core slice sorting functions that come pre-optimized for performance

* `strings` and `strconv`: more in-depth string parsing/manipulation tooling

* `sync`: most notably the package that contains `sync.WaitGroup` and `sync.Mutex` -- both of which become super useful in more complicate concurrency use cases

* `filepath`: lots of filesystem-specific utilities when working with files, such as picking correct filepath separators, joining paths, and calculating absolute paths

* `html/template`: a highly-performant and idiomatic Go templating library for rendering HTML templates (most commonly used for web servers)
