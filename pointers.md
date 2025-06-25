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
   <br>
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
    <td rowspan="2">References and temporaries</td>
    <td>

```cpp
#include <format>

const auto x = 42;
const std::string& s_ref = std::format("Hello, {}!", x);

// compile error: cannot bind non-const lvalue reference to temporary
std::string& mut_ref = std::format("Hello, {}!", x);

```   
   </td>
    <td>

```rust  
let x = 42;
let s_ref: &String = &format!("Hello, {}!", x);
// this works:
let mut_ref: &mut String = &mut format!("Hello, {}!", x);

```   
   </td>
  </tr>
  <tr>
    <td>
   Built-in C++ const references can bind to rvalues and extend a lifetime of temporary objects to the lifetime of the reference.

   This doesn't work with <code>std::referece_wrapper</code>!
    </td>
    <td>
    Same works in Rust. 
    As known, C++ has extra rvalue references, but Rust doesn't need such a concept. Thus I scope this example to const references only.
    <br>
    Also, in Rust, temporaries can be assigned to mutable references.
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
    Raw pointers in Rust actually can be created implicitly from references, if the pointer type is specified <code> let ptr : *const T = &value </code>. But the recomended way is to use <code>&raw const</code> or <code>&raw mut</code>  constructs, which make your intention clear. This is also the only correct solution to deal with pointers to unitialized fields.
   </td>
</tr>





<tr>
    <td rowspan="2">Nullable references</td>
    <td>

```cpp
// Valid in C++26!
#include <optional>

int x = 42;
std::optional<int&> opt_ref = std::nullopt;
opt_ref = x; // rebind to x

sizeof(opt_ref) == sizeof(int*); // true


if (opt_ref) { 
    (*opt_ref) = 43; // dereference and mutate
}

if (opt_ref.has_value) { 
    opt_ref.value() = 43; // dereference and mutate
}
```   
   </td>
    <td>

```rust  
let mut x = 42;
let mut opt_ref: Option<&mut i32> = None;
opt_ref = Some(&mut x); // rebind to x

size_of::<Option<&mut i32>>() == size_of::<*mut i32>(); // true

if let Some(r) = opt_ref {
    *r = 43; // dereference and mutate
}
```

   </td>
  </tr>
  <tr>
    <td>
    C++26 extends <code>std::optional</code> to support references. It allows us to create relatively safe nullable references -- that are like pointers, but with instrumentation to assert non-nullability at runtime.
    </td>
    <td>
    Rust <code>Option</code> can be used to create nullable references. Unlike C++ version, Rust's Option cannot be directly dereferenced without explicit check. It also has </code>Option::is_some()</code> and <code>Option::is_none()</code> methods to check for the current state of the option. And, if you are sure that the option is not null, you can use <code>Option::unwrap()</code>. But this is not recommended, as it can lead to runtime panics if the option is actually null. Pefer pattern matching or optional methods like <code>Option::map()</code> or <code>Option::and_then()</code>
    <br>
    <br>
    Take into account that Rust's <code>Option</code> is a generic type, so it can be used with any type, not just references. And it has built-in support for niche optimization. So, if there are holes -- invalid representations -- for the type (like 0 value for references), Rust compiler will automatically try to use this hole to represent the <code>None</code> state, saving some memory on the size of <code>Option<T></code>.
   </td>
</tr>



<tr>
    <td rowspan="2">Unique Pointer</td>
    <td>

```cpp
#include <memory>

std::unique_ptr<int> uptr = std::make_unique<int>(42);
std::unique_ptr<int> uptr2 = std::move(uptr); // transfer ownership
assert(uptr == nullptr); // uptr is now null
*uptr2 = 43; // dereference and mutate


const std::unique_ptr<int> uptr3 = std::make_unique<int>(42);
*uptr3 = 43; // const is not propagated to the pointee!
```
   </td>
    <td>

```rust
let mut uptr = Some(Box::new(42)); // Option<Box<i32>>
let mut uptr2 = uptr.take(); // transfer ownership
assert!(uptr.is_none()); // uptr is now None
if let Some(ptr) = uptr2.as_mut() {
    *ptr = 43; // dereference and mutate
}

let uptr3 = Some(Box::new(42));
// no safe way to mutate the pointee!
uptr3.as_mut() // compilation error

```

   </td>
