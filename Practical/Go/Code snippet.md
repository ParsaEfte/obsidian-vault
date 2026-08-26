# 1. How use Variables?

---

## 🧾 **Go Variable Declaration & Usage – Cheat Sheet**

### 🔹 1. **Short Variable Declaration (`:=`)**

- Used **inside functions** only.
- Type is **inferred** from the value.

```go
name := "Alice"       // string
age := 30             // int
price := 19.99        // float64

```

---

### 🔹 2. **Explicit Declaration with `var`**

- Can be used **anywhere** (inside or outside functions).
- Type is **explicitly specified**.

```go
var city string = "Tokyo"
var count int = 42
var active bool = true

```

---

### 🔹 3. **Declaration Without Initialization**

- Zero value is assigned automatically.

```go
var email string   // "" (empty string)
var score int      // 0
var ready bool     // false
var data []int     // nil

```

---

### 🔹 4. **Multiple Variable Declaration (Same Type)**

```go
var x, y, z int = 1, 2, 3

```

---

### 🔹 5. **Multiple Variable Declaration (Different Types)**

```go
var (
    a int = 10
    b string = "Go"
    c float64 = 3.14
)

```

---

### 🔹 6. **Short Multiple Declaration with `:=`**

```go
x, y, name := 1, 2, "Bob"

```

---

### 🔹 7. **Anonymous (Unused) Variable with `_`**

- Used to **ignore a value** (often with multiple return values).

```go
_, err := someFunc()

```

---

### 🔹 8. **Constant Declaration (`const`)**

- Value must be known at compile time.

```go
const Pi = 3.14
const AppName string = "MyApp"

```

---

### 🔹 9. **Global Variable**

- Declared outside any function.

```go
var version = "1.0.0"

```

---

### 🔹 10. **Zero Value Table**

|Type|Zero Value|
|---|---|
|`int`|`0`|
|`float64`|`0.0`|
|`bool`|`false`|
|`string`|`""`|
|`pointer`|`nil`|
|`slice`|`nil`|
|`map`|`nil`|
|`func`|`nil`|
|`chan`|`nil`|
|`interface{}`|`nil`|

---

### ✅ Example Putting It All Together

```go
package main

import "fmt"

var globalVar = "I am global"

func main() {
    const pi = 3.14               // constant
    var age int = 25              // explicit declaration
    name := "John"                // short declaration
    var score float64             // zero value (0.0)

    x, y := 10, 20                // multiple short declaration
    var a, b int = 1, 2           // multiple var with types

    fmt.Println(name, age, pi, score, x, y, a, b, globalVar)
}

```

---

---

# 2.Datatype:

---

## 🧩 **Golang Data Types Classification**

Go data types can be classified into **four major categories**:

---

### 🟦 1. **Basic (Primitive) Types**

These are built-in types for common values.

|Type|Description|Examples|
|---|---|---|
|`bool`|Boolean|`true`, `false`|
|`string`|UTF-8 encoded text|`"hello"`|
|`int`, `int8`, `int16`, `int32`, `int64`|Signed integers|`int = 10`|
|`uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr`|Unsigned integers|`uint16 = 42`|
|`byte`|Alias for `uint8`|`byte = 'A'`|
|`rune`|Alias for `int32`, Unicode|`rune = '好'`|
|`float32`, `float64`|Floating points|`float64 = 3.14`|
|`complex64`, `complex128`|Complex numbers|`complex128 = 2 + 3i`|

---

### 🟨 2. **Composite Types**

These types are made by combining other types.

|Type|Description|Example|
|---|---|---|
|`array`|Fixed-size collection|`[3]int{1, 2, 3}`|
|`slice`|Dynamic array|`[]int{4, 5, 6}`|
|`struct`|Group of named fields|`type Person struct { Name string }`|
|`map`|Key-value store|`map[string]int{"a": 1}`|

---

### 🟩 3. **Reference Types**

Point to or refer to data.

|Type|Description|Example|
|---|---|---|
|`pointer`|Address of a variable|`var p *int = &x`|
|`function`|Functions as values|`func add(x, y int) int`|
|`channel`|Communication between goroutines|`make(chan int)`|
|`interface`|Abstract behavior contracts|`type Reader interface { Read() }`|

---

### 🟧 4. **Special & Generic Types**

Useful in special cases or with generics (Go 1.18+).

|Type|Description|Example|
|---|---|---|
|`nil`|Represents zero value for reference types|`var p *int = nil`|
|`any`|Alias for `interface{}` (Go 1.18+)|`func Print(v any)`|
|`comparable`|Constraint for comparable types|`map[K comparable]V`|
|Custom Types|User-defined types & aliases|`type Age int`, `type JSON = map[string]interface{}`|

---

### ✅ Visual Summary

```
┌────────────────────────────┐
│       Golang Types         │
├─────────────┬──────────────┤
│ Basic       │ Composite    │
│ - int       │ - array      │
│ - string    │ - slice      │
│ - float64   │ - struct     │
│ - bool      │ - map        │
├─────────────┼──────────────┤
│ Reference   │ Special      │
│ - pointer   │ - nil        │
│ - function  │ - any        │
│ - channel   │ - comparable │
│ - interface │ - custom     │
└─────────────┴──────────────┘

```

---

## 📘 Golang Data Types Cheat Sheet

### 🔹 1. **Boolean**

```go
var isActive bool = true

```

### 🔹 2. **Numeric Types**

### Integers

```go
var a int        // Platform dependent (32 or 64 bit)
var b int8       // -128 to 127
var c int16      // -32,768 to 32,767
var d int32      // -2,147,483,648 to 2,147,483,647
var e int64      // ±9.22e+18

```

### Unsigned Integers

```go
var ua uint      // unsigned int
var ub uint8     // 0 to 255
var uc uint16    // 0 to 65,535
var ud uint32    // 0 to 4,294,967,295
var ue uint64    // 0 to 1.84e+19
var up uintptr   // pointer-sized unsigned int

```

### Floating Point

```go
var f1 float32   // ~6 decimal digits
var f2 float64   // ~15 decimal digits

```

### Complex Numbers

```go
var c1 complex64  // real + imaginary with float32 parts
var c2 complex128 // real + imaginary with float64 parts

```

### 🔹 3. **Text Types**

```go
var name string = "Gopher"
var ch rune = 'G'   // alias for int32, supports Unicode
var b byte = 'A'    // alias for uint8

```

### 🔹 4. **Derived Types**

### Array

```go
var arr [3]int = [3]int{1, 2, 3}

```

### Slice

```go
var nums []int = []int{1, 2, 3, 4}

```

### Map

```go
var dict map[string]int = map[string]int{"a": 1, "b": 2}

```

### Struct

```go
type Person struct {
    Name string
    Age  int
}

```

### Pointer

```go
var p *int

```

