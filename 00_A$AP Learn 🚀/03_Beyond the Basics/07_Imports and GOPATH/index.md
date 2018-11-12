### How do we import libraries?

Go's approach to dependency management will feel somewhat unique to many developers, as Go's import structure and native dependency management is based on project version control URLs.

### Overview

* Go programmers typically keep all their Go code in a single workspace.
* A workspace contains many version control repositories (managed by Git, for example).
* Each repository contains one or more packages.
* Each package consists of one or more Go source files in a single directory.
* The path to a package's directory determines its import path.

Note that this differs from other programming environments in which every project has a separate workspace and workspaces are closely tied to version control repositories.

### Workspaces

A workspace is a directory hierarchy with two directories at its root:

* `src` contains Go source files, i.e., `$GOPATH/src`
* `bin` contains executable commands, i.e., `$GOPATH/bin`

The `go` tool builds and installs binaries to the bin directory.

The `src` subdirectory typically contains multiple version control repositories (such as for Git or Mercurial) that track the development of one or more source packages.

To give you an idea of how a workspace looks in practice, here's an example:

```bash
bin/
    hello                          # command executable
    outyet                         # command executable
src/
    github.com/golang/example/
        .git/                      # Git repository metadata
	hello/
	    hello.go                   # command source
	outyet/
	    main.go                    # command source
	    main_test.go               # test source
	stringutil/
	    reverse.go                 # package source
	    reverse_test.go            # test source
    golang.org/x/image/
        .git/                      # Git repository metadata
	bmp/
	    reader.go                  # package source
	    writer.go                  # package source
    
    ... (many more repositories and packages omitted) ...
```

The tree above shows a workspace containing two repositories (example and image). The example repository contains two commands (hello and outyet) and one library (stringutil). The image repository contains the bmp package and several others.

A typical workspace contains many source repositories containing many packages and commands. Most Go programmers keep all their Go source code and dependencies in a single workspace.

Note that symbolic links should not be used to link files or directories into your workspace.

As discussed in earlier sections, 'commands' (files that can be `go run` directly) and libraries are built from different kinds of source packages. (`package main` vs. a unique package name)

### The GOPATH environment variable

The `GOPATH` environment variable specifies the location of your workspace. It defaults to a directory named go inside your home directory, so `$HOME/go` on Unix, `$home/go` on Plan 9, and `%USERPROFILE%\go` (usually `C:\Users\YourName\go`) on Windows.

If you would like to work in a different location, you will need to set `GOPATH` to the path to that directory. (Another common setup is to set `GOPATH=$HOME`.) Note that `GOPATH` must not be the same path as your Go installation.

The command `go env GOPATH` prints the effective current `GOPATH`; it prints the default location if the environment variable is unset.

For convenience, add the workspace's bin subdirectory to your `PATH`:

`export PATH=$PATH:$(go env GOPATH)/bin`

The scripts in the rest of this document use `$GOPATH` instead of `$(go env GOPATH)` for brevity. To make the scripts run as written if you have not set `GOPATH`, you can substitute `$HOME/go` in those commands or else run:

`export GOPATH=$(go env GOPATH)`

To learn more about the `GOPATH` environment variable, see 'go help gopath'.

To use a custom workspace location, set the `GOPATH` environment variable.

### Import paths

An import path is a string that uniquely identifies a package. A package's import path corresponds to its location inside a workspace or in a remote repository (explained below).

The packages from the standard library are given short import paths such as `fmt` and `net/http`. For your own packages, you must choose a base path that is unlikely to collide with future additions to the standard library or other external libraries.

If you keep your code in a source repository somewhere, then you should use the root of that source repository as your base path. For instance, if you have a GitHub account at `github.com/user`, that should be your base path.

Note that you don't need to publish your code to a remote repository before you can build it. It's just a good habit to organize your code as if you will publish it someday. In practice you can choose any arbitrary path name, as long as it is unique to the standard library and greater Go ecosystem.

We'll use `github.com/user` as our base path. Create a directory inside your workspace in which to keep source code:

On a Unix system, this could be done by running: `mkdir -p $GOPATH/src/github.com/user` (`-p` will make sure that all required directories are created too)

### Building Executables

To compile and run a simple program, first choose a package path (we'll use `github.com/user/hello`) and create a corresponding package directory inside your workspace:

`mkdir $GOPATH/src/github.com/user/hello`

Next, create a file named `hello.go` inside that directory, containing the following Go code.

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, world.")
}
```

Now you can build and install that program with the go tool:

`go install github.com/user/hello`

Note that you can run this command from anywhere on your system. The go tool finds the source code by looking for the `github.com/user/hello` package inside the workspace specified by `GOPATH`.

You can also omit the package path if you run go install from the package directory:

```bash
cd $GOPATH/src/github.com/user/hello
go install
```

This command builds the `hello` command, producing an executable binary. It then installs that binary to the workspace's `bin` directory as hello (or, under Windows, hello.exe). In our example, that will be `$GOPATH/bin/hello`, which is `$HOME/go/bin/hello`.

The go tool will only print output when an error occurs, so if these commands produce no output they have executed successfully.

You can now run the program by typing its full path at the command line:

`$GOPATH/bin/hello`
Should return: `Hello, world.`

Or, as you have added `$GOPATH/bin` to your `PATH`, just type the binary name: `hello`

If you're using a source control system, now would be a good time to initialize a repository, add the files, and commit your first change. Again, this step is optional: you do not need to use source control to write Go code.

```bash
$ cd $GOPATH/src/github.com/user/hello
$ git init
Initialized empty Git repository in /home/user/work/src/github.com/user/hello/.git/
$ git add hello.go
$ git commit -m "initial commit"
[master (root-commit) 0b4507d] initial commit
 1 file changed, 1 insertion(+)
  create mode 100644 hello.go
Pushing the code to a remote repository is left as an exercise for the reader.
```

### Building Libraries

Let's write a library and use it from the hello program.

Again, the first step is to choose a package path (we'll use `github.com/user/stringutil`) and create the package directory:

`mkdir $GOPATH/src/github.com/user/stringutil`

Next, create a file named `reverse.go` in that directory with the following contents.

```go
// Package stringutil contains utility functions for working with strings.
package stringutil

// Reverse returns its argument string reversed rune-wise left to right.
func Reverse(s string) string {
	r := []rune(s)
	for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
		r[i], r[j] = r[j], r[i]
	}
	return string(r)
}
```

Now, test that the package compiles with `go build`:

`go build github.com/user/stringutil`

Or, if you are working in the package's source directory, just:

`go build`

Note: This won't produce an output file. Instead it saves the compiled package in the local build cache.

After confirming that the `stringutil` package builds, modify your original `hello.go` (which is in `$GOPATH/src/github.com/user/hello`) to use it:


```go
package main

import (
	"fmt"

	"github.com/user/stringutil"
)

func main() {
	fmt.Println(stringutil.Reverse("!oG ,olleH"))
}
```

Install the hello program:

`go install github.com/user/hello`

Running the new version of the program, you should see a new, reversed message:

`hello`

Will now print out:

`Hello, Go!`

After the steps above, your workspace should look like this:

```bash
bin/
    hello                 # command executable
src/
    github.com/user/
        hello/
            hello.go      # command source
        stringutil/
            reverse.go    # package source
```

### Package names

The first statement in a Go source file must be `package name` where name is the package's default name for imports. (All files in a package must use the same name.)

Go's convention is that the package name is the last element of the import path: the package imported as `crypto/rot13` should be named `rot13`.

Executable commands must always use `package main`.

There is no requirement that package names be unique across all packages linked into a single binary, only that the import paths (their full file names) be unique.

### Keep it Idiomatic!

In Go we:

* Always use the recommended `GOPATH` structure as described above! The Go tooling will make it really hard for you to use any other structure (unless you're using another dependency management tool, like `dep` which is officially supported and managed by Go)

* Generally use `dep` for managing dependencies for larger/production projects that require a more consistent environment (this is covered in the upcoming Dependency Management section)

### Attribution

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License.