</tr>
<tr>
<td>
C++'s <code>std::unique_ptr</code> is a smart pointer that provides exclusive ownership of the object it points to. It cannot be copied, only moved.

unique_ptr can be null, and become null after move operations.

It can be constructed from raw pointers. It can be used with custom deleters to utilize RAII idiom without defining custom structs with destructors.
</td>
<td>
Rust <code>Box</code> is non-nullable smart pointer that owns the object it points to. It can be moved, but not copied. 

Memory for the object is allocated on the heap (by default GLOBAL allocator, actually). 

Also note that Rust's Box is constructed from some complete object, while C++'s make_unique calls the constructor directly in place of the newly allocated memory.

This may be critical for large objects or arrays of objects: in non-optimized Rust builds, the object will be, most likely, first constructed on the stack, and then moved to the heap.

Box can be parametrized with allocator, but such API is unstable and requires nightly version of rustc and its standard library.
</td>
</tr>


<tr>
    <td rowspan="2">Indirect - Unique Pointer</td>
    <td>

```cpp
// C++26!
#include <memory>

std::indirect<int> uptr {42};
std::indirect<int> uptr2 = std::move(uptr); // transfer ownership
assert(uptr.valueless_after_move()); // uptr is now moved-out
*uptr2 = 43; // dereference and mutate


const std::indirect<int> uptr3 {42};
*uptr3 = 43; // compilation error! const is propagated to the pointee!
```
   </td>
    <td>

```rust
let mut uptr = Box::new(42); // Box<i32>
let mut uptr2 = uptr; // transfer ownership
// any access to uptr is a compilation error!

*uptr2 = 43;

let uptr3 = Box::new(42);
*uptr3 = 43; // Compilation error

```

   </td>
</tr>

<tr>
<td>
C++26's <code>std::indirect</code> is the closest equivalent to the Rust.

It tries to be non-null (but failed due to C++ move semantics). It also propagates constness to the pointee.

Unlike unique_ptr, <code>std::indirect</code> cannot be parametrized by custom deleter. But it can be parametrized by allocator.

</td>

<td>
</td>

</tr>

<tr>
    <td rowspan="2"> Shared Pointers: Weak and Strong </td>
    <td>

```cpp

#include <memory>

std::shared_ptr<int> sptr = std::make_shared<int>(42);
std::shared_ptr<int> sptr2 = sptr; // copiable
std::shared_ptr<int> sptr3 = std::move(sptr); 
assert(sptr == nullptr); // nullable

*sptr2 = 43; // dereference and mutate


const std::shared_ptr<int> sptr3 = std::make_shared<int>(42);
*sptr3 = 43; // fine, const is not propagated to the pointee

// weak pointer, not associated with any shared pointer
std::weak_ptr<int> wptr {};

// weak pointer, associated to shared pointer
std::weak_ptr<int> wptr2 = sptr; 

// upgrade weak pointer to strong one
// may return null!
std::shared_ptr<int> sptr4 = wptr2.lock();

```
   </td>
    <td>

```rust
use std::sync::Arc; // Atomic Ref Counter
use std::sync::Weak;

let mut sptr = Some(Arc::new(42)); // Option<Arc<i32>>
let mut sptr2 = sptr.clone() // can be copied, explicitly
// Similar to Box, Arc is non nullable

// compilation error! Arc is a shared reference 
// cannot mutate over it!
*sptr2 = 43;

// Will make a copy, if there are more than 1 strong reference,
// or if there are weak references
*Arc::make_mut(&mut sptr) = 43;

// Returns None, if there are other strong references
// or weak references
if let Some(p) = Arc::get_mut(&mut sptr) {
    *p = 43;
}

// weak pointer, not associated with any shared pointer
let wptr = Weak::new();
// explicitly downgrade Arc to obtain weak version
let wptr2 = Arc::downgrade(&sptr2);
// upgrade weak pointer to strong version
// nullabitily encoded in type!
let sptr4: Option<Arc<_>> = wptr.upgrade();

```

   </td>
</tr>