### Function

```go
func add(a int, b int) int {
    return a + b
}

```

### Interface

```go
type Shape interface {
    Area() float64
}

```

### Channel

```go
var chn chan int

```

### Slice of Structs (example)

```go
var people []Person = []Person{
    {"Alice", 30},
    {"Bob", 25},
}

```

---

## 2.1 Rune

---

## 🧾 Golang `rune` Type Cheat Sheet

### 🔹 What is a `rune`?

- A **`rune` is an alias for `int32`**.
- It represents a **Unicode code point**.
- Each `rune` can store **one Unicode character**, including emojis and non-Latin scripts.

```go
type rune = int32
```

---

### 🔹 Why Use `rune`?

- To **work with characters** (not bytes).
- To handle **Unicode text** properly (e.g., Chinese, Arabic, emojis).
- Useful in **string iteration**, where Go strings are UTF-8 encoded.

---

### 🔹 Declaring a `rune`

```go
var r rune = 'A'         // character literal (single quotes)
fmt.Println(r)           // 65 (ASCII value of 'A')

emoji := '😀'            // Unicode emoji
fmt.Println(emoji)       // 128512 (Unicode code point)

```

---

### 🔹 `rune` vs `byte`

|Aspect|`rune`|`byte`|
|---|---|---|
|Alias for|`int32`|`uint8`|
|Size|4 bytes|1 byte|
|Use case|Unicode char|Raw byte|
|Literals|`'a'`, `'你'`|`'a'`|

---

### 🔹 Iterating Over a String as `rune`s

```go
s := "Go语言" // "Go Language" in Chinese

for i, r := range s {
    fmt.Printf("Index: %d, Rune: %c, Unicode: %U\n", i, r, r)
}

```

**Output:**

```
Index: 0, Rune: G, Unicode: U+0047
Index: 1, Rune: o, Unicode: U+006F
Index: 2, Rune: 语, Unicode: U+8BED
Index: 5, Rune: 言, Unicode: U+8A00

```

> 🧠 Note: Chinese characters take more than 1 byte, so indexes skip bytes.

---

### 🔹 Rune Literal Examples

```go
r1 := 'A'        // ASCII letter
r2 := '你'       // Chinese character
r3 := '🎉'       // Emoji
fmt.Println(r1, r2, r3) // 65 20320 127881

```

---

### 🔹 Convert Between `rune`, `int32`, and `string`

```go
var r rune = 'é'
fmt.Println(string(r))   // "é"
fmt.Println(int32(r))    // 233

```

---

### 🔹 Convert String to Rune Slice

```go
s := "🙂🎉"
runes := []rune(s)

for _, r := range runes {
    fmt.Printf("Rune: %c Unicode: %U\n", r, r)
}

```

---

### 🔹 Creating Strings from `rune` Slices

```go
runes := []rune{'你', '好'}
greeting := string(runes)
fmt.Println(greeting) // 你好

```

---

### ✅ Summary

|Operation|Example|
|---|---|
|Declare a rune|`var r rune = 'A'`|
|Get code point|`int32('你')` → `20320`|
|Iterate string runes|`for _, r := range str`|
|Convert to string|`string(rune)`|
|String to runes|`[]rune("文字")`|

---

## 2.2 Type of const

1. can write single:

```go
const pi float64 = 3.14
```

1. can write multi:

```go
const (
		name = "parsa"
		age = 23
		)
```

---

# 3. Built-In function

---

## 🧾 Go `strings` Package Cheat Sheet

📦 **Import First**

```go
import "strings"
```

---

### 🔹 1. **Contains Functions**

|Function|Description|Example|
|---|---|---|
|`strings.Contains(s, substr)`|Checks if `substr` in `s`|`strings.Contains("hello", "he") → true`|
|`strings.ContainsAny(s, chars)`|Any char in `chars` in `s`|`strings.ContainsAny("team", "i") → true`|
|`strings.ContainsRune(s, r)`|Checks if rune exists in `s`|`strings.ContainsRune("go", 'g') → true`|

---

### 🔹 2. **Comparison & Equality**

|Function|Description|Example|
|---|---|---|
|`strings.EqualFold(s1, s2)`|Case-insensitive comparison|`EqualFold("Go", "go") → true`|
|`strings.Compare(a, b)`|Lexical comparison (-1, 0, 1)|`Compare("a", "b") → -1`|
|`strings.HasPrefix(s, prefix)`|Check if string starts with prefix|`HasPrefix("hello", "he") → true`|
|`strings.HasSuffix(s, suffix)`|Check if string ends with suffix|`HasSuffix("hello", "lo") → true`|

---

### 🔹 3. **Indexing and Searching**

|Function|Description|Example|
|---|---|---|
|`strings.Index(s, substr)`|First index of `substr`, -1 if not found|`Index("choco", "co") → 3`|
|`strings.LastIndex(s, substr)`|Last index of `substr`|`LastIndex("gogogo", "go") → 4`|
|`strings.IndexAny(s, chars)`|First index of any `char` from `chars`|`IndexAny("team", "aeiou") → 1`|
|`strings.IndexRune(s, r)`|Index of rune `r`|`IndexRune("golang", 'g') → 0`|

---

### 🔹 4. **Modifying Strings**

|Function|Description|Example|
|---|---|---|
|`strings.Replace(s, old, new, n)`|Replace `n` instances of `old`|`Replace("go go", "go", "run", 1) → "run go"`|
|`strings.ReplaceAll(s, old, new)`|Replace all instances|`ReplaceAll("go go", "go", "run") → "run run"`|
|`strings.ToUpper(s)`|Converts to upper case|`ToUpper("go") → "GO"`|
|`strings.ToLower(s)`|Converts to lower case|`ToLower("GO") → "go"`|
|`strings.TrimSpace(s)`|Remove leading/trailing whitespace|`TrimSpace(" hello ") → "hello"`|
|`strings.Trim(s, cutset)`|Remove characters in cutset from ends|`Trim("!hi!", "!") → "hi"`|
|`strings.TrimPrefix(s, prefix)`|Remove prefix if exists|`TrimPrefix("unhappy", "un") → "happy"`|
|`strings.TrimSuffix(s, suffix)`|Remove suffix if exists|`TrimSuffix("file.txt", ".txt") → "file"`|

---

### 🔹 5. **Splitting and Joining**

|Function|Description|Example|
|---|---|---|
|`strings.Split(s, sep)`|Splits string by separator|`Split("a,b,c", ",") → ["a", "b", "c"]`|
|`strings.SplitN(s, sep, n)`|Splits into `n` parts|`SplitN("a,b,c", ",", 2) → ["a", "b,c"]`|
|`strings.SplitAfter(s, sep)`|Include separator in result|`SplitAfter("a,b,c", ",") → ["a,", "b,", "c"]`|
|`strings.Join(slice, sep)`|Joins slice into string|`Join([]string{"a","b"}, "-") → "a-b"`|
|`strings.Fields(s)`|Split by whitespace (auto-trimmed)|`Fields(" a b\tc\n") → ["a", "b", "c"]`|

