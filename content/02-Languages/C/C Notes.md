
> [!info] C Bridge — Scope & Prerequisites
> **Prep notes before OSTEP.** This is not a ground-up C tutorial—it is a fast, systems-oriented bridge for someone who already knows programming fundamentals and wants to focus strictly on the low-level memory and compiler mechanics C exposes.
>
> 

## 1. Basics

- `#include <stdio.h>` brings in input/output functions.
- `printf("Hello C!");` prints text to stdout.

## 2. Strings in C

Most modern languages have a built-in `string` type. C does not. A C string is just an **array of `char`** that ends with a null terminator.

```c
const char *msg_from_dax = "You still have 0 users";
```

- A `char` occupies **1 byte**, enough for one character, not a sequence of text.
- To represent text, C places characters **contiguously in memory** and you hold the **address of the first character** in a pointer (`char *`).
- C arrays do not store their own length, so the compiler appends a hidden **null terminator (`'\0'`)** after the last character. Functions like `printf` and `strlen` read until they hit it.

```text
Address:  0x402000  0x402001  0x402002  0x402003  0x402004  0x402005
Value:       'H'       'e'       'l'       'l'       'o'      '\0'
```

**Pointer arithmetic stride:** the pointer's type decides how far `ptr + 1` moves. For `char *` it advances **1 byte**; for `int *` it advances **4 bytes**.

**Gotcha:** `strlen("Hello")` is `5` (does not count `'\0'`), but `sizeof("Hello")` is `6` (does count it).

### Where the literal lives

When you write `const char *msg = "Hello";`, the compiler places the bytes of `"Hello"` in a read-only section of the binary called **`.rodata`**. At load time the OS maps that section into the process's address space with read-only page permissions. The variable `msg` itself is just an 8-byte pointer (on 64-bit) holding that address.

Prefer `const char *` for literals. The literal's type in C is technically `char[]`, so the compiler won't force `const`, but writing to it is undefined behavior (usually a segfault). `const` turns that runtime crash into a compile-time error.

## 3. `char *` vs `char[]` vs heap strings

|   |   |   |   |   |
|---|---|---|---|---|
|Syntax|Where it lives|Page permissions|Mutability|Lifetime|
|`const char *s = "Hi";`|Pointer (8B) on the **stack**; literal bytes in **`.rodata`**|Read-only|**Immutable.** Writing is undefined behavior, typically `SIGSEGV`.|Whole program|
|`char s[] = "Hi";` (inside a function)|The full array (3B) is **copied onto the stack**|Read-write|**Mutable.** Safe to modify in place.|The function's stack frame|
|`char *s = strdup("Hi");`|Pointer (8B) on the stack; bytes on the **heap**|Read-write|**Mutable.** Dynamic size.|Until `free(s)`|

Notes:

- `strdup` is POSIX (standardized in C23), not classic ISO C. It is `malloc` plus copy.
- A `char s[] = "Hi";` declared at file scope or as `static` lives in `.data`, not on the stack.

## 4. What happens under the hood

The steps below describe typical unoptimized x86-64 output (for example `gcc -O0`). With optimizations the compiler may keep the pointer in a register and skip the stack slot entirely.

### 4.1 `const char *msg = "Hello";`

1. **Compile time (data):** the 6 bytes (`'H' 'e' 'l' 'l' 'o' '\0'`) go into `.rodata`.
2. **Compile time (code):** the compiler emits one address-loading instruction (for example `lea` on x86-64) that references the literal. No bytes are copied.
3. **Load time:** the OS loader maps `.rodata` into virtual memory and marks its pages read-only.
4. **Runtime:** the function's prologue reserves stack space (it lowers `rsp`), and the `.rodata` address is stored into the 8-byte slot for `msg`.

### 4.2 `char msg[] = "Hello";`

1. **Compile time (data):** the template bytes are stored in `.rodata`, or for short strings encoded as immediate constants inside instructions.
2. **Compile time (code):** the compiler emits copy instructions (`mov` with immediates, or a `memcpy`) that populate the stack at runtime.
3. **Load time:** `.rodata` is mapped read-only; the stack is read-write.
4. **Runtime:** the stack frame reserves space for the whole array (6 bytes, with alignment) and every character plus `'\0'` is copied in. There is no separate pointer variable. `msg` is effectively just a stack offset known at compile time.

## 5. Traversal and array-to-pointer decay

