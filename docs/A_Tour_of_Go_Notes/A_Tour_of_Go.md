# A Tour of Go

## 1. Basics

### 1.1. Packages, variables, and functions

#### 1.1.1. Packages and imports

- The name of current package is determined with a `package name` statement in the front of a code file;

- Imports are executed with `import name_string` statement:
  
  ```go
  package main
  import (
   "fmt"
   "math"
   "github.com/go-yaml/yaml/v2" // define version
   mongo "mywebapp/libs/mongodb/db" // alias
   . "something/big" // 'big' namespace is used
   - "lib/may/not/be/used"
  )
  ```

- Only items starting with capital letter can be exported:
  
  ```go
  fmt.Println(math.Pi)
  fmt.Println(math.pi) // Invalid
  ```

#### 1.1.2. Functions

- Declaration:
  
  ```go
  func add(x int, y int) int {
      return x + y
  }
  ```

- Shorten parameter list:
  
  ```go
  func add(x, y int) int { // For parameters in same type
   return x + y
  }
  ```

- Mutiple results and named return values:
  
  ```go
  func swap(sum int) (x, y int) { // `x int, y int`
      x = sum * 4 / 9
      y = sum - x
      return
  }
  ```

#### 1.1.3. Variables

- Variable declaration
  
  ```go
  package main
  
  var i, j int = 1, 2
  
  func main() {
      var c, python, java bool // Initialized as false
      n := 0;
  }
  ```

- Go's basic types are
  
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

- Uninitialized variables are automatically set to thier zero values
  
  - `0` for numeric types,
  
  - `false` for the boolean type, and
  
  - `""` (the empty string) for strings.

- Unlike in C, in Go assignment between items of different type requires an explicit conversion.
  
  ```go
  // T(v)
  var i int = 42
  var f float64
  f = float64(i) // where T~float64 and v~i.
  ```

- `:=` statement can inferer the type of variable:
  
  ```go
  i := 42           // int
  j := i            // int
  f := 3.142        // float64
  g := 0.867 + 0.5i // complex128
  ```

#### 1.1.4. Constants

- Declared like variables, but with the `const` keyword.

- Can be character, string, boolean, or numeric values.

- Cannot be declared using the `:=` syntax.

- Numeric constants are high-precision *values*.
  
  ```go
  const (
      Big = 1 << 100
      Small = Big >> 99
  )
  Small == 2 // true
  ```

### 1.2. Flow control statements: for, if , else, switch and defer

#### 1.2.1. For

- Basic usage:
  
  ```go
  sum := 0
  for i := 0; i < 10; i++ {
      sum += i
  }
  ```

- Every sub-statement in `for` can be omitted
  
  ```go
  sum := 0
  i := 0
  for ;i < 10; {
   sum += i
   i++
  }
  ```

- Writen as if it's a `while` in C:
  
  ```go
  sum := 1
  for sum < 1000 {
   sum += sum
  }
  ```

- Forever:
  
  ```go
  for {
      // ...
  }
  ```

#### 1.2.2. If

- The basic usage of `if` statement
  
  ```go
  if x < 0 {
      // ...
  }
  ```

- if with a short statement and else
  
  ```go
  if v := math.Pow(x, n); v < lim {
  
      return v
  
  } else {
      // ...
  }
  ```

- Practice:
  
  ```go
  func Sqrt(x float64) float64 {
      z := float64(x/2)
      for (z * z - x) > 1e-10 || (z * z - x) < -1e-10 {
          z -= (z*z - x) / (2*z)
      }
      return z
  }
  ```

#### 1.2.3. Switch

- Go's switch cases need not be constants, and the values involved need not be integers.
  
  ```go
  switch os := runtime.GOOS; os {
  case "darwin":
      fmt.Println("OS X.")
  case "linux":
      fmt.Println("Linux.")
  default:
      // freebsd, openbsd,
      // plan9, windows...
      fmt.Printf("%s.\n", os)
  }
  ```

- Switch without a condition is the same as `switch true`.
  
  ```go
  t := time.Now()
  switch {
  case t.Hour() < 12:
      fmt.Println("Good morning!")
  case t.Hour() < 17:
      fmt.Println("Good afternoon.")
  default:
      fmt.Println("Good evening.")
  }
  ```

