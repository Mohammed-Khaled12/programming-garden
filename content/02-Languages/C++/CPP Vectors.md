
> [!ABSTRACT] What is a Vector?
> A dynamic array that can **resize itself** automatically when an element is inserted or deleted.
> * Unlike standard arrays `int arr[5]`, vectors handle memory management for you.
> * It is the most used container in the **STL** (Standard Template Library).

**Header Required:** `#include <vector>`

Tags: #cpp #stl #data-structures #vectors

---

# What is Vector ?

A vector is a container that can store data. It is like an array  However, a vector offers several advantages over arrays. Here are just a few:
• You do not have to declare the number of elements that the vector will have.
• If you add a value to a vector that is already full, the vector will automatically
increase its size to accommodate the new value.
• vectors can report the number of elements they contain. 

> [!INFO] Under the Hood
> A Vector is essentially a smart **Dynamic Array**. It manages [[CPP Dynamic Memory]] automatically (handles allocation/deallocation) so you don't have to use `new` and `delete`.
# Syntax

```c++
vector<Type> name 
```

**Notes:**
- To use vectors in your program, you must include the `<vector>` header file with the following statement: `#include <vector>`
- `vector<int> numbers(10);` --> This statement defines numbers as a vector of 10 ints. This is only a **starting size**, however. Although the vector has 10 elements, its size will expand if you add more than 10 values to it.
- `vector<int> numbers(10, 2);`--> you may also specify an initialization value, The initialization value is copied to each element , In this statement, numbers is defined as a vector of 10 ints. Each element in numbers is initialized to the value 2.

# Some of the vector Member Functions

الفيكتور كلاس , عنده شويه دوال و خواص نقدر نستعملها

![[Pasted image 20260125150238.png]]
*STARTING OUT WITH C++ From Control Structures through Objects*

**Notes:**
- You cannot use the [] operator to access a vector element that does not exist. To store a
value in a vector that does not have a starting size, or that is already full, use the push_back
member function. The push_back member function accepts a value as an argument and
stores that value after the last element in the vector. (It pushes the value onto the back of
the vector.)

- Capacity Vs Size Functions:
  ![[Pasted image 20260125154628.png]]


# Vector VS Array

```c++
  - Vector
  ---> It Need A Standard Header To Work
  ---> Can Be Resized After Insertion Or Deletion Of Elements
  ---> Not Index Based And Elements Accessed By Iterators
  ---> Vectors Are Slower Than Arrays
  ---> Vectors Occupy More Memory
  ---? Available In C++ Only

  - Array
  ---> C-Array Is Language Construct Do not require a Header File
  ---> Cannot Be Resized After Its Defined
  ---> Elements Accessed By Indexes
  ---> Arrays Are Faster Than Vectors
  ---> Arrays Occupy Less Memory
  ---> Available In C & C++
```

***Tip:***
in modern C++ if you want an array with Fixed size use the `std::array` "Class Array" 
instead of C-Style Array , it gives you functions like the Vector ones and more safety 

See Class Array From Here [[CPP Basics]] C++ Hand-Written Legacy Notes Page 32 and 33

# Access, Add, Update And Delete

## Access

| Method                   | Syntax         | Description                       |
| ------------------------ | -------------- | --------------------------------- |
| Index\subscript Operator | `name[index]`  | Like Arrays. Fast but dangerous.  |
| At Function              | name.at(index) | Throws exception if out of range. |
| First Element            | name.front()   | Returns `name[0]`.                |
| Last Element             | name.back()    | Returns last element.             |
*NOTE:*

- `[]` **does NOT check if the index is valid**.
    
- If you access an index that doesn’t exist, your program **may crash** or give **garbage value**.
    
- No runtime error will stop you. It silently trusts you.
    
✅ **When to use:** Only when you are 100% sure the index exists.

- `at()` **checks the index**.
    
- If the index is invalid, it throws an **out_of_range exception**.
    
