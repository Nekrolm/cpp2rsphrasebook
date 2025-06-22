# Syntax

<table border="1" cellspacing="0" cellpadding="8">
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
    t; // `return` keyword is optional
}
```
   </td>
  </tr>
  <tr>
    <td>
    You may call me insane, but this is the closes equivalent of Rust's functions in C++20!
    </td>
    <td>
    Each function is Rust has it's own, unique type -- like lambda funcions in C++. It provides several benefits for inlining and compiler optimizations, but has implications on the compulation time and binary size
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
    Variables in C++ are mutable by default and we need `const` to make them immutable.
    </td>
    <td>
    In Rust the defaults are reversed -- variables are immutable by default, use `mut` to make them mutable.
   For `const` and `static` variables, Rust requires explicit type annotations. Also Rust linters, by default, want to see `SCREAMING_SNAKE_CASE` for `const` and `static` variables -- because they are usually require extra attention. Especially static variables
    </td>
  </tr>

</table>