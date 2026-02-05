
> [!ABSTRACT] What is a Pointer?
> A variable that stores the **memory address** of another variable, not the value itself.
> * It allows you to directly manipulate memory.
> * Essential for dynamic memory allocation, arrays, and passing large objects efficiently.
> * **Key Operators:** `&` (Address-of) and `*` (Dereference).

**Header Required:** None (Built-in feature)

Tags: #cpp #memory #pointers #low-level

---
# Creating References

 هو انك تعمل اسم دلع او اسم تاني (Alias) لنفس المتغير 
 ```c++
 int x = 10;
int& a = x;  
// اكس و رف الاتنين واحد و الاتنين ليهم نفس العنوان علشان هما بقوا حاجه واحده
 ```

![[Pasted image 20260127142008.png]]

# Getting the Address of a Variable

**CONCEPT**: The address operator (&) returns the memory address of a variable.

Every variable is allocated a section of memory large enough to hold a value of the variable’s
data type. On a PC, for instance, it’s common for 1 byte to be allocated for chars,
2 bytes for shorts, 4 bytes for ints, longs, and floats, and 8 bytes for doubles.
Each byte of memory has a unique address. A variable’s address is the address of the first
byte allocated to that variable. Suppose the following variables are defined in a program:

char letter;
short number;
float amount;

Figure 9-1 illustrates how they might be arranged in memory and shows their addresses.

![[Pasted image 20260127141131.png]]

```c++
#include <iostream>
using namespace std;

int main()
{
    int x = 89;
    cout << &x << "\n"; // 000000E3C2AFF684

    return 0;
}
```

# What Is a Pointer ?

هو متغير عادي بيخزن عنوان متغير اخر 
## Syntax

`Type* name = &Var`

```c++
int *ptr;
```
هنا عملت بوينتر اسمه ptr بس لسه مفيهوش قيمه , خلي بالك النوع اللي جنبه ده مش نوع البوينتر دي معناها ان البوينتر يقدر يشيل عنوان متغير من نوع int 
*Remember, pointers only hold one kind of value: an address.*

## Using Of Dereference Operator "\*" and Adress Of Operator "&"

| Symbol  | Name            | Usage                                      | Code Example   | Concept                                 |
| :-----: | :-------------- | :----------------------------------------- | :------------- | :-------------------------------------- |
| **`&`** | **Address-of**  | Gets the memory address of a variable      | `int* p = &x;` | "Where is x located?"                   |
| **`*`** | **Declaration** | Declares a pointer variable (Part of type) | `int* p;`      | "I am a variable that holds an address" |
| **`*`** | **Dereference** | Accesses/Modifies the value at the address | `*p = 10;`     | "Go to address and do something"        |

```c++
#include <iostream>
using namespace std;
int main()
{
    int num = 100;
    int* ptr = &num;

    cout << "Value: " << num << "\n";   // 100
    cout << "Address: " << &num << "\n";// 00000080F3EFF594
    cout << "Value: " << *ptr << "\n";  // 100
    cout << "Address: " << ptr << "\n"; // 00000080F3EFF594

    cout << "=========================================\n";
    *ptr = 200;

    cout << "Value: " << num << "\n";   // 200
    cout << "Address: " << &num << "\n";// 00000080F3EFF594
    cout << "Value: " << *ptr << "\n";  // 200
    cout << "Address: " << ptr << "\n"; // 00000080F3EFF594

    return 0;
}
```


# Pointer VS Reference
## Pointer

**البوينتر** هو متغير عادي جدًا:

- ليه **مكان في الميموري**
    
- ليه **حجم**
    
- ليه **عنوان**
    
- وبيخزن **عنوان متغير تاني**
    
### مميزاته:

- تقدر تغيّره في **Runtime**
    
- ممكن يشاور على متغير، وبعدين يشاور على متغير تاني
    
- ممكن يكون:
    
    - `NULL` / `nullptr`
        