- Safer, especially when dealing with **user input** or **dynamic vectors**.

```c++
vector<int> v = {10, 20, 30};

cout << v[0];      // 10
cout << v.at(1);   // 20
cout << v.front(); // 10
cout << v.back();  // 30
```

## Add

using `name.push_back(value)` Stores value in the last element of the vector.
or using `.insert(iterator)` دور عليها قدام لما نحتاجها

## Update

هو هو ال Access بس حط يساوي 

```c++
vector<int> v = {10, 20, 30};

v[1] = 500;      // Update index 1
v.at(2) = 1000;  // Update index 2

// v becomes {10, 500, 1000}
```

## Delete

| Syntax           | Description                          |
| ---------------- | ------------------------------------ |
| .pop_back()      | Removes the last element only        |
| .clear()         | Clears a vector of all its elements. |
| .erase(iterator) | Removes Specific element             |

# Iterator

## يعني ايه Iterator ؟

الايتيرايتور بنستخدمه علشان نشير لعنوان الذاكره الخاص بالكونتينر بتاعك.

ببساطة شديدة: **الـ Iterator هو "مؤشر ذكي" (Smart Pointer).**
تخيل الـ Vector ده عبارة عن صف من الصناديق:

- **الـ Index:** هو "رقم" الصندوق (0، 1، 2). عشان توصل للصندوق لازم تقول "أنا عايز الصندوق رقم 5".
    
- **الـ Iterator:** هو "صبعك" وهو بيشاور على الصندوق. أنت مش محتاج تعرف رقم الصندوق كام، أنت بس محتاج تقول "هات اللي صبعي عليه" أو "حرك صبعي للصندوق اللي بعده".

**Note:** Iterators in vectors work very similarly to [[CPP Pointers]] arithmetic.
## كان ماله الـ Index؟ (ليه اخترعوا الـ Iterator؟)

للـ Vector تحديداً؟ الـ Index مالهوش أي حاجة وحش، بالعكس هو أسرع وأسهل. **لكن المشكلة في اللي مش Vector.**

الـ C++ فيها حاويات (Containers) تانية غير الـ Vector، زي:

- **List:** (زي السلسلة، كل عنصر مربوط باللي بعده).
    
- **Set:** (زي الشوال، العناصر مش مترتبة بترتيب دخولها).
    
- **Map:** (زي القاموس).
    
الحاويات دي **مفيش فيها Index**. مينفعش تقول للـ List "هاتي العنصر رقم 5"، لأن العناصر مش ورا بعض في الميموري، هي متطورة في كل حتة.
**عشان كدة عملوا الـ Iterator:** عشان يبقى عندنا **طريقة موحدة** نلف بيها على أي حاجة في الـ C++، سواء كانت Vector أو List أو Set. لو فهمت الـ Iterator، هتعرف تتعامل مع كل الـ STL Containers بنفس الكود

## Syntax

**`Container<Type>::iterator IteratorName;`**

## إزاي بيشتغل؟ (begin و end)

الـ Iterator بيعتمد على دالتين أساسيتين جوه الـ Vector:

- `v.begin()`: ده "صبعك" وهو بيشاور على **أول** عنصر.
    
- `v.end()`: دي الخدعة! ده بيشاور على مكان **"بعد" آخر عنصر** (مكان فاضي). وده بنستخدمه عشان نعرف إننا خلصنا لف.
## More on Code

```c++
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    vector<int> nums = { 10, 20, 30 };

    // Created an iterator named 'it' and initialized it to point to the first element.
    vector<int>::iterator it = nums.begin();

    // Another method using 'auto': created a second iterator pointing to the second element (begin + 1).
    auto ite = nums.begin() + 1;

    // Dereferencing (*): Used like a pointer to access/print the value the iterator is pointing to.
    cout << "First Element Is: " << *it << "\n"; // 10
    cout << "First Element Is: " << *nums.begin() << "\n"; // 10

    cout << "Second Element Is: " << *ite << "\n"; // 20

    // erase from element 1 to elemnt 3 (3 not included)
    nums.erase(nums.begin(), nums.begin() + 2); 

    cout << "First Element After Erase Is: " << *nums.begin() << "\n"; // 30

    // insert in the first position: 5
    nums.insert(nums.begin(), 5);
    cout << "First Element After Insert Is: " << *nums.begin() << "\n"; // 5

    return 0;
}
```

