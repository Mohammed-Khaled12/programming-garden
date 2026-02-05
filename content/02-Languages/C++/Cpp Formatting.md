
> [!ABSTRACT] Why Formatting?
> Formatting is about making your output readable and aligned (e.g., printing tables, fixing decimal points).
> We have two ways:
> 1. **C-Style:** `printf` (Fast & Compact).
> 2. **C++ Style:** `setw`, `setprecision` (Safe & Modern).

Tags: #cpp #formatting #output #syntax

---

# printf

## printf VS cout

- **الأمان (Type Safety):** `cout` أذكى. لو حاولت تطبع `double` ونسيت وحطيت `%d` في `printf`، البرنامج هيطلع نتيجه غلط أو يضرب (Undefined Behavior). الـ `cout` بتعرف نوع المتغير لوحدها.
    
- **السرعة (Performance):** دالة `printf` أسرع بكتير من `cout` في النسخ القديمة.

| Feature           | `printf`                 | `cout`                       |
| ----------------- | ------------------------ | ---------------------------- |
| **Type Safety**   | ❌ Low (Risk of mismatch) | ✅ High (Auto-detection)      |
| **Performance**   | ⚡ Very Fast              | 🐢 Slower (unless optimized) |
| **Format String** | Compact (`%d`, `%.2f`)   | Verbose (`<< setw <<`)       |

## Integer Format

بتستخدم `%d` او `%i` مع الاعداد الصحيحه في الطباعه
```c++
int Page = 1, TotalPages = 10;
// print string and int variable
printf("The page number = %d \n", Page); // 1
printf("You are in Page %d of %d \n", Page, TotalPages); // 1 of 10
//Width specification
printf("The page number = %0*d \n", 2, Page); // 01
printf("The page number = %0*d \n", 3, Page); // 001
printf("The page number = %0*d \n", 4 , Page);// 0001
printf("The page number = %0*d \n", 5, Page); // 00001

int Number1 = 20, Number2 = 30;
printf("The Result of %d + %d = %d \n", Number1, Number2,
Number1+ Number2); // 20 + 30 = 50
```
`%0*d` --> حجز عدد من الخانات و تعويض عن الخانات الفاضيه باصفار

## Float Format

- `%f` → float / double
	
- `%.* f` → Precision specification , Dynamic Precision
    
- `%.2f` → يطبع رقمين بعد العلامة Hardcoded Precision

```cpp
double pi = 3.14159265;
printf("Precision specification of %.*f\n", 1, pi); // 3.1      
printf("pi = %f\n", pi);     // 3.14159265
printf("pi = %.2f\n", pi);   // 3.14
printf("pi = %.4f\n", pi);   // 3.1416 خلي بالك انه بيقرب
```

## String and Character Format

هنا بنتعامل مع ال Array of Character بس 
- `%s` -> للجمله
-  `%*c` -> فراغات قبل الحرف
```c++
char Name[] = "Mohammed Khaled";
char SchoolName[] = "HNU";
// print string and String
printf("Dear %s, How are you?\n\n", Name); // Mohammed Khaled
printf("Welcome to %s School!\n\n", SchoolName); // HNU
char c = 'S';
printf("Setting the width of c :%*c \n", 1, c);// S
printf("Setting the width of c :%*c \n", 2, c);//  S
printf("Setting the width of c :%*c \n", 3, c);//   S
printf("Setting the width of c :%*c \n", 4, c);//    S
printf("Setting the width of c :%*c \n", 5, c);//     S
```

# Setw Manipulator

دي الطريقة الـ "OOP" أكتر، وبتشتغل مع `cout`. كلمة `setw` اختصار لـ **Set Width** (تحديد العرض).

**عشان تستخدمها لازم تضم المكتبة دي:**

```c++
#include <iomanip>
```

**إزاي بتشتغل؟** بتحدد مسافة ثابتة للكلمة اللي جاية بعدها، ولو الكلمة صغيرة، بتسيب مسافة فاضية. دي ممتازة لعمل الجداول. "بيخليك تعمل **مسافات/محاذاة** للطباعة زي الجداول."

`setw(n)` -- > ممكن نقول بيعمل عرض ثابت لكل عمود او انه بيديني مساحه للكاتبه بقد الرقم اللي جواه و بيبدا كتابه من اليمين للشمال

```c++
#include <iostream>
#include <iomanip>
using namespace std;

int main()
{
    cout << "---------|--------------------------------|---------|\n";
    cout << " Code    | Name                           | Mark    |\n";
    cout << "---------|--------------------------------|---------|\n";

    cout << setw(9) << "C101" << "|"
        << setw(32) << "Introduction to Programming 1" << "|"
        << setw(9) << 95 << "|\n";

    cout << setw(9) << "C102" << "|"
        << setw(32) << "Computer Hardware" << "|"
        << setw(9) << 88 << "|\n";

    cout << setw(9) << "C1035243" << "|"
        << setw(32) << "Network" << "|"
        << setw(9) << 75 << "|\n";

    cout << "---------|--------------------------------|---------|\n";

    return 0;
}
```

![[Pasted image 20260124073247.png]]

# Cheat Sheet

| Feature               | 🟢 **C-Style** (`printf`)                     | 🔵 **C++ Style** (`cout` + `<iomanip>`)                                 | 💡 **Notes & Tricks**                                                         |
| :-------------------- | :-------------------------------------------- | :---------------------------------------------------------------------- | :---------------------------------------------------------------------------- |
| **Library**           | `#include <cstdio>`                           | `#include <iostream>` <br> `#include <iomanip>`                         | `iomanip` is mandatory for `setw`.                                            |
| **Integer**           | `%d` or `%i`                                  | `cout << x;`                                                            |                                                                               |
| **Width (Space)**     | `printf("%5d", x);` <br> *Output: `    x`*    | `cout << setw(5) << x;`                                                 | `setw` is **NOT Sticky** (resets after one use).                              |
| **Zero Padding**      | `printf("%05d", x);` <br> *Output: `0000x`*   | `cout << setfill('0') << setw(5) << x;`                                 | `setfill` is **Sticky**.                                                      |
| **Float Precision**   | `printf("%.2f", x);` <br> *Output: `3.14`*    | `cout << fixed << setprecision(2) << x;`                                | `fixed` prevents scientific notation.                                         |
| **Dynamic Precision** | `printf("%.*f", n, x);` <br> *`n` = decimals* | `cout << fixed << setprecision(n) << x;`                                | `printf` uses `*` to take arg.                                                |
| **Alignment**         | `%-10d` (Left) <br> `%10d` (Right - Default)  | `cout << left << setw(10) << x;` <br> `cout << right << setw(10) << x;` | Alignment is **Sticky**.                                                      |
| **String**            | `printf("%s", s.c_str());`                    | `cout << s;`                                                            | ⚠️ **Danger:** `printf` crashes with `std::string` unless you use `.c_str()`. |
| **Table Line**        | (Manual Loop)                                 | `cout << setfill('-') << setw(20) << "";`                               | Quick way to print separator lines.                                           |
| **Char**              | `%c`                                          | `cout << ch;`                                                           |                                                                               |

