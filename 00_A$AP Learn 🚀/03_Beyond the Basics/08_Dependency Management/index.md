### Managing Dependencies

For the better part of Go's existence, dependency management was a hotly-contested topic, however, these days there are two very well supported ways to manage your dependencies.

### The simplest option

If you are not _too_ concerned about exact versions or are just installing a tool, the easiest way to fetch a dependency remains `go get github.com/user/package-that-you-want`. This will install the library/tool directly into your `GOPATH`, make it importable from any of your Go projects, and it'll also be usable as an executable if you put your `$GOPATH/bin` into your system's `PATH` as well.

### For Larger Projects

When it comes to managing a consistent set of dependencies for a larger project, it is now recommended to use a tool called [dep](https://github.com/golang/dep). This is the only dependency management tool that is directly supported and maintained by the Go project itself, and is becoming the defacto standard for Go dependency management. Once you have installed `dep` by following the relevant instructions [here](https://github.com/golang/dep#installation) you'll be able to use it for managing dependencies in your project.

The `dep` tool relies on Go's 'vendor' directory that it is a way for managing project-specific copies of dependencies (similar to Node's `node_modules` that are managed by NPM). When compiling, Go will first check this 'vendor' directory that is structured similarly to your GOPATH, except for it basically starts with the 'src' part as its root.

NOTE: The primary issue with using the `vendor`/`dep` solution for package management is that, especially when writing libraries, it will cause issues for downstream implementers who may have a different copy of those dependencies. This will cause major issues -- unless you always use good interfaces (instead of structs) that Go will be able to actually check for fit at compile time!

### Using `dep`

For the most up-to-date instructions on using `dep`, it's best to check out their official documentation [here](https://golang.github.io/dep/docs/introduction.html).

### Keep it Idiomatic!

In Go we:

* Ship new projects using `dep` to give the best experience possible to collaborators and implementers

* When developing libraries, stick to minimal interfaces so that an implementer with a different set of vendored dependencies, will still be able to pass their data in and out of your library!