**Note:**
لما بتعمل `erase` أو `insert`، الفيكتور بيغير أماكن العناصر في الميموري. ده معناه إن **الـ Iterator القديم اللي كنت ماسكه في إيدك مبقاش صالح (Invalid)** ومينفعش تستخدمه تاني، لأنه بقى بيشاور على مكان ممسوح أو مكان غلط.
## Pointer VS Iterator

الإجابة المختصرة: **الـ Iterator هو "بوينتر متنكر" أو نقدر نقول عليه "Smart Pointer" (مؤشر ذكي).**

تعالى نفصص الفرق بينهم عشان الصورة توضح تماماً:

### 1. الـ Pointer (العنوان الخام)

البوينتر ده "غشيم" شوية. هو مجرد متغير شايل **عنوان في الميموري** (Memory Address).

- لما بتقوله `++` (زود واحد)، هو بيمشي خطوة في الرامات بناءً على حجم المتغير (مثلاً 4 بايت لو `int`).
    
- بيفترض دايماً إن العناصر ورا بعضها في الميموري (Contiguous Memory).
    
- **مشكلته:** لو البيانات **مش** ورا بعضها (زي الـ Linked List)، البوينتر لو عمل `++` هيدخل في مكان غلط (Garbage) وممكن البرنامج يضرب (Crash).
    

### 2. الـ Iterator (المرشد الذكي)

الـ Iterator هو **Class (كائن)** مكتوب جوه الـ STL عشان يتصرف كأنه بوينتر، بس هو أذكى:

- هو "عارف" شكل الـ Container اللي هو شغال عليه.
    
- لما بتقوله `++`، هو مش مجرد بيزود عنوان، هو بيشغل كود داخلي (Operator Overloading) يسأل نفسه: "يا ترى العنصر اللي عليه الدور فين؟".
    
- **ميزته:**
    
    - في الـ **Vector**: هيشتغل زيه زي البوينتر بالظبط (لأن الفيكتور عناصر ورا بعض).
        
    - في الـ **List**: لما تعمل `++`، الـ Iterator هيروح يشوف الرابط (Link) اللي بيشاور على العنصر التالي (اللي ممكن يكون في مكان بعيد خالص في الميموري) وينط عليه صح.

### تشبيه بسيط (الأتوبيس والتاكسي) 🚌🚕

تخيل إننا بنزور بيوت أصحابنا:

1. **الـ Vector (عمارة سكنية):** أصحابك ساكنين في شقق 1، 2، 3 ورا بعض.
    
    - **البوينتر:** يقدر يوصلهم بسهولة، بيطلع من شقة 1 للي جنبها شقة 2. (سهل لأنهم لازقين في بعض).
        
2. **الـ List (بيوت متفرقة في المدينة):** واحد ساكن في المعادي، وواحد في مدينة نصر، وواحد في التجمع.
    
    - **البوينتر:** لو خرج من بيت المعادي ومشي خطوة واحدة "جنبه"، هيلاقي نفسه في الشارع (Garbage).
        
    - **الـ Iterator:** عامل زي سواق التاكسي اللي معاه اللوكيشن. لما تقوله "وديني للي بعده `++`"، هو عارف الطريق وهياخدك من المعادي لمدينة نصر مباشرة، حتى لو بعيدة.

## The Modern Way: Range-Based For Loop

Instead of defining an iterator manually, let C++ do the work.
Recommended for printing or simple access.

```c++
vector<int> nums = {10, 20, 30};

// "for every number x in nums"
for (int x : nums) {
    cout << x << " "; 
}
// Output: 10 20 30
```

