# 🧬 Object-Oriented Thinking (OOP)

> [!ABSTRACT] 📝 Note Context
> **Type:** Hybrid Note (Concept + Implementation).
> **Core Topic:** The 4 Pillars of OOP (Encapsulation, Inheritance, Polymorphism, Abstraction).
> **Language Used:** Java ☕ (for demonstration).
> 
> *Note: Although written in Java, these concepts apply to C++, Python, and C#.*

Tags: #concept #oop #java #fundamentals

---
# Youtube Playlist
{
https://youtube.com/playlist?list=PLwWuxCLlF_ue7GPvoG_Ko1x43tZw5cz9v&si=ZND2aqnMDiRsE1d2
}
# Why OOP ?

قبل ال OOP كنا بنتعامل مع اجزاء مفككه , تلاقي الداتا مرميه في متغيرات او ستراكتس و الدوال موجوده في مكان تاني و تناديهم , الكلام ده عادي لما يكون البرنامج صغير المشكله بتظهر لما يكبر البرنامج و بيبقي في دوال و متغيرات كتيره و يبقي صعب التعديل عليه 

# What Is OOP ?

هو طريقه تنظيميه لها شويه مبادي و مصطلحات هدفها تربط الداتا و الدوال ببعض في كائن واحد
مثال :
العربيه ليها مواصفات (سرعه , لون ) و ليها افعال (تتحرك , تقف) فاا باستخدام ال OOP هنقدر نجمع كل ده في مكان واحد 

# Class , Objects and Constructor

## Class :
 هو المخطط او الورقه اللي مكتوب فيها المواصفات 
مثال :
التصميم بتاع العربيه و وصفها زي لونها او سرعتها او دوال تحركها و وقوفها ده الكلاس هو مش عربيه حقيقيه هو بس ورقه بمواصفاتها 
الكلاس بيحتوي علي افعال (Methods or Functions) زي داله الفرامل و التشغيل في العربيه و صفات (Attributes) زي اللون و الموديل 

## Object :
هو الكيان الحقيقي بمعني انك اخدت ورقه المواصفات (Class) و حولتها لحاجه علي ارض الواقع مش مجرد ورقه فيها مواصفات
مثال:
روحت مكتب هندسي معماري قولتله صمملي مبني فاا هو صممه و اداك ورقه مرسوم فيها و فيها كل المواصفات (**Class**) خدت انت الورقه و روحت لمقاول يعملك العماره (**Constructor**) العماره اتعملت (**Object**) و من ورقه مواصفات واحده تقدر تعمل عدد لا نهائي من المباني 
كلاس واحد تقدر تنشي منه عدد لا نهائي من الاوبجيكتس 


## Constructor :
هو فانكشن خاصه جوه ال Class بتشتغل لحظه ما تعمل Object و هي اللي بتعمل ال Object و تجهزه بالقيم اللي انت عايزها 
و هنتكلم عنه اكتر تاني قدام 



# More Details With Coding : 

*Class File* :

```java
package com.mycompany.test;

public class Car 
{
    // Default Constructor --> بيعملك اوبجيكت من غير ما تبعتله اي قيم 
    // و ممكن متكتبوش عادي و هو بيبقي موجود بس مش ظاهر و ممكن تكتبه و تحط في اي حاجه هتتنفذ مع انشاء الاوبجيكت
    public Car()
    {
        System.out.println("I'm Using This Constructor");
    }
    
    // Attributes >>> الصفات
    int Speed;
    String Color;
    String Model;
    
    // Functions .. Methods >>> الافعال
    void Turn_On()
    {
        System.out.println("The Car Is Being Turned On");
    }
    void Turn_Off()
    {
        System.out.println("The Car Is Being Turned Off");
    }
    
}
```