- Switch with short assignment
  
  ```go
  switch t := time.Now(); {
  
  case t.Hour() < 12:
      fmt.Println("Good morning!")
  case t.Hour() < 17:
      fmt.Println("Good afternoon.")
  default:
      fmt.Println("Good evening.")
  }
  ```

#### 1.2.4. Defer

A defer statement defers the execution of a function until the ***surrounding function returns***.

The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in ***last-in-first-out order***.

```go
package main

import "fmt"

func main() {
    fmt.Println("counting")

    for i := 0; i < 10; i++ {
        defer fmt.Println(i)
    }

    fmt.Println("done")
}
```

### 1.3. More types: structs, slices, and maps.

#### 1.3.1. Pointers

- `*T` is a pointer to a `T` value whose zero value is nil.
  
  ```go
  var p *int // p == nil
  i := 42
  p = &i
  ```

- The `*` operator denotes the pointer's underlying value (dereferencing)
  
  ```go
  *p = 21 // i == 21
  ```

- Automatic dereferencing
  
  ```go
  type struct Vertex { X, Y int }
  
  var v Vertex = Vertex{1, 2}
  p := &v
  p.X        // 1
  ```

#### 1.3.2. Structs

- A `struct` is a collection of fields
  
  ```go
  type Vertex struct {
      X int
      Y int
  }
  ```

- Literals
  
  ```go
  type Vertex struct {
      X, Y int
  }
  
  var (
      v1 = Vertex{1, 2} // {1 2}
      v2 = Vertex{X: 1} // {1 0}
      v3 = Vertex{}     // {0 0}
      p = &Vertex{1, 2} // {1 2}
  )
  ```

#### 1.3.3. Arrays & slices

- The type `[n]T` is an array of `n` values of type `T`.
  
  ```go
  var a [10]int    // an array of 10 integers.
  arr := [6]int{1, 2, 3, 4, 6, 5}
  ```

- The type `[]T` is a slice of elements of type `T`.
  
  ```go
  var b []int      // a slice with integers.
  a[1: 4]          // a slice which inclides elements 1 through 3 of a
  ```

- As `Vector` in C++, type `slice` does not store any data but includes a section of an underlying array.

- Literals:
  
  ```go
  [3]bool{false, true, false}
  [ ]bool{false, true, false}
  ```

- Slicing
  
  ```go
  var a [10]int
  a[0: 10]
  a[: 10]
  a[0: ]
  a[:]
  ```

- *length* and *capacity*
  
  ```go
  s := []int{2, 3, 5, 7, 11, 13}
  fmt.Println(len(s))
  fmt.Println(cap(s))
  ```

- Zero value of slice is `nil` which hase a length and capacity of 0 and has no underlying array. 

- `make` function
  
  ```go
  a := make([]int, 5)
  
  b := make([]int, 0, 5) // len(b)=0, cap(b)=5
  b = b[:cap(b)] // len(b)=5, cap(b)=5
  
  b = b[1:]      // len(b)=4, cap(b)=4
  ```

- Slices can contain any type, including other slices.
  
  ```go
  board := [][]string{
      []string{"_", "_", "_"},
      []string{"_", "_", "_"},
      []string{"_", "_", "_"},
  }
  ```

- `append`
  
  ```go
  // func append(s []T, vs ...T) []T
  s := []int{}            // len=0, cap=0
  s = append(s, 0)        // len=1, cap=2
  s = append(s, 1)        // len=2, cap=2
  s = append(s, 2, 3, 4)  // len=5, cap=8
  ```

- `range` form of the `for` loop iterates over a slice or map.
  
  ```go
  var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}
  
  for i, v := range pow {
      fmt.Printf("2**%d = %d\n", i, v)
  }
  ```

- Skiping the index or value by assigning to _:
  
  ```go
  for i, _ := range pow
  for _, value := range pow
  for i := range pow
  ```

#### 1.3.4. Maps

- A map maps keys to values.

- The zero value of a map is `nil`. A `nil` map has no keys, nor can keys be added.