---

### 🔹 6. **Counting**

|Function|Description|Example|
|---|---|---|
|`strings.Count(s, substr)`|Count non-overlapping occurrences|`Count("cheese", "e") → 3`|

---

### 🔹 7. **Builder (Efficient String Concatenation)**

```go
var b strings.Builder
b.WriteString("Hello")
b.WriteRune(' ')
b.WriteString("World")
fmt.Println(b.String()) // "Hello World"

```

---

### ✅ Summary Table

|Category|Common Functions|
|---|---|
|Search|`Contains`, `ContainsAny`, `HasPrefix`, `Index`|
|Compare|`EqualFold`, `Compare`|
|Modify|`Replace`, `ToUpper`, `Trim`, `TrimSpace`|
|Split/Join|`Split`, `Join`, `Fields`|
|Count|`Count`|
|Builder|`strings.Builder`|

---

# 3. condition and loop:

---

## 🧾 Go `if` Condition Cheat Sheet

---

### 🔹 1. **Basic `if` Statement**

```go
if condition {
    // code to execute if condition is true
}

```

**Example:**

```go
if age >= 18 {
    fmt.Println("Adult")
}

```

---

### 🔹 2. **`if-else` Statement**

```go
if condition {
    // code if true
} else {
    // code if false
}

```

**Example:**

```go
if score >= 60 {
    fmt.Println("Pass")
} else {
    fmt.Println("Fail")
}

```

---

### 🔹 3. **`if-else if-else` Ladder**

```go
if condition1 {
    // code1
} else if condition2 {
    // code2
} else {
    // code3
}

```

**Example:**

```go
if score >= 90 {
    fmt.Println("Grade A")
} else if score >= 75 {
    fmt.Println("Grade B")
} else {
    fmt.Println("Grade C or below")
}

```

---

### 🔹 4. **`if` Statement with a Short Statement**

You can declare and initialize a variable in the `if` statement itself.

```go
if err := doSomething(); err != nil {
    fmt.Println("Error:", err)
} else {
    fmt.Println("Success")
}

```

---

### 🔹 5. **Nested `if` Statements**

```go
if condition1 {
    if condition2 {
        // code
    }
}

```

**Example:**

```go
if age >= 18 {
    if hasID {
        fmt.Println("Allowed to enter")
    } else {
        fmt.Println("ID required")
    }
}

```

---

### 🔹 6. **Multiple Conditions Using Logical Operators**

- **AND:** `&&`
- **OR:** `||`
- **NOT:** `!`

```go
if age >= 18 && hasID {
    fmt.Println("Allowed")
}
if age < 18 || !hasPermission {
    fmt.Println("Not allowed")
}

```

---

### 🔹 7. **Tips**

- Parentheses `()` are **optional** around conditions.
- The condition **must be a boolean expression**.
- No ternary operator (`? :`) in Go — use `if-else` instead.

---

### ✅ Summary Table

|Usage|Syntax Example|
|---|---|
|Basic if|`if x > 10 { ... }`|
|If-else|`if x > 10 { ... } else { ... }`|
|If-else if-else|`if x>10 {...} else if y>5 {...} else {...}`|
|If with short statement|`if err := f(); err != nil { ... }`|
|Nested if|`if a { if b { ... } }`|
|Logical operators|`if x>10 && y<20 { ... }`|

---

## 3.2 Switch:

---

## 🧾 Go `switch` Statement Cheat Sheet

---

### 🔹 1. **Basic `switch`**

```go
switch variable {
case value1:
    // code for value1
case value2:
    // code for value2
default:
    // code if no case matches
}

```

**Example:**

```go
day := "Tuesday"

switch day {
case "Monday":
    fmt.Println("Start of the week")
case "Tuesday":
    fmt.Println("Second day")
default:
    fmt.Println("Another day")
}

```

---

### 🔹 2. **Switch without an expression**

- Acts like `switch true`, useful for complex conditions.

```go
switch {
case x > 10:
    fmt.Println("x > 10")
case x > 5:
    fmt.Println("x > 5")
default:
    fmt.Println("x <= 5")
}

```

---

### 🔹 3. **Multiple cases in one line**

```go
switch day {
case "Saturday", "Sunday":
    fmt.Println("Weekend")
default:
    fmt.Println("Weekday")
}

```

---

### 🔹 4. **Switch with short statement**

```go
switch n := time.Now().Weekday(); n {
case time.Saturday, time.Sunday:
    fmt.Println("Weekend")
default:
    fmt.Println("Weekday")
}

```

---

### 🔹 5. **Fallthrough**

- By default, `switch` breaks after a case.
- Use `fallthrough` to continue to the next case (rarely needed).

```go
switch num := 2; num {
case 1:
    fmt.Println("One")
case 2:
    fmt.Println("Two")
    fallthrough
case 3:
    fmt.Println("Three")
default:
    fmt.Println("Other")
}

```

**Output:**

```
Two
Three

```

---

### 🔹 6. **Type Switch**

- Used to determine the type of an interface value.

```go
var x interface{} = 7

switch v := x.(type) {
case int:
    fmt.Printf("int %d\n", v)
case string:
    fmt.Printf("string %s\n", v)
default:
    fmt.Println("unknown type")
}

```

---

### ✅ Summary Table

|Feature|Syntax/Example|
|---|---|
|Basic switch|`switch x { case 1: ... default: ... }`|
|Switch without expr|`switch { case x>0: ... }`|
|Multiple cases|`case "a", "b", "c": ...`|
|Short statement|`switch v := f(); v { ... }`|
|Fallthrough|Use `fallthrough` to continue next case|
|Type switch|`switch v := x.(type) { case int: ... }`|

---

---

## 🧾 Go `switch` — `fallthrough` & `break` Cheat Sheet

---

### 🔹 1. **`fallthrough`**

- By default, Go's `switch` **does not fall through** to the next case after executing a case block.
- Use the `fallthrough` keyword **inside a case** to explicitly continue execution to the next case.
- `fallthrough` **does not check the next case’s condition**, it blindly executes the next case’s code.

**Example:**

```go
switch num := 2; num {
case 1:
    fmt.Println("One")
case 2:
    fmt.Println("Two")
    fallthrough    // Continue to next case regardless
case 3:
    fmt.Println("Three")
default:
    fmt.Println("Other")
}

```

**Output:**

```
Two
Three

```

---

### 🔹 2. **`break`**

- **Go’s `switch` automatically breaks** after a case block by default — no explicit `break` needed.
- You can use `break` to:
    - Exit an outer loop if the `switch` is inside a loop (using a label).
    - Exit the `switch` early from inside nested blocks.