![[Pasted image 20260126110354.png]]

## Traversing With Iterator

```c++
#include <iostream>
#include <vector>

using namespace std;

int main()
{
  vector<int> nums = { 10, 20, 30, 40 };
  vector<int>::iterator first = nums.begin();
  vector<int>::iterator last = nums.end() - 1;

  cout << "First Element Is: " << *first << "\n"; // 10
  cout << "Second Element Is: " << first[1] << "\n"; // 20
  cout << "Third Element Is: " << first[2] << "\n"; // 30

  cout << "Last Element Is: " << *last << "\n"; // 40
  cout << "Before Last Element Is: " << *(last - 1) << "\n"; // 30

  advance(first, 3);

  cout << "First Element Is: " << *first << "\n"; // 40

  advance(first, -2);

  cout << "First Element Is: " << *first << "\n"; // 20

  return 0;
}
```

# Using Vector and Iterator To Count, Sort & Reverse

اولا لازم تستدعي مكتبه algorithm :
**`#include<algorithm>`**

- `sort(start , end)` --> بترتب العناصر تصاعدي في الرينج اللي انت حاطه
- `sort(start, end, greater<int>())`--> بترتب في الرينج تنازلي
- `reverse(start, end) `--> بتقلب البيانات فعليا في الميموري مش بترتب
- `count(start, end , value to count)` --> لو عايز تعرف رقم معين متكرر كام مرة جوه الفيكتور

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main()
{
  vector<int> nums = { 10, 500, 60, -20, 20, 20, 100, 20 };

  int val = 20;
  int countTimes = count(nums.begin(), nums.end(), val);
  cout << "Number " << val << " Found " << countTimes << " Times.\n";

  cout << "=====================\n";

  for (int &n : nums)
  {
    cout << n << "\n";
  }

  cout << "=====================\n";

  sort(nums.begin(), nums.end());

  for (int &n : nums)
  {
    cout << n << "\n";
  }

  cout << "=====================\n";

  reverse(nums.begin(), nums.end());

  for (int &n : nums)
  {
    cout << n << "\n";
  }

  cout << "=====================\n";

  return 0;
}
```

# Notes:

## C++ Iterators: Forward vs. Reverse
### 1. Forward Iterators (`begin` & `end`)

Used for traversing a container from **left to right** (start to finish).

- **`begin()`**: Returns an iterator pointing to the **first element** of the container (Index $0$).
    
- **`end()`**: Returns an iterator pointing to the **theoretical element** that follows the last element. It acts as a sentinel; attempting to dereference it will cause a crash.
    
- **Incrementing (`it++`)**: Moves the iterator **forward** toward the end of the container.
    

### 2. Reverse Iterators (`rbegin` & `rend`)

Used for traversing a container from **right to left** (finish to start).

- **`rbegin()`** (Reverse Begin): Returns a reverse iterator pointing to the **last actual element** (the reverse beginning).
    
- **`rend()`** (Reverse End): Returns a reverse iterator pointing to the **theoretical element preceding the first element**.
    
- **Incrementing (`it++`)**: Moves the iterator **backward** toward the beginning of the container.

| **Iterator**   | **Points to...** | **Direction of ++** | **Common Use Case**            |
| -------------- | ---------------- | ------------------- | ------------------------------ |
| **`begin()`**  | First Element    | Forward             | Normal processing/sorting.     |
| **`rbegin()`** | Last Element     | Backward            | Reversing strings or logic.    |
| **`end()`**    | After Last       | N/A                 | Loop termination (Stop point). |
| **`rend()`**   | Before First     | N/A                 | Reverse loop termination.      |
In Modern C++, always prefer using `rbegin()` and `rend()` when reversing a collection. It is safer than manual indexing (like `size()-1`) because it handles empty containers gracefully and follows the **RAII** (Resource Acquisition Is Initialization) philosophy of the C++ Standard Library.