- ينفع تعمل عليه:
    
    - Arithmetic (زي `ptr++`)
        
- أساسي في:
    
    - Dynamic Memory
        
    - Arrays
        
    - Data Structures
        
    - Low-level programming
## Reference

**الريفيرنس** هو:

> Alias (اسم دلع) لمتغير موجود بالفعل

يعني:

- **مش متغير مستقل**
    
- ملوش Memory لوحده (في التفكير المنطقي)
    
- مجرد اسم تاني لنفس المتغير
    

### خصائصه:

- لازم يتعمله **Initialize وقت التعريف**
    
- **مينفعش** يتغير بعد كده
    
- **مينفعش** يكون `null`
    
- أي تعديل عليه = تعديل على المتغير الأصلي
    

---

## أهم الفروق الجوهرية

### 1️⃣ التغيير

- Pointer ➜ ممكن يتغير ويشاور على متغيرات مختلفة
    
- Reference ➜ ثابت على نفس المتغير طول عمره
    

### 2️⃣ Null

- Pointer ➜ ينفع `nullptr`
    
- Reference ➜ مستحيل
    

### 3️⃣ الاستخدام

- Pointer ➜ تحكم كامل في الميموري
    
- Reference ➜ أمان وسهولة
# The Relationship between Arrays and Pointers

*CONCEPT*: Array names can be used as constant pointers, and pointers can be used
as array names.
- اسم الاراي بيشيل عنوان اول عنصر فيه 
- 
![[Pasted image 20260127152140.png]]![[Pasted image 20260127152213.png]]![[Pasted image 20260127152302.png]]

```c++
#include <iostream>
using namespace std;

int main()
{
     short int nums[]{ 10, 20, 30, 40 };
     short int* ptr = &nums[0]; // We ALSO Can Use = nums Directly

    cout << "First Element\n\n";

    cout << "Value With Index: " << nums[0] << "\n";      // 10
    cout << "Value With Pointer: " << *ptr << "\n";       // 10
    cout << "Address With Index: " << &nums[0] << "\n";   // 000000071AEFFB48
    cout << "Address With Pointer: " << ptr << "\n";      // 000000071AEFFB48

    cout << "Second Element\n\n";

    cout << "Value With Index: " << nums[1] << "\n";      // 20
    cout << "Value With Pointer: " << *(ptr + 1) << "\n"; // 20
    cout << "Address With Index: " << &nums[1] << "\n";   // 000000071AEFFB4A
    cout << "Address With Pointer: " << ptr + 1 << "\n";  // 000000071AEFFB4A

    cout << "Third Element\n\n";

    cout << "Value With Index: " << nums[2] << "\n";      // 30
    cout << "Value With Pointer: " << *(ptr + 2) << "\n"; // 30
    cout << "Address With Index: " << &nums[2] << "\n";   // 000000071AEFFB4C
    cout << "Address With Pointer: " << ptr + 2 << "\n";  // 000000071AEFFB4C

    return 0;
}
```

**لاحظ:**
- بنتنقل بالبوينتر علي عناصر الاراي ازاي 
- ان العنوان في الميموري كل مره بيزيد 2 علشان احنا حاطيين الحجم شورت , خلي بالك ان الهيكسا اخرها 9 بعد كده حروف 
# Void , Wild and Null Pointer

## Null Pointer:

ده لما نعمل بوينتر مش بيشاور علي حاجه دلوقتي فبنديله قيمه فاضيه

- **زمان (C-Style):** كنا بنستخدم كلمه `NULL` (وده كان مجرد `#define NULL 0`) او رقم صفر يعني
	
- **دلوقتي (Modern C++):** بنستخدم `nullptr`.

**ليه `nullptr` أحسن؟** لأن `NULL` الكمبيوتر بيعتبرها رقم (Zero)، وممكن تلخبط الدوال (Functions)، لكن `nullptr` نوع خاص بيفهم الكمبيوتر "ده بوينتر فاضي مش رقم".

