# 🏗️ C++ Dynamic Memory Management

> [!ABSTRACT] Core Concept
> **Dynamic Memory** is the ability to reserve memory blocks manually during **Runtime** (while the app is running), not at Compile Time.
> * It utilizes the **Heap** segment of memory.
> * It gives you full control over the **Lifetime** of variables.
> * **Prerequisite:** [[CPP Pointers]]

**Tags:** #cpp #memory #heap #stack #performance

---
# 1. The Memory Layout (Stack vs. Heap)

Before coding, we must understand *where* our data lives. The RAM is divided into segments:

| Feature | Stack (The "Notebook") | Heap (The "Warehouse") |
| :--- | :--- | :--- |
| **Allocation** | **Static/Automatic:** Happen when function starts. | **Dynamic:** Happens when you use `new`. |
| **Deallocation** | **Automatic:** When variable goes out of scope `}`. | **Manual:** When you strictly say `delete`. |
| **Size Limit** | Very Small (Few MBs). Stack Overflow risk. | Huge (Limited by physical RAM). |
| **Speed** | **Extremely Fast** (LIFO structure). | **Slower** (Requires search for free block). |
| **Access** | Direct access via variable names. | Indirect access via **Pointers** only. |
| **Visibility** | Local to the function. | Global (Accessible anywhere via pointer). |

**Visualization:**
The Stack grows/shrinks automatically. The Heap is a free-floating pool of memory.

![[Pasted image 20260128231545.png]]

---

# 2. Allocation: The `new` Operator

كلمة `new` معناها: "يا كمبيوتر، روح دور في المخزن الكبير (Heap) على مكان فاضي واحجزهولي".

- بما إن المكان ده في الـ Heap (بعيد)، الكمبيوتر بيرجعلك **العنوان** بتاعه.
    
- عشان كده **لازم** تستقبله في **Pointer**.

The `new` operator is a request to the OS: *"Please find me a free block of X bytes in the Heap."*
### What `new` actually does:
1.  Searches the Heap for a free block of the required size.
2.  Reserves that block.
3.  **Constructs** the object (calls Constructor - *Advanced*).
4.  **Returns the memory address** of the first byte.

### Syntax
```cpp
// 1. Single Variable
int* ptr = new int;       // Allocates 4 bytes (garbage value)
int* ptr2 = new int(50);  // Allocates 4 bytes & initializes to 50

// 2. Dynamic Array (The Super Power)
int size;
cout << "Enter size: ";
cin >> size;

// Allows determining array size at RUNTIME!
int* arr = new int[size];
```

# 3. Deallocation: The `delete` Operator

Since the Heap is manual, C++ will **NEVER** clean it up for you. If you lose the pointer without deleting, the memory is lost forever (until reboot).
### What `delete` actually does:

1. Destructs the object (calls Destructor).
    
2. Marks the memory block as "Free" so the OS can reuse it.
    
3. **Crucial Note:** It does **NOT** clear the pointer itself. The pointer still holds the old address (Time bomb!).
### Syntax

```C++
// 1. Single Variable
delete ptr;  

// 2. Array
delete[] arr; // ⚠️ VERY IMPORTANT: Must use []
```

# 4. The Dark Side: Common Memory Errors 💀

Dealing with the Heap is like holding a double-edged sword. Here are the 3 ways you can kill your program.
### A. Memory Leak (The Silent Killer)

Allocating memory and forgetting to `delete` it.

- **Result:** RAM fills up slowly until the PC hangs or crashes.

```C++
void badFunction() {
    int* p = new int[1000];
    // Function ends, 'p' (stack variable) dies.
    // BUT the 1000 ints in Heap are still reserved!
    // No one can reach them now. -> LEAK.
}
```

### B. Dangling Pointer (The Liar)

A pointer that points to memory that has _already_ been deleted.

```C++
int* p = new int(10);
delete p; 
// p still stores the address (e.g., 0x500), but 0x500 is now free/garbage.

cout << *p; // ❌ CRASH or Undefined Behavior!
```

> **Fix:** Always set `ptr = nullptr` immediately after delete.

### C. Double Free (The Over-cleaner)

Trying to `delete` the same memory twice.

```C++
int* p = new int;
delete p;
delete p; // ❌ CRASH! Corrupts memory manager.
```