*main File* : 
```java
package com.mycompany.test;

public class TEST
{
   public static void main(String []args)
   {
      Car C1 = new Car(); // Making an Object
      // Car >> Class
      // C1 >> Reference Object >> بيشاور علي مكان الاوبجيكت في الميموري
      // () >> Constuctor
      C1.Color = "RED";
       System.out.println(C1.Color);
       
       Car C2 = new Car(); // كلاس واحد اعمل منه عدد لا نهائي من الابجيكتس
       // كل ما اعمل اوبجيكت الكونستراكتور هيشتغل تاني علشان يعمله
       // كل اوبجيكت ليه نسخته المنفصله من الداتا اللي في الكلاس
       C2.Color = "Green";
       C2.Model = "BMW";
       System.out.println(C2.Color);
       System.out.println(C2.Model);
       C1.Turn_On();
       System.out.println("After 2 Hours:");
       C1.Turn_Off();
       // ايه اللي هيحصل لو طبعنا ال سي 2 نفسه؟
       System.out.println(C2); // مكان الاوبجيكت في الميوري بالهيكساديسيمل

   }
}
```

# More About Constructor :

##  قواعد الـ **Constructor** :

1. **الاسم:** يجب أن يكون **نفس اسم الكلاس** بالضبط (حرفياً).
    
2. **الإرجاع:** لا يرجع أي قيمة (لا تكتب `void` ولا `int` ولا أي شيء).
    
3. **التوقيت:** يعمل مرة واحدة فقط عند الـ `new`.
   
4. اغلب الوقت بيبقي `public` --> تقدر تعمل اوبجيكت من الكلاس في اي حته بره الكلاس  ده معني `public`

## انواع ال **Constructor** :

#### النوع الأول: Default Constructor (هدية الجافا)

إذا أنت **لم تكتب** أي constructor في الكلاس بيدك، الجافا ستصنع واحداً خفياً لك لكي لا يتعطل الكود.
هو اللي بيبنيلك Object **من غير ما تبعت له أي قيم** , زي إنك تقول للمقاول “ابني عمارة بالتصميم الافتراضي اللي عندك.”

- شكله: فارغ تماماً `public Robot() {}`.
    
- وظيفته: ينشئ الأوبجكت والقيم كلها أصفار و null.
    
- **تحذير:** بمجرد أن تكتب أي constructor بيدك، الجافا تسحب هذه الهدية فوراً.
  
#### النوع الثاني: No-Arg Constructor (القيم الافتراضية)

تكتبه أنت، لكن لا يأخذ أي باراميترز. نستخدمه لو أردنا قيم "ثابتة" لكل الأوبجكتس.

```java
public Robot() {
    name = "Generic Bot"; // أي روبوت ينشأ بدون اسم سيأخذ هذا الاسم
    power = 100;          // أي روبوت يبدأ مشحوناً
}
```

#### النوع الثالث: Parameterized Constructor (التخصيص)

هذا هو الأكثر استخداماً. تجبر المستخدم أن يعطيك البيانات لحظة الإنشاء.
```java
public Robot(String n, int p) {
    name = n; // n parameter in the costructor and we will put the value of it in the class variable name 
    power = p;
}

// in main --> Robot R1 = new Robot(name:"aqw" , power: 100)
```

```java
public class Car
{
	int Speed;
	String Model;
	
	public Car(String Model)
	{
		this.Model = Model; // علشان افرق بين الباراميتر بتاع الكونستراكتور و المتغير بتاع الكلاس بحط ذيس دوت الاسم مع المتغير بتاع الكلاس
	}
}
```

## امثله : 

```java
package com.mycompany.test;

public class TEST
{
   public static class Car // لو هتكتب الكلاس فوق الماين حط ستاتيك او تحطه في ملف لوحده
   {
       int Year;
       String Model;
       
       public Car(int Year , String Model)
       {
           this.Year = Year;
           this.Model = Model;
       }
   }
    
   public static void main(String []args)
   {
      Car C1 = new Car(1990,"BMW");
      
      System.out.println("Model:" + C1.Model);
      System.out.println("Year:" + C1.Year);
   }
}
```

