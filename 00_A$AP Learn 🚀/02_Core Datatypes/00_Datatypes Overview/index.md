### The Essential Datatypes

Go is a statically-typed language with some 'basic' datatypes, arrays and slices, pointers, and maps. We also have structs and interfaces, however, those are covered in their respective sections, "Structs" and "Interfaces"

#### ⚠️ BEWARE Potential Confusion Ahead! ⚠️ 

* ⚠️ Many new Go developers struggle discerning arrays from slices because the syntax and apparent use case seems so similar, so the best advice for new Go developers is just don't use arrays! Call everything a slice, and always use slice notation when you need an 'array-like' thing. This will give you a consistent behavior and later when you're ready to explore more Go, you can consider learning more about arrays

* ⭐ For more complex data structures (or if you're looking for something similar to a 'class' in Java/C++), then head over to the dedicated "Structs" section. Structs are not discussed here

* ⭐ For learning about interfaces, check out the "Interfaces" section as they are not discussed here

* ⭐ For learning about `chan` (channels), check out the "Channels" section as they are not discussed here

* ⭐ For learning about the `error` type, check out "The Error Type" section as it's not discussed here

* ⚠️ Go doesn't offer generics. The most 'generic-like' option would be `interface{}` which is an empty interface, and since everything (`nil` included) satisfies the empty interface type, then this becomes a catch-all for 'send me anything.' This is described in further detail in the "Interfaces" section

* Go types `int`, `uint`, `float`, `complex64`, and `complex128` have numeric suffixes that specify how many bits they fit. For instance, `uint8` is an 8-bit unsigned integer, while `complex128` stores 128-bits (two `float64`s worth of data).

* Go types `int`, `uint`, and `float` offer 'default' versions which are runtime-specific. On 32-bit systems, these types are all equivalent to their 32-bit versions, while on 64-bit systems they are all equivalent to their 64-bit versions. Many Go developers (and a lot of the standard library) uses the 64-bit versions explicitly for consistency and predictability, i.e., `int64`. **However,** remember that lengths, indices, and capacities are always of the `int` type, so keep that in mind!

