
# Mina Language
A simple, C-like programming language taking inspiration from other languages such as Go and Rust.


**Table of Contents**
- [Variables and Data Types](#variables-and-data-types)
  - [Scalar Types](#scalar-types)
  - [Arrays](#arrays)
  - [Pointers](#pointers)
    - [Strings](#strings)
  - [Attribute Handles](#attribute-handles)


## Variables and Data Types
Variables are declared the same way as in C: `type name = value;`    
There is no special keyword like 'var' or 'let' beacause what type a variable is should be in focus.

Variables are immutable by default.    
To make a variable mutable use the `mut` keyword: `i32 mut var = 42;`


### Scalar Types
| Size | Signed Integer | Unsigned Integer | Floating Point | Character |
| ------ | ------ | ------ | ------ | ------ |
| 8-bit  | `i8`   | `u8`   | `f8`   | `c8`   |
| 16-bit | `i16`  | `u16`  | `f16`  | `c16`  |
| 32-bit | `i32`  | `u32`  | `f32`  | `c32`  |
| 64-bit | `i64`  | `u64`  | `f64`  |
| 128-bit | `i128` | `u128` |
| Architecture | `int` | `uint` | `float`

Examples of declaring variables:

```mina
i32  integer  = 42;
f64  floating = 3.1415;
uint unsigned = 127;    // unsigned integer with the same size as the architecture
```

```mina
c8  character = 'A';  // can hold all ascii characters
c32 character = 'あ'; // can hold any unicode charater
```

#### Boolean Type
The boolean type `bool` can only be the values `true` and `false`, which can also be defined using `0` and `1`

```mina
bool var_a = true; // 1
bool var_b = 0;    // false
```

### Arrays
Arrays are static, stack allocated collections of scalar variables
```mina
i16 array_a[]    = [10, 20, 30];  // lenght 3, all set
f64 array_b[5];                   // length 5, all 0
i32 array_c[10]  = [1, 2];        // length 10, first two set, rest 0
u8  array_d[128] = [127...];      // length 128, set all to 127

c8  str_array[1, 10];               // initial length of 1, but has allocated and can be expanded to 10
c8  str_array[]  = "Hello!";
```
You can get information about an array using the [attribute handles](#attribute-handles)
```mina
array_a:len     // length: 3 entries
array_a:size    // total size: 6 bytes
array_a[0]:size // enty size: 2 bytes
```


### Pointers
Pointers are defined as a special type `ptr` pointing to some memory. It also contains information about the data it points to like type, length, capacity, etc..

```mina
ptr pointer_a = &interger_var;          // pointer to to some integer value, taking the type
ptr pointer_b(i16, 10);                 // allocates memory at runtime of size 10 * `i16`
ptr pointer_c(f32, 1, 20);              // allocates memory at runtime of size 20 * `f32`, with current length set to 1
ptr pointer_d(u8, size_var) = [255...]; // allocates memory at runtime of size 'size_var' and sets it to 255

ptr string(c16) = "こんにちは！";          // allocates memory at runtime for a c16 string of the same size as the literal

// unsafe
unsafe ptr pointer_x      = null;                 // void pointer to null
unsafe ptr pointer_y(i32) = null;                 // i32 pointer to null
unsafe ptr pointer_z(my_struct, 1024) = 0xFE78A2; // points to memory location specified and maps it as an array of type 'my_struct' with a length of 1024
```
You can get information about a pointer using the [attribute handles](#attribute-handles)


#### Strings
The string type `str` is an alias for `ptr(c32)`
```mina
str string = "Hello";
string.append(", World!");
```


### Attribute Handles
You can get information about any variable by using the attribute handles. These are in most cases calculated at compile time, except for dynamic sizes of heap allocated variables.

| Handle | Description |
| ------ | ------ |
| `:type` | Type of variable or function. `i32`, `f16`, `ptr-c8`, `ptr-void`, etc.. |
| `:name` | Returns name of variable, function or type |
| `:size` | Size of variable, whole collection or type in bytes |
| `:len` | Length (number of entries) of collection (1 for scalar variables) |
| `:cap` | Capacity (number of entries) of collection (1 for scalar variables) |
| `:align` | Alignment of struct members in bytes (usually size of biggest type) |
| `:offset` | Offset of member within struct in bytes+bits |

```mina
variable:type  // type of "variable"
func:name      // name of "func"
array:size     // size in bytes of "array" (both stack and heap)
array:len      // number of entries in "array"
string:cap     // number of preallocated entries in "array"
vec3:align     // alignment of members in struct type "vec3" in bytes
vec4.z:offset  // offset of member "z" within struct type "vec4" in bytes (+bits if using bit fields)
```