```java
package com.mycompany.test;

public class TEST
{
   public static class Student
   {
       String name;
       int grade;
       
       public Student()
       {
           name = "Unknown";
           grade = 0;
       }
       public Student(String name , int grade)
       {
           this.name = name;
           this.grade = grade;
       }
   }
    
   public static void main(String []args)
   {
      Student st1 = new Student();
       System.out.println("Name: " + st1.name);
       System.out.println("Grade: " + st1.grade);
       
      Student st2 = new Student("Mohammed" , 100);
       System.out.println("Name: " + st2.name);
       System.out.println("Grade: " + st2.grade);
   }
}
```


# OOP Concepts :

## 1) Encapsulation:

هو حمايه الداتا اللي في الكلاس من ان اي حد يعدل او يلعب فيها طب ازاي؟

### Access Modifiers :

1) **Public** --> الداتا مقتوحه لاي حد يعدل عليها او يستخدمها عادي
2) **Private** --> قفلت الداتا مفيش حد بره الكلاس يعرف يعدل عليها
3) **Protected** --> بتتورث لكن محدش يعرف ياكسس عليها بره الكلاس
4) **Default** سبتها فاضيه و محطتش

***طب ازاي اعمل Access علي الداتا و هي برايفت ؟***

الطريقه القديمه بتاعت اني اعمل اوبجيكت من الكلاس و استعمله بدوت مش هتنفع لو الداتا برايفيت 

الحل اننا نشوف وسيط "*Proxy*" , بدلاً من التعامل مع المتغير مباشرة، نصنع دالتين لكل متغير:

1) Setter : دالة لـ "تعديل" القيمة (Modify/Write).
2) Getter : دالة لـ "قراءة" القيمة (Access/Read).

```java
package com.mycompany.test;

// الملف الواحد لا يمكن أن يحتوي إلا على كلاس بابلك واحد بنفس اسم الملف علشان كده هنشيل بابلك من هنا او نحطه في ملف لوحده
 class Car 
{
    private int speed = 120;
    
    // set , Void as it will not return value to me < i want to just modifiy the value not get
    public void setspeed(int speed)
    {
        this.speed = speed;
    }
    
    // get , will return the variable نوعها هيبقي نوع المتغير اللي هترجعه
    public int getspeed()
    {
        return speed;
    }
}

public class TEST
{    
   public static void main(String []args)
   {
      Car C1 = new Car();
      // System.out.println(C1.speed); No Willnot Run In This Way
      C1.setspeed(100); // set speed to 100
      System.out.println(C1.getspeed()); // get speed >> 100
   }
}
```

***يعني انا كل متغير برايفيت هقعد اعمل دالتين , موضوع بطي جدا يعني***

لا تقلق في اي مكان فاضي في الكلاس يكون بره اي سكوب لداله يكون مكان فاضي حرفيا و دوس كليك يمين و اختار ***Insert Code*** او ***Generate Code*** اختارها و اختار انه يعملك سيت و جيت و اختار المتغيرات و شكرا 
نفس الحوار مع ال Constructor للتسريع

### Example علي اللي فات :

#### **📌 Bank_Account – Requirements 

**Class Name:** `Bank_Account`  
**Attributes (private):**

- `String Owner_Name`
    
- `double Balance`
    

**Constructor:**

- Takes (`String Owner_Name`, `double Balance`) and initializes them.
    

**Methods:**

- `String getOwner_Name()`
    
- `double getBalance()`
    
- `void setOwner_Name(String newName)`
    
- `void deposit(double amount)` → increases balance
    
- `void withdraw(double amount)` → decreases balance
    

**Main Method Tasks:**

- Create: `Bank_Account acc = new Bank_Account("Mohammed", 500);`
    
- Call: `deposit(200);`, `withdraw(100);`
    
- Print owner name + final balance.

#### ***Solution*** 