## Wild Pointer:

ده لما تعرف بوينتر و متديلوش قيمة ابتدائية (Initialization).

- **المشكلة:** هو بيشاور على عنوان "عشوائي" في الرامات (Garbage Address).
    
- **الخطر:** لو جيت تستخدمه `*ptr = 10;`، أنت وحظك! ممكن يكتب فوق ملفات الويندوز، ممكن يكتب فوق برنامج تاني، وغالباً البرنامج بيعمل **Crash**.
    
- **الحل:** دايماً صفر البوينتر (`= nullptr`) لو ممعكش عنوان حالياً.

## Void Pointer:

ده بوينتر "بتاع كله". نوعه `void*`.

- **الميزة:** يقدر يشيل عنوان **أي حاجة** (int, float, char, struct.. أي حاجة).
    
- **العيب (المهم جداً):** **مقدرش أعمله Dereference (`*ptr`) مباشرة.**
    
- **ليه؟** لأن الكمبيوتر مش عارف هو بيشاور على إيه؟ هل يقرأ 4 بايت (int) ولا 1 بايت (char)؟
    
- **الحل:** لازم أعمله **Casting** (تحويل) لنوع محدد قبل ما أستخدم القيمة اللي جواه.
	
- C-Style Casting --> `*(int *)ptr`
	
- Modern Casting -->`*static_cast<int *>(ptr)`
  



## Example

```c++
#include <iostream>
using namespace std;

int main()
{
  int *ptr1; // Wild Pointer
  int *ptr2 = NULL;
  int *ptr3 = nullptr;

  cout << ptr1 << "\n"; // Garbage Value
  cout << ptr2 << "\n"; // 0
  cout << ptr3 << "\n"; // 0

  int a = 100;
  void *ptr = &a;

  cout << ptr << "\n";
  // cout << *ptr ; --> Error

  // C-Style
  cout << *(int *)ptr << "\n"; // 100

  // Modern
  cout << *static_cast<int *>(ptr) << "\n"; // 100

  return 0;
}
```


# Pointers and Functions

***CONCEPT***: A pointer can be used as a function parameter. It gives the function
access to the original argument, much like a reference parameter does.
## Pass By Pointer

اننا نبعت للداله عنوان المتغير علشان لما الداله تغير فيه يتغير في كل مكان 

```c++
#include <iostream>
using namespace std;

// 1. الدالة بتستقبل "عنوان" (Pointer)
void promote(int* salaryPtr) {
    // *salaryPtr معناها: روح للعنوان ده وعدل القيمة اللي جواه
    *salaryPtr = *salaryPtr + 500; 
}

int main() {
    int mySalary = 5000;

    cout << "Before: " << mySalary << endl; // 5000

    // 2. وإحنا بننادي الدالة، لازم نبعت "العنوان" (&)
    // في كومبايلرز بتفهم لوحدها و مش محتاج تحط علامه العنوان معاها
    promote(&mySalary); 

    cout << "After: " << mySalary << endl;  // 5500 (اتعدلت بجد!)

    return 0;
}
```
> **الخلاصة:** لو عايز الدالة تغير في المتغير الأصلي، استقبل `pointer` وابعت `address`.

## Pass by Pointer VS Pass by Reference

- في **Pass by Reference (`&`):** أنت بتدي للدالة **"اسم دلع" (Alias)** للمتغير.
    
    - كأنك بتقول للدالة: "يا دالة، المتغير `x` اللي عندي بره، ناديه عندك باسم `ref`. أي حاجة تعمليها في `ref` هتسمع في `x` فوراً."
        
    - **ميزة:** كود نضيف جداً (Clean Code)، مش محتاج تكتب `*` ولا `&` وأنت بتنادي الدالة.
        
    - **قيد:** لازم يشاور على حاجة موجودة (مينفعش يكون فاضي).
        