- **How `printf("%s", msg)` reads a string:** C passes arguments **by value**, so `printf` gets a _copy_ of the pointer, aimed at index `0`. It loops, reading `*ptr` and doing `ptr++` (1 byte per step) until `*ptr == '\0'`. The caller's own pointer is untouched.
- **Array-to-pointer decay:** when you pass a `char[]` to a function, the array name decays into a `char *` pointing at its first element (`&msg[0]`). Inside the callee, the length information is gone: `sizeof(arg)` gives `8` (the pointer size on 64-bit), not the array length. Decay does not happen under `sizeof` or unary `&`, which is why `sizeof(msg)` works in the function that declared the array.

## 6. Which one to use

1. **`const char *`: static read-only text.** Use for literals, format strings, and constant error messages. There is no copying, and `const` makes the compiler catch accidental writes.
2. **`char buffer[N]`: temporary local mutation.** Use on the stack when reading lines (`fgets`) or tokenizing (`strsep`) inside one function. **Never return a local `char[]`:** the stack frame is reclaimed on `return`, so the caller gets a **dangling pointer**.
3. **`malloc` / `calloc` / `strdup`: dynamic heap strings.** Use when the length is unknown at compile time, or when a function must build and return a string that outlives its own frame. Always check for `NULL` after allocating and pair every allocation with `free()`.

## 7. Format specifiers

We have to tell C _how_ each value should be printed using **format specifiers**.

| Specifier | Meaning                                       |
| --------- | --------------------------------------------- |
| `%d`      | decimal integer (`int`)                       |
| `%c`      | character                                     |
| `%f`      | floating-point number                         |
| `%s`      | string (`char *`)                             |
| `%zu`     | `size_t` (for example the result of `sizeof`) |
| `%p`      | pointer address                               |

```c
printf("Hello, %s. You're %d years old.\n", name, age);
```