- The `make` function returns a map of the given type, initialized and ready for use.
  
  ```go
  type Vertex struct {Lat, Long float64}
  
  var m map[string]Vertex
  
  m = make(map[string]Vertex)
  m["Bell Labs"] = Vertex{
      40.68433, -74.39967,
  }
  // A hypothesis: The `var` declaration only allocates `m`'s position on Stack while the `make` function indeed allocates bits on Heap. Why not tell it out in the Tour? Silly.
  ```

- Map literals are like struct literals, but the keys are required.
  
  ```go
  var m = map[string]Vertex{
      "Bell Labs": Vertex{
          40.68433, -74.39967,
      },
      "Google": Vertex{
          37.42202, -122.08408,
      },
  }
  
  // ...Or if the top-level type is just a type name, you can omit it from the elements of the literal.
  var m = map[string]Vertex{
      "Bell Labs": {40.68433, -74.39967},
      "Google": {37.42202, -122.08408},
  }
  ```

- Map usage
  
  ```go
  // Insert or update an element in map m:
  m[key] = elem
  
  // Retrieve an element:
  elem = m[key]
  
  // Delete an element:
  delete(m, key)
  
  // Test a key is present with a two-value assignment:
  elem, ok = m[key]    // ok == true or ok == false
  ```

#### 1.3.5. Functions

- Functions are values which can be used as arguments or return values.
  
  ```go
  func compute(fn func(float64, float64) float64) float64 {
      return fn(3, 4)
  }
  ```

- Go functions may be closures.
  
  ```go
  func adder() func(int) int {
      sum := 0
      return func(x int) int {    // variable sum is bound
  
          sum += x
          return sum
      }
  }
  ```

- Exercise:
  
  ```go
  package main
  
  import "fmt"
  
  // fibonacci is a function that returns
  // a function that returns an int.
  func fibonacci() func() int {
      i, j := 0, 1
      return func() int {
          i, j = j, i + j
          return i
      }
  }
  
  func main() {
      f := fibonacci()
      for i := 0; i < 10; i++ {
          fmt.Println(f())
      }
  }
  ```

## 2. Methods and interfaces

### 2.1. Methods and interfaces

#### 2.1.1. Methods

- No classes in Go, alternatively there are types with methods who have special *receiver* arguments
  
  ```go
  package main
  
  import (
      "fmt"
      "math"
  )
  
  type Vertex struct {
      X, Y float64
  }
  
  func (v Vertex) Abs() float64 {
      return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }
  
  func main() {
      v := Vertex{3, 4}
      fmt.Println(v.Abs())
      fmt.Println(Abs(v))
  }
  ```

- Can only declare a method with a receiver whose type is defined in the same package as the method.

- Methods with pointer receivers:
  
  - able to modify the value;
  
  - avoid copying the value on each method call.
  
  ```go
  package main
  
  import (
      "fmt"
      "math"
  )
  
  type Vertex struct {
      X, Y float64
  }
  
  func (v Vertex) Abs() float64 {
      return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }
  
  func (v *Vertex) Scale(f float64) {
      v.X = v.X * f
      v.Y = v.Y * f
  }
  
  func main() {
      v := Vertex{3, 4}
      v.Scale(10)        // Automatically  turns to (&v).Scale(10)
  }
  ```

#### 2.1.2. Interfaces

- A set of method signatures. A value of interface type can hold any value that implements those methods.

- Implicity
  
  ```go
  package main
  
  import (
      "fmt"
      "math"
  )
  
  type I interface {
      M()
  }
  
  type T struct {
      S string
  }
  
  func (t *T) M() {
      fmt.Println(t.S)
  }
  
  type F float64
  
  func (f F) M() {
      fmt.Println(f)
  }
  
  func main() {
      var i I
  
      i = &T{"Hello"}
      describe(i)
      i.M()
  
      i = F(math.Pi)
      describe(i)
      i.M()
  }
  
  func describe(i I) {
      fmt.Printf("(%v, %T)\n", i, i)
  }
  ```

- Interfaces can be treated as a tuple like `(type, value)`, where both of them are `nil` in an empty interface. Note, an empty interface is not `nil`.

- Type assertion
  
  ```go
  // t, ok := i.(T)
  ```