```java
package com.mycompany.test;

class Bank_Account
{
    private String Owner_Name;
    private double Balance;
    
    public Bank_Account(String Owner_Name , double Balance)
    {
        this.Owner_Name = Owner_Name;
        this.Balance = Balance;
    }

    public String getOwner_Name() {
        return Owner_Name;
    }

    public double getBalance() {
        return Balance;
    }

    public void setOwner_Name(String Owner_Name) {
        this.Owner_Name = Owner_Name;
    }
    
    public void deposit(double amount)
    {
        Balance+= amount;
    }
    public void withdraw(double amount)
    {
        Balance-= amount;
    }
}

public class TEST
{    
   public static void main(String []args)
   {
      Bank_Account acc = new Bank_Account("Mohammed" , 500);
      
      acc.deposit(200);
      acc.withdraw(100);
      
       System.out.println(acc.getOwner_Name());
       System.out.println(acc.getBalance());
   }
}
```



## 2) Inheritance:

مبدئ الوراثه لمنع تكرار الكود , زي لما يكون عندك **Class رئيسي (Parent / Superclass)**، وعايز تعمل **Class جديد (Child / Subclass)** ياخد كل حاجة من الـ Parent ويضيف حاجات جديدة.

***نوت --> في جافا مينفعش كلاس واحد يورث من كلاسين تانيين عكس ال C++ ,  جافا بتدعم ال Single Inheritance مش ال multi Inheritance***

طيب ازاي نستعمل ده في الكود؟
باجي جنب الكلاس نيم و بكتب ***extends*** و جنبها اسم الكلاس اللي هياخد صفاته 

```java
package com.mycompany.test;

class Father
{
    private String Name = "Mahmoud";
    private String Home;

    public String getName() {
        return Name;
    }

    public void setName(String Name) {
        this.Name = Name;
    }
    
    public String getHome() {
        return Home;
    }

    public void setHome(String Home) {
        this.Home = Home;
    }
    
}

class Boy extends Father
{
    private char Garde  = 'A';

    public char getGarde() {
        return Garde;
    }

    public void setGarde(char Garde) {
        this.Garde = Garde;
    }
    
}

public class TEST
{    
   public static void main(String []args)
   {
      Boy B1 = new Boy();
       System.out.println(B1.getGarde());
      B1.setHome("AZbt Elwalda city");
       System.out.println( B1.getHome());
       System.out.println(B1.getName());
       
   }
}
```

***--> Notes:***
1) Use Final To Prevent Inheritance From the class **-Write it Before word class-**
2) ***Constructor Do Not Inherited But Invoked*** , _***Look***_ at the next example to understand
```JAVA
package com.mycompany.test;

import java.util.Scanner;

class Vehicle {

    String Brand;

    public Vehicle(String Brand) {
        this.Brand = Brand;
        System.out.println("1. Vehicle (Parent) Built : " + Brand);
    }
}

class Car extends Vehicle {

    int Doors;

    public Car(String Brand, int Doors) {
        super(Brand); // MUST BE FIRST LINE
        this.Doors = Doors;
        System.out.println("2. Car (Chiled) Built with : " + Doors + "Doors");
    }
}

public class TEST {

    public static void main(String args[]) {
        Car myCar = new Car("KIA", 4);
//        1. Vehicle(Parent) Built: KIA
//        2. Car(Chiled) Built with : 4Doors
    }
}
```


#### ***Method Overriding :***

هو ان الكلاس الابن ياخد داله (Method) من السوبر كلاس لكن يغير فيها و يعمل نسخه خاصه بيه معدله
ازاي اعمل

*--> ازاي؟*
1) لازم يكون في وراثه 
2) خد نفس الداله اللي هتعملها اوفر رايد من السوبركلاس و حطها كوبي في الكلاس اللي عايزه
3) **@Override** فوق الداله (مش الزامي لكن احسن)
4) عدل فيها براحتك
   
***Note :***
"عندما تقوم الفئة الفرعية (Subclass) بعمل Override لدالة موجودة في الفئة الأساسية (Superclass)، بنقدر لسه ننادي الدالة الأصلية باستخدام كلمة super.  
لو كتبنا super.funcName() ده بينادي تنفيذ الدالة اللي في الـ Superclass مش اللي في الـ Subclass."

