### Public, private, in Go?

Unlike C++ or Java, Go doesn't have access keywords, such as `public`, `private`, etc. However, we still have a way to manage access to fields, types, and functions -- capitalization!

### Which scopes are we managing access among?

Go allows us to manage access within a package and between packages. For instance, you can make a field only accessible within its own package, or you can allow any package to access it. These rules are also applied during runtime 'reflection' that many libraries you use will be performing, so watch out for the potential affects of your access management practices!

### Capitalization matters in Go

The first thing to

```go
package demo

// This will be available to packages other than the current one via import since it starts with a capital character
const DemoConst = 21 // Basically like `public final int DemoConst = 1` for those familiar with Java

// Since this const starts with a lower-case character, it'll only be visible within this package
const constForOurselves = 42;

// PublicStruct will be available to other packages that import this package since it starts with a capital character
type PublicStruct struct {
    // We still need to export public fields (this is important for things like JSON decoding/encoding)
    FieldWeWantPeopleToSee string

    // Even though the struct is exported, we can still have package-only fields
    // This field will not be decoded/encoded in JSON because the encoding/json package won't be able to see it
    fieldWeDontWantPeopleToSee string
}

// This is an unexported struct because it starts with a lower-case character
type demoStruct struct {
    // We can still have exported fields (this is important for things like JSON decoding/encoding)
    FieldWeWantPeopleToSee string
    // This field will not be decoded/encoded in JSON because the encoding/json package won't be able to see it
    fieldWeDontWantPeopleToSee string
}

```

### Keep it Idiomatic!

In Go we:

* Do our best to document exported types, constants, variables, and functions -- it's good practice

* Generally try to export as few fields as possible