Reference: [printf on cplusplus.com](https://cplusplus.com/reference/cstdio/printf/)

## 8. `f()` vs `f(void)`

```c
int get_integer(void);   // explicitly: takes NO arguments
int get_integer();       // unspecified arguments (before C23)
```

- **`int get_integer(void);`** is a strict contract: the function takes nothing. Calling `get_integer(5)` is a **compile-time error**.
- **`int get_integer();`** in classic C (C89, C99, C11, C17) does **not** mean "no arguments". It means "the number and types of arguments are unspecified, so don't check". This is a leftover from pre-prototype K&R C, kept for backward compatibility. A call like `get_integer(5)` compiles, and if the definition really takes no parameters, the behavior is undefined.
- **C23 changed this:** empty parentheses now mean `(void)`, like in C++. Don't rely on that yet.

Rule: always write `(void)` when you mean "no parameters".

## 9. `typedef`

`typedef` creates a **type alias**: a new name for an existing type. It does **not** create a new type in memory. It is handled by the compiler at compile time.

### 9.1 Why structs need it in C (unlike C++)

- **C++:** after `struct Coordinate { int x; int y; };`, the name `Coordinate` is a ready-to-use type: `Coordinate p1;`.
- **C:** struct names live in a separate **tag namespace**, so you must keep writing `struct` every time:

```c
struct Coordinate p1;
void move(struct Coordinate *p);
```

`typedef` removes that repetition.

### 9.2 Three common forms

**Form 1: define the struct, then alias it.**

```c
struct Coordinate {
    int x;
    int y;
};

typedef struct Coordinate coordinate_t;

coordinate_t point = {10, 20};
```

**Form 2: anonymous struct plus alias in one statement** (common in Boot.dev).

```c
typedef struct {
    int x;
    int y;
} coordinate_t;
```

The struct has no tag of its own; `coordinate_t` is its only name.

**Form 3: named struct plus alias** (needed for self-referential types: linked lists, trees, and a lot of `xv6` code).

```c
typedef struct Node {
    int data;
    struct Node *next;   // must say "struct Node": node_t isn't defined yet
} node_t;
```

Form 2 can't do this because the alias `node_t` only exists after the closing `}`, so the struct can't refer to itself through it.

### 9.3 Other places you'll see it (OSTEP and `xv6`)

1. **Shorter or portable type names.** `xv6`'s `types.h` is mostly `typedef` lines:
    
    ```c
    typedef unsigned int   uint;
    typedef unsigned short ushort;
    typedef unsigned char  uchar;
    ```
    
      
    Kernel code avoids typing `unsigned int` hundreds of times, and if a type's size must change on another architecture, only one line changes. Standard types like `size_t` work the same way: on x86-64 Linux it is `unsigned long`, but that is platform-specific.
2. **Taming function pointer syntax.** Raw function pointer declarations are hard to read, and you'll need them for signals and threads:
    
    ```c
    typedef void (*sighandler_t)(int);
    ```
    

### 9.4 `typedef` vs `#define`

`#define` is blind text substitution done by the **preprocessor**. `typedef` is understood by the **compiler**. This matters with pointers:

```c
#define INT_PTR int*
INT_PTR a, b;
// expands to: int* a, b;
// a is a pointer, b is a plain int!

typedef int* int_ptr_t;
int_ptr_t a, b;
// both a and b are int*
```

**Convention:** `typedef` names often end in `_t` (`coordinate_t`, `node_t`) to mark them as types. Note that POSIX reserves the `_t` suffix, which is fine in your own program but worth avoiding in libraries.

## 10. Struct memory layout and padding

Structs are stored **contiguously**, one field after another, in declaration order.

```c
typedef struct Coordinate {
    int x;
    int y;
    int z;
} coordinate_t;
```

Assuming `int` is 4 bytes, the layout of `coordinate_t` is 12 bytes with no gaps:

![[Pasted image 20261116114707.png]]

### 10.1 Alignment and padding

Now a struct with mixed sizes:

```c
typedef struct {
    char   first_initial;   // 1 byte
    int    age;             // 4 bytes
    double height;          // 8 bytes
} human_t;
```

The fields add up to `1 + 4 + 8 = 13` bytes, but `sizeof(human_t)` is **16**. Here is why.

**The mental model.** Picture memory as shelves. The CPU wants to fetch a value in one grab, so a value should not straddle a shelf boundary. If it does, the CPU needs extra memory accesses (and on some architectures a misaligned access faults outright). Compilers prevent this with **alignment**:

- Each field must start at an offset that is a **multiple of its own alignment**. On x86-64, a primitive's alignment equals its size: `char` 1, `int` 4, `double` 8, pointers 8.
- The compiler inserts unused bytes, **padding**, to satisfy that rule.
- The whole struct's alignment is the largest field alignment, and `sizeof` is rounded up to a multiple of it (**trailing padding**).

Laying out `human_t` step by step:

|   |   |   |   |
|---|---|---|---|
|Field|Offset|Size|Why|
|`first_initial`|0|1|starts at 0|
|_padding_|1–3|3|`age` needs a multiple of 4|
|`age`|4|4|splitting it across two rows would need two memory reads|
|`height`|8|8|already a multiple of 8, so no padding|
|**Total**||**16**|13 bytes of data + 3 bytes of padding|

Shelf view (4-byte rows):

```text
Row 1: [ first_initial (1B) |      padding (3B)      ]
Row 2: [                  age (4B)                  ]
Row 3: [            height, first half (4B)            ]
Row 4: [            height, second half (4B)           ]
```

![[Pasted image 20261116115237.png]]

### 10.2 Field order matters

There are many compiler-specific rules and heuristics, but the oversimplified version is:

1. Fields are laid out contiguously, in the order you declare them.
2. Struct size depends on that order, because padding depends on it.

C aims to give tight control over memory, so controlling layout is a feature, not a bug. What you _think_ the computer does can differ from what it actually does (compilers, hardware, optimizations, and plain mistakes), but C lets you dig in and check for your specific compiler and architecture.

**Rule of thumb:** order fields from **largest to smallest** to minimize padding.

```c
typedef struct { char a; double b; char c; } bad_t;   // 24 bytes
// a@0, padding 1-7, b@8, c@16, trailing padding 17-23

typedef struct { double b; char a; char c; } good_t;  // 16 bytes
// b@0, a@8, c@9, trailing padding 10-15
```

Two caveats:

- Reordering doesn't always help. `human_t` as `{ double, int, char }` still comes to 16 bytes, because 13 bytes of data are rounded up by trailing padding to a multiple of 8.
- Trailing padding matters for **arrays of structs**: each element must start correctly aligned, so the padding is paid per element.

### 10.3 Verify it yourself

Don't trust mental math; ask the compiler:

```c
#include <stdio.h>
#include <stddef.h>   // offsetof

printf("size   = %zu\n", sizeof(human_t));
printf("age @ %zu\n",   offsetof(human_t, age));
printf("height @ %zu\n", offsetof(human_t, height));
```

To remove padding deliberately (for example for wire formats or hardware registers), compilers offer `__attribute__((packed))` or `#pragma pack`, at the cost of misaligned access.