من الاخر --> علشان أنادي الفانكشن الأصلية بستخدم:  
super.funcName()
   
##### Example :
```java
package com.mycompany.test;
import java.util.Scanner;

class Father
    {
        public void whoAmI()
        {
            System.out.print("I'm Father");
        }
    }
class Boy extends Father
    {
        @Override
        public void whoAmI()
        {
            super.whoAmI(); // بستدعي القديم و هزود عليه
            System.out.println(" , No I'm Jokking I'm a Boy");
        }
    }
class Girl extends Father
    {
        @Override
        public void whoAmI()
        {
            System.out.println("I'm Girl");
        }
    }
    
public class TEST
{
    
    public static void main(String args[])
    {
        Girl G1 = new Girl();
        Boy B1 = new Boy();
        Father F1 = new Father();
        
        G1.whoAmI();// I'm Girl
        B1.whoAmI();// I'm Father , No I'm Jokking I'm a Boy
        F1.whoAmI();// I'm Father
    }
}
```




## 3) Abstraction:

Hiding The Implementation of a Function .
#### Abstract Class:
هو كلاس لا يمكن انشاء كائنات منه , بيبقي في دوال عاديه باكواد و دوال غير مكتمله الكلاسات التانيه اللي هتورثه هتكملها 
علشان اخلي الكلاس Abstract هكتب قبل كلمه كلاس abstract
لو عملت داله غير مكتمله هكتب قبلها abstract ---> مثال abstract void calcArea(); // abstract method 

علشان اكمل الداله في الكلاس اللي هيورث هعمل اوفررايد 

```JAVA
package com.mycompany.test;
import java.util.Scanner;

abstract class Shape
    {
        String color;
        // Costractor عادي
        public Shape(String color)
        {
            this.color = color;
        }
        public void showColor()
        {
            System.out.println("Shape Color:" + this.color);
        }
        public abstract double calcArea();
    }
class Circle extends Shape
    {
        double r;

    public Circle(double r, String color) {
        super(color);
        this.r = r;
    }
    @Override
    public double calcArea()
    {
        return 3.14 * r * r;
    }
    }
    
public class TEST
{
    
    public static void main(String args[])
    {
        Circle C1 = new Circle(5 , "Red");
        System.out.println("AREA IS:" + C1.calcArea());
    }
}
```

#### Interface: 
هو عقد بيقول لاي كلاس هيورث منه انه لازم ينفذ كل الدوال اللي هو اعلنها
***--> خصائصه:***
1) لازم تدي المتغيرات فيه قيم
2) اي متغير فيه باي ديفولت بابليك و ستاتيك و فاينال
3) اي فانكشن فيه ناقصه Abstract
4) مينفعش تعمل منه كائنات
5) اي كلاس بيورث منه عن طريق كلمه ***implements*** 

```java
package com.mycompany.test;
import java.util.Scanner;

interface Animal 
{
    public void drink();
    public void walk();
} 

class Dog implements Animal
{
    @Override
    public void drink()
    {
        System.out.println("Dog Is Drinking");
    }
    public void walk()
    {
        System.out.println("Dog Is Walking");
    }
}
// تقدر تورث من كلاس كمان عادي و تعمل ايمبليمينت تاني  كومه و اسم الانترفيس التاني
class Cat implements Animal
{
    public void drink()
    {
        System.out.println("Cat Is Drinking");
    }
    public void walk()
    {
        System.out.println("Cat Is Walking");
    }
}

public class TEST
{
    
    public static void main(String args[])
    {
        Dog D1 = new Dog();
        D1.drink(); // Dog Is Drinking
        
        Cat C1 = new Cat();
        C1.walk(); // Cat Is Walking
    }
}
```

## 4) Polymorphism:
ترجمته الحرفيه هي تعدد الاشكال
***Method Overriding :*** شرحتها في التوريث
***Method Overloading :*** دالتين بنفس الاسم لكن مدخلات مختلفه