**Example: Basic `break` (not needed to prevent fallthrough):**

```go
switch num := 2; num {
case 2:
    fmt.Println("Two")
    break  // optional, Go breaks automatically here
case 3:
    fmt.Println("Three")
}

```

**Example: `break` with label to exit loop:**

```go
OuterLoop:
for i := 0; i < 3; i++ {
    switch i {
    case 1:
        fmt.Println("Breaking loop")
        break OuterLoop  // breaks the for loop, not just switch
    }
}

```

---

### 🔹 3. **Key Points**

|Keyword|Purpose|Notes|
|---|---|---|
|`fallthrough`|Continue executing the _next_ case’s code unconditionally|Does **not** check the next case’s condition|
|`break`|Exit the `switch` (automatic after each case) or exit labeled loops|No need to break `switch` cases explicitly|

---

### ✅ Summary Example

```go
switch x := 1; x {
case 1:
    fmt.Println("One")
    fallthrough  // runs next case too
case 2:
    fmt.Println("Two")
    // no fallthrough here, switch ends
case 3:
    fmt.Println("Three")
}

```

Output:

```
One
Two

```

---

## 3.2 For & Loop

---

## 🧾 Go Loop & Control Statements Cheat Sheet

---

### 🔹 1. **Basic `for` Loop**

```go
for i := 0; i < 5; i++ {
    fmt.Println(i)
}

```

- Initialization: `i := 0`
- Condition: `i < 5`
- Post statement: `i++`

---

### 🔹 2. **`for` as `while` Loop**

Go **does not have a separate `while` keyword**. Use `for` with only a condition:

```go
i := 0
for i < 5 {
    fmt.Println(i)
    i++
}

```

---

### 🔹 3. **Infinite Loop**

```go
for {
    // endless loop
    fmt.Println("Loop forever")
}

```

---

### 🔹 4. **`continue` Statement**

- Skips the rest of the current loop iteration and moves to the next iteration.

```go
for i := 0; i < 5; i++ {
    if i%2 == 0 {
        continue  // skip even numbers
    }
    fmt.Println(i)  // prints only odd numbers: 1,3
}

```

---

### 🔹 5. **`break` Statement**

- Immediately exits the loop.

```go
for i := 0; i < 10; i++ {
    if i == 3 {
        break  // exit loop when i == 3
    }
    fmt.Println(i)
}

```

Output:

```
0
1
2

```

---

### 🔹 6. **Labeled `break` and `continue`**

- Use labels to break or continue outer loops from nested loops.

```go
OuterLoop:
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if i == 1 && j == 1 {
            break OuterLoop  // break outer loop
        }
        fmt.Printf("%d,%d\n", i, j)
    }
}

```

---

### ✅ Summary Table

|Statement|Usage|Example|
|---|---|---|
|`for` loop|Standard loop with init, condition, post|`for i:=0; i<5; i++ { ... }`|
|`for` as `while`|Loop with only condition|`for i < 5 { ... }`|
|Infinite loop|Loop with no condition|`for { ... }`|
|`continue`|Skip current iteration|`if condition { continue }`|
|`break`|Exit loop immediately|`if condition { break }`|
|Labeled `break`/`continue`|Control outer loops from nested loops|`break OuterLoop`, `continue OuterLoop`|

---

---

## 🧾 Go `for range` Loop Cheat Sheet

---

### 🔹 1. **What is `for range`?**

- The `for range` loop iterates over elements in various data structures: **arrays, slices, maps, strings, and channels**.
- It returns **one or two values** on each iteration:
    - For arrays, slices, strings: index and value.
    - For maps: key and value.
    - For channels: value.

---

### 🔹 2. **Syntax**

```go
for index, value := range collection {
    // use index and value
}

```

- You can ignore either index or value by using underscore `_`.

---

### 🔹 3. **Examples**

### a. Iterating over an array or slice

```go
arr := []string{"apple", "banana", "cherry"}

for i, v := range arr {
    fmt.Printf("Index: %d, Value: %s\n", i, v)
}

```

---

### b. Ignoring index

```go
for _, v := range arr {
    fmt.Println(v)
}

```

---

### c. Ignoring value

```go
for i := range arr {
    fmt.Println("Index:", i)
}

```

---

### d. Iterating over a map

```go
m := map[string]int{"a": 1, "b": 2}

for k, v := range m {
    fmt.Printf("Key: %s, Value: %d\n", k, v)
}

```

---

### e. Iterating over a string (by rune)

```go
s := "hello"

for i, r := range s {
    fmt.Printf("Index: %d, Rune: %c\n", i, r)
}

```

---

### f. Iterating over a channel

```go
ch := make(chan int, 2)
ch <- 1
ch <- 2
close(ch)

for v := range ch {
    fmt.Println(v)
}

```

---

### 🔹 4. **Important Notes**

- When ranging over a string, the index is the **byte index** of the rune.
- When ranging over a map, the iteration order is **random**.
- Avoid modifying the collection while ranging over it.
- Use underscore `_` to ignore unwanted return values.

---

### ✅ Summary Table

|Collection Type|Loop Syntax|Values Returned|
|---|---|---|
|Array/Slice|`for i, v := range arr {}`|index, value|
|String|`for i, r := range str {}`|byte index, rune|
|Map|`for k, v := range m {}`|key, value|
|Channel|`for v := range ch {}`|value|

---

# 4. Composite Datatype:

---

## 🧾 Go Arrays Cheat Sheet

---

### 🔹 1. **What is an Array?**

- An array is a **fixed-length sequence** of elements of the **same type**.
- The **length is part of the array’s type**.
- Arrays are **value types** (copy on assignment).

---

### 🔹 2. **Declaration**

```go
var arr [5]int      // array of 5 integers, default zero values

```

- Declares an array of length 5, all elements initialized to zero.

---

### 🔹 3. **Short Declaration & Initialization**

```go
arr := [3]string{"a", "b", "c"}

```

---

### 🔹 4. **Implicit Length**

Let Go count elements automatically:

```go
arr := [...]int{1, 2, 3, 4}

```

---

### 🔹 5. **Access and Modify Elements**

```go
arr[0] = 10
fmt.Println(arr[0])   // prints 10

```

---

### 🔹 6. **Array Length**

```go
len(arr)   // returns length of the array

```

---

### 🔹 7. **Arrays are Value Types**

Assigning one array to another copies the entire array:

```go
a := [3]int{1, 2, 3}
b := a
b[0] = 10
fmt.Println(a[0])  // 1 (original not changed)

```

---

### 🔹 8. **Iterate Over Arrays**

Using `for` loop:

```go
for i := 0; i < len(arr); i++ {
    fmt.Println(arr[i])
}

```

Using `range`:

```go
for i, v := range arr {
    fmt.Printf("Index %d: Value %v\n", i, v)
}

```

---

### 🔹 9. **Multidimensional Arrays**

```go
var matrix [2][3]int
matrix[0][1] = 5

```

---

### 🔹 10. **Array vs Slice**

- Arrays have **fixed size**.
- Slices are **dynamic, flexible views over arrays**.
- Use slices for most flexible code.

---

### ✅ Summary Table

|Operation|Syntax/Example|
|---|---|
|Declare array|`var arr [5]int`|
|Declare with init|`arr := [3]string{"x","y","z"}`|
|Implicit length|`arr := [...]int{1,2,3}`|
|Access element|`arr[0]`|
|Length|`len(arr)`|
|Copy array|`b := a` (creates a copy)|
|Iterate array (for)|`for i:=0; i<len(arr); i++ {}`|
|Iterate array (range)|`for i,v := range arr {}`|
|Multidimensional array|`var m [2][2]int`|

---

## 4.1 Slice:

How Write slice?

1. write like array but without number:

```go
	mySlice := []int{Red, Blue, Green}

```

1. use built-in to create default slice with n length:

```go
	mySlice2 := make([]int, 10)
```

convert array to slice:

```go
myArray := [3]int{1,2,3}
mySlice := myArray[1:2]
//output: 2
```

## 4.2 Map:

---

## 🧾 Go `map` Cheat Sheet

A `map` is an unordered collection of key-value pairs.

---

### 🔹 1. **Declaration**

```go
var m map[string]int

```

- Declares a nil map (not initialized, cannot assign values yet).

---

### 🔹 2. **Initialization**

### a. Using `make`

```go
m := make(map[string]int)

```

### b. Using literal

```go
m := map[string]int{
    "a": 1,
    "b": 2,
}

```

---

### 🔹 3. **Add or Update Values**

```go
m["c"] = 3  // add new key or update existing

```

---

### 🔹 4. **Access Values**

```go
val := m["a"]  // returns 1

```

---

### 🔹 5. **Check if Key Exists**

```go
val, ok := m["x"]
if ok {
    fmt.Println("Key exists:", val)
} else {
    fmt.Println("Key not found")
}

```

---

### 🔹 6. **Delete a Key**

```go
delete(m, "b")

```

---

### 🔹 7. **Length of Map**

```go
len(m)  // number of key-value pairs

```

---

### 🔹 8. **Iterate Over Map**

```go
for key, value := range m {
    fmt.Printf("Key: %s, Value: %d\n", key, value)
}

```

> 🔸 Map iteration order is randomized in Go.

---

### 🔹 9. **Nil vs Empty Map**

```go
var a map[string]int     // nil map, cannot assign keys
b := map[string]int{}    // empty map, ready to use

```

---

### 🔹 10. **Map of Structs / Nested Maps**

```go
type User struct {
    Name string
    Age  int
}

users := map[string]User{
    "id1": {"Alice", 30},
    "id2": {"Bob", 25},
}

```

Nested map:

```go
nested := map[string]map[string]int{
    "group1": {
        "x": 1,
        "y": 2,
    },
}

```

---

### ✅ Summary Table

|Operation|Example|
|---|---|
|Declare|`var m map[string]int`|
|Initialize|`m := make(map[string]int)`|
|Literal|`m := map[string]int{"a": 1}`|
|Access|`val := m["key"]`|
|Check existence|`val, ok := m["key"]`|
|Add/Update|`m["key"] = 42`|
|Delete|`delete(m, "key")`|
|Length|`len(m)`|
|Iterate|`for k, v := range m { ... }`|

---

# 5. Function:

---

## 🧾 Go Function Cheat Sheet

---

### 🔹 1. **Basic Function Declaration**

```go
func add(a int, b int) int {
    return a + b
}

```

- `func`: keyword to declare a function
- `a, b int`: parameters
- `int`: return type

---

### 🔹 2. **Multiple Parameters of Same Type**

```go
func multiply(x, y int) int {
    return x * y
}

```

---

### 🔹 3. **Multiple Return Values**

```go
func divide(dividend, divisor int) (int, int) {
    return dividend / divisor, dividend % divisor
}

```

Usage:

```go
q, r := divide(10, 3)

```

---

### 🔹 4. **Named Return Values**

```go
func split(sum int) (x, y int) {
    x = sum * 2 / 3
    y = sum - x
    return  // returns x and y
}

```

---

### 🔹 5. **Variadic Functions**

```go
func sum(nums ...int) int {
    total := 0
    for _, v := range nums {
        total += v
    }
    return total
}

```

Usage:

```go
sum(1, 2, 3, 4)

```

---

### 🔹 6. **Function as a Value**

```go
func greet(name string) string {
    return "Hello, " + name
}

var f func(string) string = greet
fmt.Println(f("Alice"))  // "Hello, Alice"

```

---

### 🔹 7. **Anonymous Functions (Lambdas)**

```go
func() {
    fmt.Println("Anonymous function")
}()

```

With parameters:

```go
add := func(a, b int) int {
    return a + b
}

```

---

### 🔹 8. **Closures**

A closure **captures variables** from its enclosing scope:

```go
func counter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

c := counter()
fmt.Println(c()) // 1
fmt.Println(c()) // 2

```

---

### 🔹 9. **Defer in Functions**

```go
func example() {
    defer fmt.Println("Deferred")
    fmt.Println("Now")
}

```

Output:

```
Now
Deferred

```

> defer delays execution until the surrounding function returns.

---

### 🔹 10. **Recursion**

```go
func factorial(n int) int {
    if n == 0 {
        return 1
    }
    return n * factorial(n-1)
}

```

---

### 🔹 11. **Function Types**

```go
type operation func(int, int) int

func add(a, b int) int {
    return a + b
}

var op operation = add
fmt.Println(op(3, 4))  // 7

```

---

### 🔹 12. **Init Function**

- Special function run **before `main()`**, only once per package:

```go
func init() {
    fmt.Println("Init function")
}

```

---

### 🔹 13. **Function Types**

---

### ✅ Summary Table

---

|Feature|Syntax / Example|
|---|---|
|Basic Function|`func name(p Type) Type { ... }`|
|Multiple Returns|`func(x int) (int, int)`|
|Named Returns|`func(x int) (a, b int)`|
|Variadic Params|`func(x ...int)`|
|Anonymous Func|`f := func(x int) int { return x*x }`|
|Closure|`func() func() int { ... }`|
|Function Type Alias|`type op func(int, int) int`|
|Defer|`defer fmt.Println("done")`|
|Recursion|`func f(n int) int { return f(n-1) }`|

---

# 6. Struct

How create strucrt?

```go
type Person struct{
				Firstname string
				Lastname string
				Age int
	}
```

How to use struct?