- Type switch
  
  ```go
  switch v := i.(type) {
  case T:
      // here v has type T
  case S:
      // here v has type S
  default:
      // no match; here v has the same type as i
  }
  ```

- `IPAddr` exercise
  
  ```go
  package main
  
  import "fmt"
  
  type IPAddr [4]byte
  
  // In fmt package
  // type Stringer interface {
  //     String() string
  // }
  // TODO: Add a "String() string" method to IPAddr.
  
  func (ip IPAddr) String() string {
      return fmt.Sprintf("%v.%v.%v.%v", ip[0], ip[1], ip[2], ip[3])
  }
  
  func main() {
      hosts := map[string]IPAddr{
          "loopback":  {127, 0, 0, 1},
          "googleDNS": {8, 8, 8, 8},
      }
      for name, ip := range hosts {
          fmt.Printf("%v: %v\n", name, ip)
      }
  }
  ```

- The error type is a built-in interface similar to `fmt.Stringer`:
  
  ```go
  type error interface {

      Error() string
  }
  ```

- TODO: finish all exercises.

## 3. Concurrency

### 3.1. Concurrency

#### 3.1.1 Goroutines

- A *goroutine* is a lightweight thread managed by the Go runtime.
  
  ```go
  go f(x, y, z)
  ```

- Goroutines run in the same address space, so access to shared memory must be synchronized.

#### 3.1.2. Channels

- Channels are a typed conduit through which you can send and receive values with the channel operator, `<-`.
  
  ```go
  ch := make(chan int) // Create a channel
  ch <- v    // Send v to channel ch.
  v := <-ch  // Receive from ch, and
             // assign value to v.
  ```

- *Buffered* channel
  
  ```go
  ch := make(chan int, 100)
  ```

- Channel closing
  
  ```go
  package main
  
  import (
      "fmt"
  )
  
  func fibonacci(n int, c chan int) {
      x, y := 0, 1
      for i := 0; i < n; i++ {
          c <- x
          x, y = y, x+y
      }
      close(c)    
      // A sender can `close` a channel to indicate that no more values will be sent.
      // Receivers can test whether a channel has been closed by assigning a second parameter to the receive expression: after

  }
  
  func main() {
      c := make(chan int, 10)
      go fibonacci(cap(c), c)
      for i := range c {    // The loop for i := range c receives values from the channel repeatedly until it is closed.

          fmt.Println(i)
      }
  }
  ```

- `select` statement
  ```go
  func fibonacci(c, quit chan int) {
      x, y := 0, 1
      for {
          // A select blocks until one of its cases can run, then it executes that case. It chooses one at random if multiple are ready.
          select {
          case c <- x:
              x, y = y, x+y
          case <-quit:
              fmt.Println("quit")
              return
          // default:
          //     fmt.Println("No block")
          }
      }
  }
  ```
#### `Mutex`
We've seen how channels are great for communication among goroutines.

But what if we don't need communication? What if we just want to make sure only one goroutine can access a variable at a time to avoid conflicts?

This concept is called mutual exclusion, and the conventional name for the data structure that provides it is mutex.

Go's standard library provides mutual exclusion with `sync.Mutex` and its two methods:

- `Lock`
- `Unlock`

We can define a block of code to be executed in mutual exclusion by surrounding it with a call to `Lock` and `Unlock` as shown on the `Inc` method.

We can also use `defer` to ensure the mutex will be unlocked as in the `Value` method.
```go
package main

import (
    "fmt"
    "sync"
    "time"
)

// SafeCounter is safe to use concurrently.
type SafeCounter struct {
    v   map[string]int
    mux sync.Mutex
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
    c.mux.Lock()
    // Lock so only one goroutine at a time can access the map c.v.
    c.v[key]++
    c.mux.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
    c.mux.Lock()
    // Lock so only one goroutine at a time can access the map c.v.
    defer c.mux.Unlock()
    return c.v[key]
}

func main() {
    c := SafeCounter{v: make(map[string]int)}
    for i := 0; i < 1000; i++ {
        go c.Inc("somekey")
    }

    time.Sleep(time.Second)
    fmt.Println(c.Value("somekey"))
}
```
  
  
  
  
  










