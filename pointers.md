# Pointers and references

<table>
<tr>
    <th></th>
    <th>C++</th>
    <th>Rust</th>
</tr>


<tr>
    <td rowspan="2">References</td>
    <td>

```cpp
#include <functional>
template <class T>
using ref = std::reference_wrapper<T>

int x = 42;
ref<int> mut_ref = x;
mut_ref.get() = 45; // dereference and mutate
ref<const int> const_ref = x;

int y = 43;
mut_ref = ref(y); // ref wrapper can be rebinded

ref<int> array_of_refs[] = {ref(x), ref(y)}; 
```   
   </td>
    <td>

```rust  

let mut x = 42;
let mut mut_ref = &mut x;
*mut_ref = 45; // deference and mutate
let const_ref = &x;

let mut y = 43;
mut_ref = &mut y; // rebind reference

let array_of_refs: [&mut i32; 2] = [&mut x, &mut y]
```   
   </td>
  </tr>
  <tr>
    <td>
   C++ built-in references <code>T&</code> and <code>const T&</code> are special & weird types. They cannot be rebound. They cannot be stored in containers. <code>sizeof(reference)</code> gives size of type instead of size of reference... They are not first-class citizens. That's why I show 
   <code>std::reference_wrapper</code> -- they behave much closer to Rust's references.

   C++ references are not null and must not be misaligned (that's undefined behaviour). But they can point to unitialized memory.
    </td>
    <td>
    Taking references in Rust requires explicit usage of <code>&</code> or <code>&mut</code> operators -- to specify exaclty, which type of reference we want.
    <br>
    References in Rust are normal values. We can store them in containers, pass them around, mutate (by rebinding) and so on.
     <br>
    Similarly to C++, Rust references must not be null and cannot be misaligned. But Rust references never should point to unitialized memory. It's undefined behaviour.
   </td>
</tr>


<tr>
    <td rowspan="2">Raw pointers</td>
    <td>

```cpp
using PointerT = int*;
using ConstPointerT = const int*;

int x = 42;
int* mut_ptr = &x;
*mut_ptr = 43;
const int* const_ptr = &x;

int* null = nullptr;

```   
   </td>
    <td>

```rust  
type PointerT = *mut i32;
type ConstPointerT = *const i32;

let mut x = 42;
let mut_ptr = &raw mut x;
unsafe { *mut_ptr = 43; } 
let const_ptr = &raw const num;

let null: *mut i32 = std::ptr::null_mut();
let null_const: *const i32 = std::ptr::null();
```   
   </td>
  </tr>
  <tr>
    <td>
    Raw pointers in C++ and Rust are similar and serve the same purpose. However, in Rust, dereferencing raw pointer requires an <code>unsafe</code> block, as it can lead to undefined behavior if not handled correctly. 
    </td>
    <td>
    Raw pointers in Rust actually can be created implicitly from references, if the pointer type is specified <code> let ptr : *const T = &value </code>. But the recomended way is to use <code>&raw const</code> or <code>&raw mut</code>  constructs, which makes your intention clear. This is also the only correct solution while dealing with pointers to unitialized fields.
   </td>
</tr>



</table>