```go
//1
person1 := Person{Firstname : "Parsa", lastname: "Eftekharmanesh", age : 23}

//2
parsa := new(Person)
parsa.Firstname = "Parsa"
parsa.Lastname = "Eftekharmanesh"
parsa.age = 23
```

## 6.2 method and Struct

---

## 🧩 Go Method Cheat Sheet

### 🔹 1. What Is a Method?

A **method** is a function with a **receiver** – it's associated with a specific **type**.

```go
func (r ReceiverType) MethodName(params) ReturnType {
    // method body
}

```

---

### 🔹 2. Value Receiver vs Pointer Receiver

### Value Receiver:

```go
type Person struct {
    Name string
}

func (p Person) Greet() {
    fmt.Println("Hello,", p.Name)
}

```

- **Copies the receiver**
- Modifications don’t affect original object

### Pointer Receiver:

```go
func (p *Person) Rename(newName string) {
    p.Name = newName
}

```

- **Modifies original**
- More efficient for large structs

---

### 🔹 3. Method on Struct

```go
type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return math.Pi * c.Radius * c.Radius
}

```

---

### 🔹 4. Method on Non-Struct Types (Type Aliases)

```go
type MyInt int

func (m MyInt) Double() int {
    return int(m * 2)
}

```

---

### 🔹 5. Method Set

|Type|Method Set Includes|
|---|---|
|`T`|Methods with receiver `T`|
|`*T`|Methods with receiver `T` or `*T`|

---

### 🔹 6. Interface Compatibility

If a type has all methods required by an interface, it implements it **implicitly**:

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type MyReader struct{}

func (m MyReader) Read(p []byte) (int, error) {
    return 0, nil
}

```

---

### 🔹 7. Embedding and Promotion

```go
type Animal struct{}

func (a Animal) Speak() {
    fmt.Println("Animal sound")
}

type Dog struct {
    Animal
}

d := Dog{}
d.Speak() // Promoted method

