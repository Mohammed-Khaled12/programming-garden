# 📘 C++ Hand-Written Legacy Notes
> **Description:** My original comprehensive guide to C++ fundamentals.
> **Status:** Reference Material (Archive).

## 🔍 Topics Index (Search Keywords)

### 🧱 Core Basics
- **Syntax:** `#include`, Comments (`//`), `using namespace std` [Page 1].
- **Variables:** `int`, `float`, `double`, `char`, `bool`, `string` [Page 3-4].
- **Modifiers:** `signed` vs `unsigned`, `short` vs `long` (Memory ranges) [Page 6-7].
- **Casting:** Implicit vs Explicit conversion, ASCII codes [Page 10-12].

### ⚙️ Control Flow & Logic
- **Operators:** Arithmetic, Relational, Logical (`&&`, `||`), Ternary Operator [Page 13, 37].
- **Conditions:** `if`, `else if`, `switch case` [Page 36, 38].
- **Loops:** `for`, `while`, `do-while`, `break`, `continue` [Page 40-43].

### 📦 Data Structures
- **Arrays:** 1D & 2D Arrays, `std::array` class [Page 30, 32].
- **Strings:** C-Style Strings vs `std::string`, `getline`, concatenation [Page 19, 34].
- **Structs:** User-defined data types [Page 16].
- **Enums:** Enumerations for constant values [Page 17].

### 🔧 Functions
- **Basics:** Parameters, Arguments, `void`, Return types [Page 22].
- **Advanced:** **Pass by Value** vs **Pass by Reference** (`&`) [Page 28-29].
- **Overloading:** Same function name, different parameters [Page 27].

---
**Tags:** #cpp #fundamentals #syntax #memory #legacy #handwritten 



![[C++ agenda.pdf]]

# Ranged Loops
## يعني ايه **Ranged-based for loop** ؟
هي طريقه اسهل للف علي عناصر Array , vector or str
ببساطة: هي طريقة بتقول للكمبيوتر: **"لكل عنصر جوه الصندوق ده، نفذ الكود ده"**، من غير ما تشغل بالك بـ `i` ولا حجم المصفوفة ولا إنك تخرج بره الحدود (Out of bounds).

## الشكل العام
```cpp
for (type element : container) {
    // use element
}
```

```cpp
int arr[] = {1, 2, 3, 4};

for (int x : arr) {
    cout << x << " ";
}
// 1 2 3 4 
```

## اصلها

الكومبايلر بيحولها لكده:
```cpp
for (int i = 0; i < size; i++) {
    int x = arr[i];
}
```

## خطا شائع Copy vs Reference

```cpp
#include<iostream>
using namespace std;
int main()
{
  int arr1[] = {1,2,3,4,5};
  
  for(int x : arr1)
  {
    x += 2;

    cout << x ;
  }
} 
```

في البرنامج اللي فات x نسخه كوبي انا عايز اعدل علي الاراي نفسه فهستخدم الريفرينس

```cpp
#include<iostream>
using namespace std;
int main()
{
  int arr1[] = {1,2,3,4,5};
  
  for(int &x : arr1)
  {
    x += 2;

    cout << x ;
  }
} 
```

# Bitwise Operator

## And &
الاول بتاخد الرقم تحوله باينري بعد كده بتعمل اند عاديه علي كل بت في الباينري 

![[Pasted image 20260117184422.png]]

![[Pasted image 20260117184633.png]]

## OR |
الاول بتاخد الرقم تحوله باينري بعد كده بتعمل اور عاديه علي كل بت في الباينري 

![[Pasted image 20260117205517.png]]

![[Pasted image 20260117205628.png]]

# More About Variables

## Static Variable

A `static` variable in C++ is a variable that retains its value between function calls and persists for the entire lifetime of the program, rather than being destroyed when the function or scope ends.

When you declare a variable as `static` inside a function, it is initialized only once—the first time the program execution passes through that declaration. It is **not** destroyed when the function returns. Instead, it stays in memory, keeping its value for the next time the function is called.

```cpp
#include <iostream>

void counter() {
    // Initialized only once
    static int count = 0; 
    count++;
    std::cout << "Function called " << count << " times." << std::endl;
}

int main() {
    counter(); // Output: Function called 1 times.
    counter(); // Output: Function called 2 times.
    counter(); // Output: Function called 3 times.
    return 0;
}
```

## Register Variable

كلمة `register` في لغات  C و C++ كانت تستخدم لتوجيه الكومبيلر علشان يخزن متغير معين في ال CPU Registers 
**ليه؟**
- علشان الوصول لل Registers اسرع بكتير من الوصول للرامات

**حالياً، لا ينصح باستخدامها، بل إنها ألغيت فعلياً.**

- **منذ C++11:** تم اعتبارها "Deprecated" (أي مهملة وغير مفضلة).
    
- **منذ C++17:** تم إزالة تأثيرها تماماً. المترجمات الحديثة (Compilers) ذكية جداً وتقوم بعملية "Optimization" تلقائياً؛ فهي تعرف بنفسها أي المتغيرات يجب وضعها في الـ Register وأيها في الـ RAM أفضل مما يحدده المبرمج. استخدامك للكلمة الآن لا يغير شيئاً.