<tr>
<td>

<code>std::shared_ptr</code> uses atomic reference counter for
to track references to the managed object. No other thread-safety
guarantees. You can mutate object via shared pointer (and cause data race if you don't have proper synchronization)
<br>
C++'s shared pointer, similarly to unique pointer, supports custom deleters

</td>

<td>
<code>Arc</code>, aka atomic ref counter, is similar to <code>shared_ptr</code>, but you cannot directly mutate the object behind it, if there are other pointer to the same object.

<code>Arc</code> similarly to <code>Box</code> is nonnullable. Wrap it with <code>Option</code> if needed
</td>

</tr>

</tr>
 <td> Shared Pointers: Non Atomic </td>
 <td> 
    N/A
 </td>
 <td>

 ```rust
   use std::rc::Rc;
   use std::rc::Weak;

   // Same as Arc, but without atomics. Cannot be shared or moved between mutliple threads
 ```
 </td>




<tr>
    <td rowspan="2">Copy on Write pointer</td>
    <td>

```cpp
#include <variant>
#include <functional>
#include <type_traits>

template <typename T>
requires std::copy_constructible_v<T>
struct CopyOnWrite : std::variant<T, std::reference_wrapper<const T>> {

    operator const T& () const {
        // returns reference, stored or to stored object
        ...
    }

    T& to_mut() {
        // if stores reference, copies the value inside
        // returns reference to the stored value
        ...
    }
};


```

   </td>

   <td>

```rust
   use std::borrow::Cow; // Clone on Write

   let value = String::from("Hello World");
   let mut ptr = Cow::Borrowed(&value); // takes reference
   ptr.to_mut().push("Rust");
   // on attempt to mutate the value, makes the copy 
   assert!(matches!(ptr, Cow::Owned(_)));
```
   </td>
</tr>


<tr>
<td>
C++ standard library doesn't have such smart pointer, but
it can be manually crafted on top of <code>std::variant</code>
Copy-on-write propery in C++ usually implemented inside 
concrete types (like <code>std::string</code> in pre C++11 standard library implemetations)

</td>

<td>

In Rust, It's a common practice to attach Copy-On-Write property via smart pointer-wrapper. Arc & Rc as it was shown above, also have Copy-On-Write support.
Keep in mind that <code>Cow</code> carries lifetime parameter to ensure validness of the reference

</td>

</tr>


<tr>
    <td rowspan="2"> References to arrays -- contigious ranges & spans</td>
    <td>

```cpp

#include <span>

uint8_t bytes[] = {1, 2, 3, 4, 5};
auto bytes_mut = std::span<uint8_t>(bytes, 5);
auto bytes_const = std::spawn<const uint8_t>(bytes, 5);

static_assert(sizeof(bytes_mut) == 2 * sizeof(uint8_t*));

```

   </td>

   <td>

```rust
  let bytes = [1,2,3,4,5];
  let bytes_mut : &mut [u8] = &mut bytes[..];
  let bytes_const : &[u8] = & bytes[..];
  
  std::mem::size_of::<&[u8]>() == 2 * std::mem::size_of::<*const u8>();
```
   </td>
</tr>


<tr>

<td>
</td>

<td>

References to slices are fat pointers: they carry length of the slice along with the pointer to its start.

<code>std::span<T></code> in C++ and <code>&mut [T]</code> in Rust are identical, except Rust's lifetime & borrowing rules  

</td>

</tr>



<tr>
    <td rowspan="2"> References to string slices </td>
    <td>

```cpp

#include <string_view>

std::u8string_view s1 = u8"Hello World";
// but almost always it's just
std::string_view s2 = "Hello World";

```

   </td>

   <td>

```rust
  let s1 : &str = "Hello World;
```
   </td>
</tr>


<tr>

<td>
In C++, to handle references to utf-8 strings, standard library provides <code>std::u8string_view</code>
But there are too many legacy code lines, and almost everyone uses just <code>std::string_view</code>
which can contain arbitray sequences.
</td>

<td>

Rust's <code>&str</code> must point to utf-8 encoded string. Similarly to slices, such references are pairs: pointer to the start of the string, and the length in bytes.

</td>

</tr>



</table>