```

---

### 🔹 8. Best Practices

✅ Use **pointer receivers** when:

- The method modifies the receiver
- The receiver is large and copying is expensive

✅ Use **value receivers** when:

- The method does not modify the receiver
- The receiver is small or a basic type

---

### 🔹 9. Common Pitfalls

- Forgetting in pointer receivers:
    
    ```go
    func (p Person) ChangeName(...) { // ❌ won't modify original
    
    ```
    
- Mixing value/pointer receivers inconsistently
    
- Interfaces only match methods on the **method set**
    

---

## Example of polymorphism:

1. Ticket example:

```
package main

import "fmt"

type BusTicket struct {
	Id                int
	DepartureCity     string
	ArrivalCity       string
	DepartureTime     string
	BusType           string
	PassengerName     string
	DepartureTerminal string
	ArrivalTerminal   string
	NationalCode      string
	Price             int
}

type FlightTicket struct {
	Id               int
	DepartureAirport string
	ArrivalAirport   string
	DepartureTime    string
	ArrivalTime      string
	AirplaneType     string
	PassengerName    string
	PassportId       string
	PassengerType    string
	Price            int
}

func main() {

	busTicket := BusTicket{
		Id:                1,
		DepartureCity:     "Tehran",
		ArrivalCity:       "Mashhad",
		DepartureTime:     "12:00",
		BusType:           "Bus",
		PassengerName:     "Reza kamali",
		DepartureTerminal: "Terminal 1",
		ArrivalTerminal:   "Terminal 2",
		NationalCode:      "123456789",
		Price:             100,
	}

	flightTicket := FlightTicket{
		Id:               2,
		DepartureAirport: "Tehran",
		ArrivalAirport:   "London",
		DepartureTime:    "12:00",
		ArrivalTime:      "23:00",
		AirplaneType:     "Airbus",
		PassengerName:    "Peyman Hassani",
		PassportId:       "312321414",
		PassengerType:    "Adult",
		Price:            1890,
	}

	printer := []TicketPrinter{busTicket, flightTicket}

	for _, printer := range printer {
		printer.PrintTicket()
	}

}

type TicketPrinter interface {
	PrintTicket()
}

func (ticket BusTicket) PrintTicket() {
	fmt.Printf("Bus Ticket:\n ID: %d\n DepartureCity : %s\n ArrivalCity : %s\n DepartureTime : %s\n", ticket.Id, ticket.DepartureCity, ticket.ArrivalCity, ticket.DepartureTime)
	fmt.Printf("BusType : %s\n PassengerName : %s\n DepartureTerminal : %s\n ArrivalTerminal : %s\n", ticket.BusType, ticket.PassengerName, ticket.DepartureTerminal, ticket.ArrivalTerminal)
	fmt.Printf("NationalCode : %s\n Price : %d\n", ticket.NationalCode, ticket.Price)
}

func (ticket FlightTicket) PrintTicket() {
	fmt.Printf("Flight Ticket:\n ID: %d\n DepartureAirport : %s\n ArrivalAirport : %s\n DepartureTime : %s\n ArrivalTime : %s\n", ticket.Id, ticket.DepartureAirport, ticket.ArrivalAirport, ticket.DepartureTime,ticket.ArrivalTime)
	fmt.Printf("AirplaneType : %s\n PassengerName : %s\n PassportId : %s\n PassengerType : %s\n", ticket.AirplaneType, ticket.PassengerName, ticket.PassportId, ticket.PassengerType)
	fmt.Printf("Price : %d\n", ticket.Price)
}
```

1. Employee Example:

```go
package main

import "fmt"

const (
	BaseSalary       = 5600000
	ExtraHourRate    = 90000
	HourlySalaryRate = 110000
	ShiftSalaryRate  = 80000
	TaxRate          = 0.09
)

func main() {
	// 
	fullTimeEmployees := []FullTimeEmployee{
		{Id: 1, NationalCode: "1234567890", FullName: "Pejman Rezaee", ExtraHours: 40},
		{Id: 2, NationalCode: "4836524125", FullName: "Maryam Hosseini", ExtraHours: 120},
	}

	partTimeEmployees := []PartTimeEmployee{
		{Id: 3, NationalCode: "6563453455", FullName: "Milad Hassani", PartTimeHours: 100},
		{Id: 4, NationalCode: "5435435435", FullName: "Maryam Rezaee", PartTimeHours: 87},
	}

	shiftEmployees := []ShiftEmployee{
		{Id: 5, NationalCode: "3123123213", FullName: "Shahin", ShiftHours: 150},
		{Id: 6, NationalCode: "6363454355", FullName: "Masoud", ShiftHours: 168},
	}

	var employees []Employee = []Employee{}

	for _, employee := range fullTimeEmployees {
		employees = append(employees, employee)
	}
	
	for _, employee := range partTimeEmployees {
		employees = append(employees, employee)
	}
	
	for _, employee := range shiftEmployees {
		employees = append(employees, employee)
	}

	for _, employee := range employees {
		salary, tax := employee.SalaryCalculator()
		fmt.Printf("\nEmployee (%T): %v\nSalary: %f\nTax: %f\n", employee, employee, salary, tax)
	}
 
}

type Employee interface {
	SalaryCalculator() (salary float64, tax float64)
}

type FullTimeEmployee struct {
	Id           int
	NationalCode string
	FullName     string
	ExtraHours   float64
}

type PartTimeEmployee struct {
	Id            int
	NationalCode  string
	FullName      string
	PartTimeHours float64
}

type ShiftEmployee struct {
	Id           int
	NationalCode string
	FullName     string
	ShiftHours   float64
}

func (employee FullTimeEmployee) SalaryCalculator() (salary float64, tax float64) {
	salary = BaseSalary + (ExtraHourRate*employee.ExtraHours)*1.4
	tax = salary * TaxRate
	salary += tax
	return
}

func (employee PartTimeEmployee) SalaryCalculator() (salary float64, tax float64) {
	salary = HourlySalaryRate * employee.PartTimeHours
	tax = salary * TaxRate
	salary += tax
	return
}

func (employee ShiftEmployee) SalaryCalculator() (salary float64, tax float64) {
	salary = ShiftSalaryRate * employee.ShiftHours
	tax = salary * TaxRate
	salary += tax
	return
}
```

## 6.2 Embedded Struct:

---

## 📦 Embedded Struct & Composition in Go (Cheat Sheet)

---

### 🔹 1. What is Embedding?

In Go, **embedding** allows one struct to **include another struct anonymously** — enabling field and method promotion, mimicking **inheritance-like behavior**.

---

### 🔹 2. Basic Syntax

```go
type Person struct {
	Name string
	Age  int
}

type Employee struct {
	Person // Embedded struct
	Salary int
}

```

✅ Now `Employee` **has access** to `Person`’s fields:

```go
emp := Employee{
	Person: Person{Name: "Alice", Age: 30},
	Salary: 5000,
}

fmt.Println(emp.Name) // Access promoted field directly

```

---

### 🔹 3. Promoted Methods

Methods of the embedded struct are also promoted:

```go
func (p Person) Greet() {
	fmt.Println("Hello,", p.Name)
}

emp.Greet() // Works, even though Greet() is on Person

```

---

### 🔹 4. Field Name Collision

If both structs have a field with the **same name**, access must be **disambiguated**:

```go
type A struct {
	Value int
}

type B struct {
	Value int
	A     // embedded
}

b := B{Value: 1, A: A{Value: 2}}

fmt.Println(b.Value)   // 1 (from B)
fmt.Println(b.A.Value) // 2 (from A)

```

---

### 🔹 5. Multiple Embeddings

You can embed **multiple structs**:

```go
type Logger struct {
	LogLevel string
}

type Config struct {
	Timeout int
}

type Server struct {
	Logger
	Config
}

srv := Server{Logger: Logger{"DEBUG"}, Config: Config{Timeout: 10}}

fmt.Println(srv.LogLevel) // "DEBUG"
fmt.Println(srv.Timeout)  // 10

```

---

### 🔹 6. Composition > Inheritance

- Go uses **composition** (embedding) rather than classical inheritance.
- It promotes loose coupling and modular design.
- Embedded structs are **not subclasses** but more like components.

---

### 🧠 Bonus: Embedding Interfaces

You can embed interfaces too:

```go
type Reader interface {
	Read(p []byte) (n int, err error)
}

type File struct {
	Reader // any type implementing Read()
}

```

---

## ✅ Summary

|Feature|Supported via Embedding|
|---|---|
|Field promotion|✅ Yes|
|Method promotion|✅ Yes|
|Inheritance|❌ No (but mimicked)|
|Multiple inheritance|✅ (via multiple embeds)|
|Overriding|❌ Not supported|

---

# 7. Interface

---

## 🧩 Go Interface Cheat Sheet

---

### 📌 What is an Interface?

An **interface** is a type that specifies **a set of method signatures**. If a type implements those methods, it **satisfies the interface** implicitly.

---

### 🔹 1. Basic Syntax

```go
type Shape interface {
	Area() float64
}

```

---

### 🔹 2. Implementing an Interface

```go
type Circle struct {
	Radius float64
}

func (c Circle) Area() float64 {
	return 3.14 * c.Radius * c.Radius
}

```

Since `Circle` has `Area() float64`, it satisfies `Shape`.

---

### 🔹 3. Interface Usage

```go
func PrintArea(s Shape) {
	fmt.Println("Area:", s.Area())
}

circle := Circle{Radius: 5}
PrintArea(circle)

```

---

### 🔹 4. Interface with Multiple Methods

```go
type WriterReader interface {
	Write([]byte) (int, error)
	Read([]byte) (int, error)
}

```

Any type that has both `Write()` and `Read()` methods satisfies `WriterReader`.

---

### 🔹 5. Empty Interface (`interface{}`)

```go
var x interface{} = 42

```

The empty interface can hold **any value** — like `Object` in other languages.

Use with **type assertion** or **type switches**:

```go
value, ok := x.(int)
switch v := x.(type) {
case string:
	fmt.Println("string", v)
case int:
	fmt.Println("int", v)
}

```

---

### 🔹 6. Interface Composition

Interfaces can embed other interfaces:

```go
type Reader interface {
	Read([]byte) (int, error)
}

type Closer interface {
	Close() error
}

type ReadCloser interface {
	Reader
	Closer
}

```

---

### 🔹 7. Interfaces Are Implicit

There’s **no `implements` keyword**. If a type has all required methods, it satisfies the interface.

---

### 🔹 8. Interface as Return Type

```go
func GetShape() Shape {
	return Circle{Radius: 3}
}

```

This enables polymorphism.

---

### 🔹 9. Nil Interface Pitfall

```go
var s Shape
fmt.Println(s == nil) // true

var c *Circle = nil
s = c
fmt.Println(s == nil) // false!

```

Because `s` holds a **typed nil**, it’s not equal to `nil`.

---

### 🔹 10. Interface Best Practices

- Use small, focused interfaces (`io.Reader`, `fmt.Stringer`)
- Use interface **as input** to functions
- Return concrete types when possible

---

### ✅ Summary Table

|Concept|Example|
|---|---|
|Define interface|`type Shape interface { Area() }`|
|Implement interface|`func (c Circle) Area() float64`|
|Use interface param|`func Print(s Shape)`|
|Empty interface|`var x interface{}`|
|Type assertion|`val := x.(int)`|
|Type switch|`switch x.(type) { ... }`|

---

# 8. Generics:

---

## 🧬 Go Generics Cheat Sheet

---

### 🧠 What Are Generics?

Generics let you write **functions, types, and methods** that work with **any type** — while preserving **type safety**.

Think of generics as **"type parameters"** for functions or structs.

---

## 🔹 1. Generic Function Syntax

```go
func Identity[T any](val T) T {
	return val
}

```

- `T` is a **type parameter**.
- `any` is a built-in constraint (alias for `interface{}`).

### ✅ Usage:

```go
x := Identity
y := Identity[string]("hello")

```

---

## 🔹 2. Generic Type (Struct)

```go
type Box[T any] struct {
	Value T
}

```

### ✅ Usage:

```go
b := Box[int]{Value: 100}
fmt.Println(b.Value)

```

---

## 🔹 3. Multiple Type Parameters

```go
func Pair[A, B any](a A, b B) (A, B) {
	return a, b
}

```

---

## 🔹 4. Type Constraints

Constraints restrict what kind of types can be passed.

```go
type Number interface {
	~int | ~float64
}

func Add[T Number](a, b T) T {
	return a + b
}

```

- `~int` allows custom types based on `int`.
- `|` means “or”.

---

## 🔹 5. Constraint as Interface

```go
type Stringer interface {
	String() string
}

func PrintString[T Stringer](s T) {
	fmt.Println(s.String())
}

```

---

## 🔹 6. Type Sets with Union

```go
type Ordered interface {
	~int | ~float64 | ~string
}

```

Use for comparable or sortable values.

---

## 🔹 7. Generic Method on Struct

```go
type Container[T any] struct {
	items []T
}

func (c *Container[T]) Add(item T) {
	c.items = append(c.items, item)
}

```

---

## 🔹 8. Instantiation (Explicit or Inferred)

```go
Identity         // explicit
Identity("hello")        // inferred

```

---

## ✅ Built-in Generic Constraints

|Constraint|Description|
|---|---|
|`any`|Accepts any type (like `interface{}`)|
|`comparable`|Supports `==` and `!=`|

---

## 🧪 Practical Examples

### Sort generic slice:

```go
func Min[T Ordered](a, b T) T {
	if a < b {
		return a
	}
	return b
}

```

---

### Generic Map Function

```go
func Map[T any, R any](arr []T, f func(T) R) []R {
	result := make([]R, len(arr))
	for i, v := range arr {
		result[i] = f(v)
	}
	return result
}

```

---

## ⚠️ Limitations

- No higher-kinded types
- Limited reflection on generics
- Can't overload by type param

---

## 📚 Summary Table

|Use Case|Syntax|
|---|---|
|Generic function|`func Foo[T any](x T) T`|
|Generic struct|`type Box[T any] struct`|
|Constraint|`func Bar[T MyInterface](x T)`|
|Multiple types|`func Baz[A, B any](a A, b B)`|
|Type inference|`Foo(123)`|
|Type set|`~int|

---

# 9. Error and panic

---

# 🧨 Go Error, Panic & Recover Cheat Sheet

---

## 📦 1. `error` – The Standard Error Handling

### 🔹 Basic Concept

In Go, error handling is **explicit**. Functions return `error` as a second return value.

```go
func DoSomething() error {
    return errors.New("something went wrong")
}

```

### 🔹 Using `errors.New` and `fmt.Errorf`

```go
import "errors"
import "fmt"

err := errors.New("basic error")
err2 := fmt.Errorf("wrapped error: %w", err)

```

---

### 🔹 Error Handling Pattern

```go
result, err := DoSomething()
if err != nil {
    log.Fatal(err)
}

```

---

### 🔹 Custom Error Type

```go
type MyError struct {
    Code int
    Msg  string
}

func (e MyError) Error() string {
    return fmt.Sprintf("Error %d: %s", e.Code, e.Msg)
}

return MyError{Code: 404, Msg: "Not Found"}

```

---

### 🔹 Check Error Type with `errors.As` / `errors.Is`

```go
var myErr MyError
if errors.As(err, &myErr) {
    fmt.Println("it's a MyError:", myErr.Code)
}

if errors.Is(err, io.EOF) {
    fmt.Println("Reached end of file")
}

```

---

## 🔥 2. `panic` – For Unexpected, Non-Recoverable Errors

### 🔹 What is `panic`?

`panic` stops the normal flow of execution and begins **stack unwinding**.

```go
panic("something really bad happened")

```

### 🔹 When to Use

✅ Use panic for:

- Bugs (invalid array indexes, nil pointer dereference)
- Programmer errors
- Unrecoverable system failures

❌ Don't use panic for:

- Invalid user input
- Expected errors (like file not found)

---

### 🔹 Panic Example

```go
func dangerous() {
    panic("bad thing happened")
}

```

Execution will stop and unwind the stack until it finds a deferred `recover`.

---

## 🧯 3. `recover` – Catch a Panic and Resume

### 🔹 What is `recover`?

`recover()` is used **inside a `defer`** to catch a panic and allow graceful recovery.

---

### ✅ Example: Catching a Panic

```go
func safe() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from panic:", r)
        }
    }()
    panic("oh no!")
}

```

Output:

```
Recovered from panic: oh no!

```

---

### ❗ `recover()` only works in `defer`

This **won’t work**:

```go
// Wrong
r := recover() // will always return nil here

```

---

## 🔄 Full Cycle Example

```go
func main() {
    fmt.Println("Start")
    safe()
    fmt.Println("End")
}

func safe() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered:", r)
        }
    }()
    panic("Something bad")
}

```

---

## 🧠 Summary Table

|Feature|Purpose|When to Use|
|---|---|---|
|`error`|Standard error handling|Expected or user-triggered issues|
|`panic`|Abort execution on severe failure|Programmer bugs or system issues|
|`recover`|Handle panics and continue execution|Gracefully recover from panics|

---

## 🧪 Best Practices

✅ **Use `error` for expected failures**

✅ **Use `fmt.Errorf` with `%w` to wrap errors**

✅ **Use `errors.Is` and `errors.As` for type matching**

✅ **Log panics and recover where appropriate**

❌ **Don’t use panic for control flow**

---

## 🧰 Real-World Example: HTTP Server with Panic Recovery

```go
func RecoveryMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        defer func() {
            if err := recover(); err != nil {
                log.Println("Recovered:", err)
                http.Error(w, "Internal Server Error", http.StatusInternalServerError)
            }
        }()
        next.ServeHTTP(w, r)
    })
}

```

---

## 📌 Quick Reference Snippets

### Return an error

```go
return fmt.Errorf("failed to do X: %w", err)

```

### Check custom error

```go
type MyErr struct{}
func (e MyErr) Error() string { return "my err" }

if errors.As(err, &MyErr{}) { ... }

```

### Panic + Recover

```go
defer func() {
    if r := recover(); r != nil {
        log.Println("Recovered panic:", r)
    }
}()
panic("fatal error")

```

---

# 9. Multi-thread