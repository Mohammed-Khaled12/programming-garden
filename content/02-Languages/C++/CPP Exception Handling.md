
# 🚨 C++ Exception Handling

> [!ABSTRACT] What is an Exception?
> An **Exception** is a problem that arises during the execution of a program (Runtime Error).
> * **Handling** allows the program to continue running or shut down gracefully instead of crashing.
> * **Keywords:** `try`, `catch`, `throw`.

**Header Required:** `#include <stdexcept>` (for standard exceptions) or `<exception>`

Tags: #cpp #error-handling #reliability

---

## 1️⃣ The Basic Structure (Try-Catch)

```cpp
try {
    // 1. Code that might cause an error
    int age = -5;
    if (age < 0) {
        throw "Age cannot be negative!"; // 2. Throw an exception
    }
}
catch (const char* msg) {
    // 3. Handle the error
    cout << "Error: " << msg << endl;
}
````

## 2️⃣ Multiple Catches

You can catch specific types of errors (int, string, or objects).

```C++
try {
    // ... code ...
}
catch (int e) { ... }      // Catches integers
catch (const char* e) { ... } // Catches strings
catch (...) {              // Catches ANY exception (The Safety Net)
    cout << "Unknown Error occurred!";
}
```

## 3️⃣ Standard Exceptions (`std::exception`)

C++ provides a hierarchy of built-in exceptions (like `out_of_range` for Vectors).

```C++
#include <vector>
#include <stdexcept> // Important!

try {
    vector<int> v(5);
    v.at(10) = 100; // Throws out_of_range
}
catch (const exception& e) {
    cout << "Standard Error: " << e.what() << endl;
}
```

---

## 🔗 Relations

- Used heavily in: [[Input-Validation-Strategy]] (Throwing errors on bad input).
    
- Vital for: [[CPP Dynamic Memory]] (Handling `bad_alloc` if RAM is full).
    



### 🧠 ربط النقط (Connecting the Dots):
أول ما تخلص النوت دي، روح لنوت **`Input-Validation-Strategy`** وزود فيها ملحوظة:
`💡 Advanced Tip: Instead of just printing errors, we can [[CPP-Exception-Handling|Throw Exceptions]] to handle them logically.`

كده أنت بتربط "طريقة التفكير" بـ "أداة التنفيذ". 🚀
```