- في **Pass by Pointer (`*`):** أنت بتدي للدالة **"عنوان البيت" (Address)**.
    
    - كأنك بتقول للدالة: "خدي العنوان ده، وروحيله بنفسك (`*`) عشان تعدلي فيه".
        
    - **ميزة:** مرن (ممكن يشاور على `nullptr` يعني ممكن تقوله "مفيش حاجة").
        
    - **عيب:** السنتاكس بتاعه "زحمة" (لازم تستخدم `*` و `&` كتير).
### إمتى أستخدم ده وإمتى أستخدم ده؟ (Best Practices) 💡

#### استخدم **References (`&`)** لما:

1. **الأمان والشياكة:** عايز الدالة تعدل في المتغير، ومش عايز وجع دماغ الـ `*` والـ `&`.
    
2. **إجباري:** المتغير **لازم** يكون موجود (مش ممكن يكون `null`). الريفرينس مستحيل يكون `null`.
    
3. **Operator Overloading:** هنعرف يعني ايه قدام
    

#### استخدم **Pointers (`*`)** لما:

1. **(الاحتمالية) Optionality :** عايز تبعت للدالة حاجة ممكن تكون "موجودة أو لا" (يعني ممكن تبعت `nullptr` كإشارة إن "مفيش قيمة").
    
2. **Arrays:** المصفوفات بتتحول لبوينترز غصب عننا، فبنستخدم البوينترز معاها.
    
3. **C-Libraries:** لو بتتعامل مع مكتبات قديمة مكتوبة بـ C.
   
## Passing Arrays to Functions

- **خليك فاكر:** *اسم الاراي هو اصلا بوينتر لاول عنصر  فيها"اسم الاراي بيشيل عنوان اول عنصر فيها"*
علشان كده الاراي في CPP دايما بتتبعت Pass by Pointer اوتوماتيك (مش بيتاخد منها Copy لأنها ممكن تكون كبيرة جداً).

- **الشكلين لتعريف الدالة (والاتنين واحد):**
```c++
void printArray(int* arr, int size)  // طريقة المحترفين
void printArray(int arr[], int size) // طريقة سهلة للقراءة (بس هي هي اللي فوق)
```

-  ليه لازم نبعت الـ Size؟ ⚠️

لأن البوينتر `arr` اللي راح للدالة ده "غلبان"، هو شايل عنوان أول عنصر بس، وميعرفش المصفوفة بتخلص فين. فلو مبعتش الـ Size، الدالة مش هتعرف تقف وهتكمل قراءة في الميموري العشوائية (Buffer Overflow).

```c++
#include <iostream>
using namespace std;

void doubleValues(int* ptr, int size) {
    for (int i = 0; i < size; i++) {

        // Using Pointer Arithmetic
        *(ptr + i) = *(ptr + i) * 2;

        // Another Method
        // ptr[i] = ptr[i] * 2;
    }
}

int main() {
    int nums[] = { 10, 20, 30 };
    int len = 3;

    doubleValues(nums, len); // donet need to add & Operator , as array name holds adrress of first element

    cout << nums[0] << " " << nums[1] << " " << nums[2]; // 20 40 60
    return 0;
}
```

## Return Pointer

هنا بقى التركيز كله. هل ينفع الدالة ترجع عنوان؟ **أه طبعاً.** بس فيه **Common Mistake**

### **Common Mistake:** Dangling Pointer

**Dangling Pointer:**
A pointer that **points to memory that is no longer valid**.
The pointer still holds the address, but the memory it points to has been **deleted or gone out of scope**.

تخيل دالة بتعرف متغير جواها، وبترجع عنوانه. المتغيرات اللي بتتعرف جوه الدالة (Local Variables) بتعيش في الـ **Stack**. أول ما الدالة تخلص وتوصل لـ `}`، المتغير ده **بيموت وبيتمسح من الذاكرة**. لو أنت رجعت عنوانه.. أنت بترجع عنوان "بيت مهدود".
```c++
int* createNumber() {
    int x = 10;   // x اتولد هنا (Local Variable)
    return &x;    // كارثة! بنرجع عنوان متغير هيموت حالاً
} // هنا x ماتت، والعنوان اللي رجع بقى بيشاور على "ولا حاجة"
```

