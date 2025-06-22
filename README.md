# Modern C++ <-> Rust translation phrasebook

This guidebook provides a comprehensive list of concepts, types, idioms and patterns in C++ and their closest equivalents in Rust.

The contents is structured to show side-by-side comparisons for C++ and Rust snippets. I group these snippets by topics: syntax, types, control flow, etc. -- See table of contents below.

When it's needed I'll provide a comments for correspoding side to higlight potential pitfalls, differenes or just interesting facts.

After many years of working with C++ and Rust, I prefer Rust for sane default and ergonomic design. However, I still use C++ and tracking its evolution. In this guidebook I will be focusing on modern C++ (C++20 and newer) and will be taking the closest equivalents in C++ for the Rust code, and vice versa.

So, for example, here is a simple "Hello, World!" example in both languages: It uses the `std::println` from C++23. I'm intentionally not going to use `std::cout` ostream operations, because they are horrible legacy we all want to forget.

<table>
<tr>
    <th></th>
    <th>C++</th>
    <th>Rust</th>
</tr>
<tr>
<td>Print "Hello, World!"</td>
<td>

```cpp
#include <print>
int main() {
    std::println("Hello, C++!");
    return 0;
}
```
</td>
    
<td>

```rust
fn main() {
    println!("Hello, Rust!");
}
```
</td>
</tr>
</table>

# Table of Contents
- [Syntax](syntax.md)
- [Pointers and references](pointers.md)