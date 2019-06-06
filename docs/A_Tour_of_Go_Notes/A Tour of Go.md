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



## 2. Methods and interfaces

### 2.1. Methods and interfaces

## 3. Concurrency

### 3.1. Concurrency