###  Safe Ways 

عشان ترجع بوينتر بأمان، لازم يشاور على حاجة "عايشة ومكملة معانا". عندنا طريقتين لحد دلوقتي (لحد ما ناخد Dynamic Memory):

**1. ترجع بوينتر لحاجة Static (ثابتة):** الـ `static variable` مش بيموت بانتهاء الدالة، بيفضل عايش طول البرنامج.
لو مش فاكر يعني ايه static variable راجعه من هنا --> [[CPP Basics##Static Variable|Static Variable]]

**2. ترجع واحد من البوينترز اللي مبعوتلها أصلاً:** زي دالة بتشوف مين الأكبر وترجع عنوانه.

```c++
#include <iostream>
using namespace std;

// الدالة بتاخد عنوانين، وبترجع عنوان القيمة الأكبر فيهم
int* getMax(int* a, int* b) {
    if (*a > *b)
        return a; // برجع العنوان اللي جالي (وهو أكيد سليم لأنه جاي من main)
    else
        return b;
}

int main() {
    int x = 10, y = 20;
    
    int* maxPtr = getMax(&x, &y); // maxPtr هيشيل عنوان y
    
    cout << "Max Value: " << *maxPtr << endl; // 20
    
    // لو غيرنا القيمة عن طريق البوينتر اللي رجع
    *maxPtr = 50; 
    cout << "New y: " << y << endl; // y بقت 50!

    return 0;
}
```


# Pointers and Const

أنت عندك حاجتين ممكن يكونوا `const`:

1. **المتغير نفسه (The Data):** القيمة اللي جوه الصندوق.
    
2. **البوينتر نفسه (The Pointer):** الورقة اللي شايلة العنوان.
    

مكان كلمة `const` هو اللي بيحدد مين فيهم اللي ممنوع يتغير.


| مكان const | النوع                        | اللي هيحصل                |
| ---------- | ---------------------------- | ------------------------- |
| قبل `*`    | Pointer to Constant          | Constant Value            |
| بعد `*`    | Constant Pointer             | Constant Address          |
| بعد و قبل  | Constant Pointer to Constant | Constant Value and Adress |

## 1. Pointer to Constant

البوينتر ده "بيتفرج بس". يقدر يشاور على أي حد، لكن **ميقدرش يغير قيمة** الحد ده.

- **Syntax:**  `const int* ptr` **OR** `int const *ptr`

```c++
int x = 10;
int y = 20;
const int* ptr = &x; // (1) تعريف البوينتر

*ptr = 100; // ❌ خطأ: ممنوع تغيير القيمة (Read-only)
ptr = &y;   // ✅ صح: مسموح يغير العنوان ويشاور على y
```

## 2. Constant Pointer

البوينتر ده اتخلق بيشاور على `x`، هيموت وهو بيشاور على `x`. مستحيل يغير العنوان. لكن يقدر يغير القيمة اللي جوه `x` براحته.

- **Syntax:**  `int* const ptr`

```c++
int x = 10;
int y = 20;
int* const ptr = &x; // لازم ياخد عنوان لحظة التعريف

*ptr = 100; // ✅ صح: يقدر يغير القيمة عادي (بقت 100)
ptr = &y;   // ❌ خطأ: ممنوع يغير العنوان (Assignment of read-only variable)
```

## 3. Constant Pointer to Constant

لا ينفع يغير العنوان، ولا ينفع يغير القيمة. استخدام للقراءة فقط (Read-Only Access) للمكان ده تحديداً.

- **Syntax:**  `const int* const ptr`

```c++
int x = 10;
const int* const ptr = &x;

*ptr = 100; // ❌ خطأ
ptr = &y;   // ❌ خطأ
```
