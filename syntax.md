# Syntax

<table>
<tr>
    <th></th>
    <th>C++</th>
    <th>Rust</th>
</tr>


  <tr>
    <td rowspan="2">Function definition</td>
    <td>

```cpp
constexpr inline auto fun = []<class T>(T t) -> T {
    return t;
};
```   
   </td>
    <td>

```rust
fn fun<T>(t: T) -> T {
    t // `return` keyword is optional
}
```
   </td>
  </tr>
  <tr>
    <td>
    You may call me insane, but this is the closest equivalent of Rust's functions in C++20!
    </td>
    <td>
    Each function is Rust has it's own, unique type -- like lambda funcions in C++. It provides several benefits for inlining and compiler optimizations, but has implications on the compilation time and binary size
    </td>
  </tr>

  <tr>
    <td rowspan="2">Variable definition</td>
    <td>

```cpp
auto mut_val = 42;

const auto const_val = 42;

constexpr auto constexpr_val = 42;

const int64_t int_val = 42;

static auto static_mut_val = 42;

static const auto static_const_val = 42;
```   
   </td>
    <td>

```rust
let mut mut_val = 42;

let const_val = 42; 

let int_val: i64 = 42; // explicit type

const CONSTEXPR_VAL: i32 = 42; // `const` is used for constexpr values.

static mut STATIC_MUT_VAL: i32 = 42; 
static STATIC_CONST_VAL: i32 = 42;
```
   </td>
  </tr>
  <tr>
    <td>
    Variables in C++ are mutable by default and we need <code>const</code> to make them immutable.
    </td>
    <td>
    In Rust the defaults are reversed -- variables are immutable by default, use <code>mut</code> to make them mutable.
   For <code>const</code> and <code>static</code> variables, Rust requires explicit type annotations. Also Rust linters, by default, want to see <code>SCREAMING_SNAKE_CASE</code> for them -- because they are usually require extra attention. Especially static variables. 
    </td>
  </tr>




  <tr>
    <td rowspan="2">Constexpr functions</td>
    <td>

```cpp
constexpr int add(int a, int b) {
    return a + b;
}
constexpr const int result = add(1,2); 
```   

   </td>
    <td>

```rust
const fn add(a: i32, b: i32) -> i32 {
    a + b
}
const RESULT: i32 = add(1, 2);
```
   </td>
  </tr>
  <tr>
    <td>
    C++ constexpr is extremely powerfull, with C++23 almost anything can be done at the compilation time.
    <br>
    Note that in form of the lambda functions, as shown above, <code>constexpr</code> specifier is already included for implicitly declared <code>operator()</code>
    </td>
    <td>
    Constants and statics in Rust can be initiailed only with constant expressions, e.g. functions that are makred with <code>const</code>. const functions are similat to C++ constexpr functions, but they are much more limited. E.g. there are no compile-time allocations, `for` loops are not supported in const context. See <a href="https://doc.rust-lang.org/reference/const_eval.html">Rust reference</a> for more details.
    </td>
  </tr>

 <tr>
   <td>Struct definition</td>

   <td>


```cpp

struct Pair {
    int32_t x;
    int32_t y;
};

// C++20
auto p = Pair {.x = 5, .y = 10};

```

   </td>

   <td>
   
   ```rust
   struct Pair {
     x: i32,
     y: i32,
   }
   
   let p = Pair { x : 5, y : 10};


   // use pub visibility modifier
   // if the struct fields are intended to be accessible outside of the 
   // module that defines a structure

   pub struct Point {
     pub x : i32,
     pub y : i32
   }
   ```

   </td>
 </tr>

 <tr>
 <td>Enum definition (trivial) </td>

  <td>
  
  ```cpp
  
  enum class MyEnum : uint32_t {
     Value1 = 1,
     Value2 = 2,
     Value3
  };
  
  ```
  
  </td>

  <td>

  ```rust
  #[repr(u32)]
  enum MyEnum {
    Value1 = 1,
    Value2 = 2,
    Value3
  } 
  ```

 </td>

 </tr>



  <tr>
 <td>Enum definition (tagged union) </td>

  <td>
  
  ```cpp
  
#include <variant>
struct Leaf { 
  int32_t value; 
};
struct Tree: 
  std::variant<
      Leaf, 
      std::vector<Tree>
  > {};
  
  ```
  
  </td>

  <td>

```rust

enum Tree {
  Leaf { value : i32 },
  Node(Vec<Tree>)
}

  ```

 </td>

 </tr>



<tr>
 <td rowspan="2"> Switch / Match over trivial enum </td>

  <td>
  
  ```cpp
  MyEnum e = ...;

  auto value = [e]{
    // C++20 !
    using enum MyEnum;
    switch (e) {
        case Value1: return 25;
        case Value2: return 10;
        case Value3: return 0;
        default: return 45;
    };
  }();

  
  ```
  
  </td>

  <td>

  ```rust
  let e : MyEnum = ...;
  use MyEnum::*;
  let value = match e {
     Value1 => 25,
     Value2 => 10,
     Value3 => 0,
     _ => 45,
  };
  ```

 </td>
</tr>

<tr>
<td>
Rust is expression-oriented language. That's why I decided to show C++'s switch inside
the immediately invoked lambda -- this is the closes counterpart
</td>

<td>
Of course, usage of <code>use MyEnum</code> or <code>using enum MyEnum</code> is not mandatary. I demonstrate it just because such option exists and it's useful
</td>

 </tr>


<tr>

<td rowspan="2"> Switch / Match over tagged union </td>

<td>

```cpp
#include <variant>

// C++20
// helper type for the visitor
template<class... Ts>
struct matcher : Ts... { using Ts::operator()...; };


Tree t = ...;

size_t estimate(const Tree& t) {
    t.visit(
        matcher {
            [](const Leaf& l) -> size_t { return l.value; },
            [](const std::vector<Tree>& n) -> { return n.size(); }
        }
    );
}

```

</td>

<td>

```rust

fn estimate(t: &Tree) -> usize {
    match t {
        Leaf { value } => value as usize,
        Node(next) => next.len() * 5
    }
}

```

</td>

</tr>
<tr>

<td> That's beatiful and painful. <code>std::variant</code> is not a core language construct but custom type with methods </td>
<td> Tagged enums are language feature in Rust. </td>

</tr>



</table>