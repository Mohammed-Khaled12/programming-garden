
# ☕ Java Kickstart & Syntax

> [!ABSTRACT] What is Java?
> A class-based, object-oriented language.
> **Motto:** *"Write Once, Run Anywhere"* (WORA).

Tags: #java #syntax #basics #jvm

---


# What Is Java?

Java is a class-based, object-oriented programming language. It is widely used for developing various types of applications, ranging from simple desktop utilities to complex enterprise-level systems
Java is available for most operating systems (Write once run anywhere).

# How Java Works?

```text
Source Code(.Java file) --> Javac (Java Compiler) --> Byte Code (.class File) --> JVM 
                                         |                                         |
                                         |                                         > Interpreter Takes
                                         |                                           Byte Code And
                                         |                                           Transform it To
                                         |                                           Machine Code
                                         |
                                         |
                                         > Half-baked Code CPU and Windows Can't Understand it, 
                                           But JVM Can and By This Way You Can Run Java Code On 
                                           any Machine That Have JVM 
```

![[Screenshot 2025-11-25 173358.png]]

# First Program

```java
package com.mycompany.test;

public class TEST {
 static public void main(String args[])
 {
	 System.out.print(" Hi ");
 }
}

```
تعالي نشرح سطر سطر :
1) الباكيدج في جافا هو مجرد فايل او ملف بتحط فيه الكلاسات علشان تنظم المشروع "عنوان المجلد بتاع مشروعك"
2) بابليك public يعني ان الكلاس ده متاح لاي مكان في المشروع , كلاس class علشان انشئ كلاس , تيست TEST اسم الكلاس (لازم يكون نفس اسم الملف ال .java)
3) ده سطر ال main ال JVM اول ما يشتغل بيدور علي ال main  , تعالي نفكك السطر كلمه كلمه :
    بابليك public يعني ان الكلاس ده متاح لاي مكان في المشروع ليه بقي خليناه كده؟ علشان ال JVM اللي جي من بره الكلاس يعرف يشوفه و تقدر تدخل علي الكلاس
    ليه static؟ لأن JVM عايزة تشغلها **من غير ما تعمل object** "هتفهمها اكتر قدام"
    فويد void يعني مش بترجع قيمه 
    سترنج ارجيومنتس (String args[]) دي مصفوفة من Strings اسمها args تمثل **arguments** اللي تيجي من سطر الأوامر *خلي بالك ال s بتاعت سترينج كابيتال في الجافا String علشان هو هنا Object مش Primitives *

# طرق الطباعه Output:

1) System.out.print("Text")
2) System.out.println("Text") --> اطبع و انزل سطر جديد


# انواع البيانات Data Types:

زي Cpp ماعدا كام اختلاف:
1) الـ String في جافا هو **Object** (كلاس كامل) وليس Primitive و s كابيتال S
2) ال bool هتتكتب boolean و خلي بالك لا يقبل الصفر ولا الواحد يقبل true , false فقط
3) نوت مهمه --> علشان اقارن نصين زي ال cpp مبستعملش علامه == علشان ده هيقارن الريفيرينس لكن بستعمل .equal()
4)  نوت مهمه --> علشان اعمل متغير ثابت او Constant بستعمل الكلمه دي final int z = 7
```java
String s1 = "Hello";
String s2 = "Hello";

if (s1 == s2) { ... } // ❌ بيقارن عناوين الذاكره (بوينترز)
if (s1.equals(s2)) { ... } // ✅ صح! بيقارن المحتوى الفعلي

```
   
# طرق الادخال Input:

مفيش cin في الجافا لكن بنستخدم كلاس مساعد اسمه Scanner
الاول بنحتاج نستدعي مكتبه اسمها java.util.Scanner

```java
package com.mycompany.test;
import java.util.Scanner;

public class TEST
{
    public static void main(String args[])
    {
        Scanner in = new Scanner(System.in);
        
        final float PI = 3.14f;
        Float Radius , Area;
        
        System.out.println("Please Enter Radius");
        Radius = in.nextFloat();
        
        Area = PI * Radius * Radius;
        System.out.println("Arae =" + Area);
    }
}

```
### ملخص ازاي تعمل ادخال:

- **1.** استدعي مكتبة `Scanner` ⬅️ كود: `import java.util.Scanner;`
- **2.** اعمل `Object` منها ⬅️ `Scanner in = new Scanner(System.in);`
- **3.** علشان تدخل في متغير استخدم ⬅️ `nextInt()`, `nextFloat()` and So on.
- **4.** لو هتقرا سترينج `next()` دي كلمه بدون مسافات، `nextLine()` دي سطر كامل.