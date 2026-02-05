# 📚 C++ Libraries & Headers



> [!ABSTRACT] يعني إيه تعمل مكتبتك الخاصة؟
> الفكرة ببساطة إنك **تكتب كود C++ مرة واحدة**، وتحطه في ملفات منظمة، وتستخدمه بعد كده في أي مشروع تاني **من غير ما تعيد كتابة الكود**.
>
> * 🎯 **الهدف:** زي ما بتستخدم `iostream` أو `cmath`، أنت هتعمل مكتبة خاصة بيك تضبطها على مزاجك.

---
## رحله الكود من CPP ل EXE

قبل ما نتكلم عن المكتبات، لازم نفهم مسار الكود بيمشي ازاي من أول ما بنكتبه لحد ما يبقى برنامج شغال (`.exe`).

### 1️⃣ مرحلة الـ Preprocessing (التجهيز)

قبل ما الـ Compiler يبدأ شغل، الكود بيعدي على برنامج صغير اسمه **Preprocessor**.

> [!NOTE] 🕵️‍♂️ مين هو الـ Preprocessor؟
> ده برنامج **مبيفهمش C++** أصلاً! ولا يعرف يعني إيه متغيرات ولا دوال.
> * **شغلته:** بيفهم بس الأوامر اللي بتبدأ بـ ** (`#`)** زي `#include` أو `#define`.
> * **وظيفته:** بيعمل **Text Manipulation** (يعني تعديل نصوص) بيجهز ملف الكود عشان يدخل للكومبايلر.

#### بيعمل ايه بالضبط؟
1) **مع `#include` (عمليه Copy & Paste**)
   اول ما يشوف السطر ده:
   `#include <iostream>`
   بيروح لملف iostream و ينسخ كل محتواه و يحطه مكان السطر ده بالضبط , علشان كده الكود بعد المرحله دي بيبقي حجمه كبير

2) **مع `#define` (عملية Find & Replace)** لو شاف كود زي ده:
   `#define PI 3.14`
   بيمشي علي الكود كله و كل ما يلاقي كلمه `PI` يشيلها و يحط 3.14 
   
3) **تنظيف الكود** الـ Preprocessor بيشيل كل **الـ Comments** من الكود، عشان يسلم الكود للكومبايلر "على نظافة" (Pure CPP Code)

### 2️⃣ مرحلة الـ Compiling

نحويل كود CPP ل Machine Code او Object Code امتداداه `.obj` في ويندوز و `.o` في لينكس 

### 3️⃣ مرحلة الـ Linking

الكومبايلر لما بيترجم ملف هو بيعرف يحجز مكان للمتغيرات لكن لما يوصل للسطر اللي فيه استدعاء لداله من مكتبه مثلا مبيعرفش يتعامل فبيسيب في المكان ده فراغ او علامه يجيي بقي اللينكر يجيبله كود الداله اللي هو مش عارف يوصله ده 

## انواع المكتبات

### 1️⃣ Static Library

- **Extensions:** `.lib` (Windows) / `.a` (Linux/Unix)

#### **How it Works**

Think of this like **copy-pasting** pages from a textbook directly into your own notebook. When you **compile** your program, the **Linker** takes a copy of the library's object code and physically **embeds (merges)** it inside your application's executable file (`.exe`).

#### **Pros**

- **Portability:** The final result is a **single standalone file** (`.exe`). It does not require any external files to run.
    
- **Speed:** execution can be slightly faster because everything is bundled together (allows for better compiler optimization).
    

#### **Cons**

- **File Size:** The executable size becomes **larger** because it carries the library code inside it.
    
- **Updates:** If you find a bug in the library, you must **recompile the entire application** to apply the fix.
--- 

### 2️⃣ Dynamic Library

- **Extensions:** `.dll` (Windows) / `.so` (Linux)

#### **How it Works**

Think of this like writing a **reference** in your notebook: _"See page 50 in Book X"_. The **Linker** _does not_ copy the code. Instead, it places a **pointer (address)** inside your executable that says: _"When this function is needed, jump to the `.dll` file located next to me and execute it."_ This happens at **Runtime**.

#### **Pros**

- **Small Size:** The executable (`.exe`) remains very lightweight.
    
- **Memory Efficiency:** Multiple different programs can use/share the **same** `.dll` file in memory simultaneously.
    
- **Easy Updates:** You can fix or update the `.dll` file separately. All programs using it will work with the new version immediately **without recompiling**.
    

#### **Cons**

- **Dependencies (DLL Hell):** If the `.dll` file is missing, moved, or incompatible, the program will fail to run (The famous **"DLL Not Found"** error).

## Header-Only Library (Code Organization)

#### **What is this method?**

Since you added the `.h` file directly inside your main project and used `#include`, this is considered **Source Code Organization** or a **Header-Only Library**.

It is **NOT** a compiled library (Static/Dynamic) because you did not create a separate "Library Project".

#### **How it works**

1. **Preprocessor Action:** When you run the code, the Preprocessor essentially **copy-pastes** the content of your `.h` file into your `main.cpp` where the `#include` line is.
    
2. **Compilation:** The compiler translates your `.h` code along with your main code every time you build the project.
    

#### **Difference from "Real" Libraries**

- **Your Method:** You have the raw source code. It gets recompiled every time. Best for project-specific organization.
    
- **Static/Dynamic Libs:** The code is pre-compiled into binary format (`.lib`/`.dll`). It saves compilation time for huge projects and hides the source code.
    

#### **Visual Studio Tip**

> **Note:** In the Solution Explorer, you typically use **Right-Click** (not Left-Click) on the "Header Files" folder to select **Add > New Item** > .h > create a namespace and write in it .

#### Example

```cpp
#pragma once
#include <iostream>
using namespace std;
namespace mylib
{
	void Test()
	{
		cout << "Hi it's My First Function In My First Library \n";
	}
}
```

```cpp
#include <iostream>
#include "mylib.h"
using namespace std;

int main()
{    
    mylib::Test();
}
```
## How to Create a Real Static Library in VS 2022

To create a "true" compiled library (not just header files), you must create a dedicated project for it.

#### **Step 1: Create the Library Project**

1. Go to **File > New > Project**.
    
2. Search for **"Static Library"** (make sure C++ is selected).
    
3. Name it (e.g., `MathLib`).
    
4. **Important:** This project will **NOT** have a `main()` function.
    

#### **Step 2: Write the Code**

Create your `.h` and `.cpp` files as usual.

- `MathLib.h`: Contains function declarations.
    
- `MathLib.cpp`: Contains function definitions.
    

#### **Step 3: Build (Do Not Run)**

Since libraries are not executable apps, you cannot "Run" them.

1. Go to the **Build** menu > **Build Solution** (or press `Ctrl + Shift + B`).
    
2. **Output:** Go to your project folder (usually inside `x64/Debug`), and you will find a **`.lib` file** (e.g., `MathLib.lib`).
    

#### **Step 4: How to Use It (Linking)**

To use this `.lib` in another project (e.g., a Console App), you must configure the **Project Properties** of the _Console App_:

1. **C/C++ > General > Additional Include Directories:** Point to the folder containing your `.h` file.
    
2. **Linker > General > Additional Library Directories:** Point to the folder containing your `.lib` file.
    
3. **Linker > Input > Additional Dependencies:** Type the full name of your library (e.g., `MathLib.lib`).