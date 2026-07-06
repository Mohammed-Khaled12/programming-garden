# 🧬 Object-Oriented Thinking (OOP)

> [!ABSTRACT] 📝 Note Context
> * **Type:** Hybrid Note (Concept + Implementation).
> * **Core Topic:** The 4 Pillars of OOP (Encapsulation, Inheritance, Polymorphism, Abstraction).
> * **Language Used:** Java ☕ (for demonstration) , Lately C++.
>
> *Note: Although written in Java, these concepts apply to C++, Python, and C#.*

Tags: #concept #oop #java #fundamentals

---
# YouTube Playlist
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

![[Pasted image 20260702164000.png]]


## Constructor :
هو فانكشن خاصه جوه ال Class بتشتغل لحظه ما تعمل Object و هي اللي بتعمل ال Object و تجهزه بالقيم اللي انت عايزها 
و هنتكلم عنه اكتر تاني قدام 



# More Details With Codes : 

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
#### النوع الرابع:  Copy Constructor

ده بياخد Object تاني (مبني وجاهز) كـ Parameter، وينسخ كل بياناته للـ Object الجديد اللي إنت لسه بتعمله.

#### Destructor:

دي دالة بتشتغل **لوحدها أوتوماتيك** في نهاية حياة الـ Object (يعني لما البرنامج يخلص أو الفانكشن اللي الـ Object جواها تقفل).

- **وظيفته:** "التنظيف". لو الـ Object ده كان حاجز مساحة معينة في الميموري (Dynamic Memory)، أو ماسك سلاح، الـ Destructor بياخد الحاجات دي يفضيها ويرجعها للسيستم عشان الرامات ماتتمليش وتضرب.

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

```c++
#include <iostream>
#include <string>

class Character {
private:
    std::string m_name;
    int m_health;

public:
    // 1. Default Constructor
    Character() {
        m_name = "Unknown Player";
        m_health = 100;
        std::cout << m_name << " is created with Default Constructor.\n";
    }

    // 2. Parameterized Constructor
    Character(std::string name, int health) {
        m_name = name;
        m_health = health;
        std::cout << m_name << " is created with Parameterized Constructor.\n";
    }

    // 3. Copy Constructor
    Character(const Character& other_character) {
        m_name = other_character.m_name + " (Clone)";
        m_health = other_character.m_health;
        std::cout << m_name << " is created using Copy Constructor.\n";
    }

    // 4. Destructor (دايماً بيبدأ بعلامة ~)
    ~Character() {
        std::cout << m_name << " is destroyed! Cleaning up memory...\n";
    }
};

int main() {
    // هيشغل الـ Default
    Character player1; 
    
    // هيشغل الـ Parameterized
    Character player2("Geralt", 500); 
    
    // هيشغل الـ Copy (بياخد نسخة من player2)
    Character player3(player2); 

    std::cout << "--- End of Game ---\n";
    
    return 0;
    // أول ما الـ return 0 تشتغل، الـ Destructors للـ 3 شخصيات هتشتغل وتمسحهم
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
3) Read Only: اعمل للفاريابل جت بس و متعملوش ست

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
```java
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


### Inheritance in C++ :
مفيش كلمه `etends` بنستعمل مكانها السينتاكس ده علشان نورث
```cpp
class clsEmployee : public clsPerson
```
كده clsPerson ورث من clsEmployee ايه بقي حوار public دي ؟ دي اسمها Inheritance Visibility Modes

1) Public: 
وراثه صريحه عاديه زي بتاعت جافا
- الـ `public` في الأب بيفضل `public` في الابن (اليوزر يقدر يشوفه في الـ `main`)
- الـ `protected` في الأب بيفضل `protected` في الابن.
2) protected: 
**إيه اللي بيحصل؟** أي حاجة الأب كان سايبها للناس بره (`public`)، الابن هيورثها ويخفيها جواه ويخليها `protected`.
- - الـ `public` في الأب بينزل يبقى `protected` في الابن.
- الـ `protected` في الأب بيفضل `protected` في الابن.

1) private: 
**إيه اللي بيحصل؟** دي وراثة "مقفولة". الابن بيورث حاجات الأب كلها، وبيخليها `private` لنفسه بس.
- الـ `public` والـ `protected` في الأب بينزلوا يبقوا `private` في الابن.
  
***Note*** : No `super` keyword in C++ , Use `Initializer List` instead 
```cpp
#include <iostream>
using namespace std;

// Base Class (Parent)
class Vehicle
{
protected:
    string _Brand;

public:
    // Parent Parameterized Constructor
    Vehicle(string Brand)
    {
        _Brand = Brand;
        cout << "1. Vehicle (Parent) Built: " << _Brand << "\n";
    }
};

// Derived Class (Child) inheriting publicly from Vehicle
class Car : public Vehicle
{
private:
    int _Doors;

public:
    // Child Constructor
    // The ": Vehicle(Brand)" part is the Initializer List.
    // It is the EXACT C++ equivalent of calling "super(Brand);" in Java or C#.
    // It MUST be called before the opening brace '{' of the child's constructor.
    Car(string Brand, int Doors) : Vehicle(Brand)
    {
        _Doors = Doors;
        cout << "2. Car (Child) Built with: " << _Doors << " Doors\n";
    }
};

int main()
{
    // Output will show Parent building first, then Child.
    Car myCar("KIA", 4);

    return 0;
}
```

![[Pasted image 20260706115214.png]]
#### إزاي تعمل Overriding في C++؟

لازم يكون في وراثة (باستخدام `: public`). نفس الشروط بتاعت جافا بالضبط، بس بنغير شوية مسميات:

1. **الشرط الإضافي في C++ (تصريح الأب):** في جافا، أي دالة ينفع يتعملها Override تلقائياً. في C++ لأ! لازم الأب يدي تصريح إن الدالة دي ينفع تتعدل، عن طريق إنه يكتب كلمة `virtual` قبلها. لو مكتبتش `virtual`، الكومبايلر هيعتبرك بتعمل Hiding مش Overriding.
    
2. **بديل `@Override`:** في C++ بنكتب كلمة `override` في **آخر الدالة** مش فوقها. (وهي برضه مش إلزامية بس بتنقذك لو كتبت اسم الدالة غلط).
    
3. **بديل `super.funcName()`:** زي ما اتفقنا قبل كده، C++ مفهاش كلمة `super`. البديل بتاعها إنك تنادي اسم كلاس الأب مباشرة متبوع بـ `::` كده: `ParentName::funcName()`.
```c++
#include <iostream>
using namespace std;

class Father 
{
public:
    // 1. كلمة virtual هنا بتسمح للأبناء يعملوا Override
    virtual void whoAmI() 
    {
        cout << "I'm Father";
    }
};

class Boy : public Father 
{
public:
    // 2. كلمة override بتتكتب في الآخر
    void whoAmI() override 
    {
        // 3. ده البديل بتاع super.whoAmI()
        Father::whoAmI(); 
        cout << " , No I'm Joking I'm a Boy\n";
    }
};

class Girl : public Father 
{
public:
    void whoAmI() override 
    {
        cout << "I'm Girl\n";
    }
};

int main() 
{
    Girl G1;
    Boy B1;
    Father F1;
    
    G1.whoAmI(); // I'm Girl
    B1.whoAmI(); // I'm Father , No I'm Joking I'm a Boy
    F1.whoAmI(); // I'm Father
    
    return 0;
}
```
##### يعني إيه Function Hiding ؟
الـ Hiding بيحصل لما كلاس الابن يعمل دالة بنفس اسم دالة موجودة في كلاس الأب، بس **من غير** ما الأب يكون كاتب قبلها كلمة `virtual`. هنا الابن "بيخبي" دالة الأب. كأن الأب كان معلق يافطة مكتوب عليها `Print`، فجه الابن لزق فوقيها يافطة تانية بتاعته مكتوب عليها `Print`. اليافطة القديمة لسه موجودة تحت، بس اللي بيبص على الابن مش هيشوف غير اليافطة الجديدة.
الفرق بينه و بين ال Overriding مبيظهرش لو إنت بتستخدم الكلاس بشكل مباشر وعادي (`Child c; c.speak();`). في الحالتين هيطبع كلام الابن. الفرق والكارثة بتظهر **لما نستخدم الـ Pointers **.
###### بيتعمل إزاي؟ (How it's done)
عادي جداً، مجرد إنك تكتب الدالة بنفس الاسم في الابن، من غير أي كلمات مميزة 
```c++
class Parent {
public:
    // دالة عادية جداً مفيش قبلها virtual
    void speak() { 
        cout << "I am the Parent\n"; 
    }
};

class Child : public Parent {
public:
    // الابن عمل دالة بنفس الاسم.. ده هو الـ Hiding!
    void speak() { 
        cout << "I am the Child\n"; 
    }
};
```

#### Up Casting vs Down Casting : 

**Up Casting:** تحويل الابن لأب
**Down Casting:** تحويل الأب لابن
```c++
#include <iostream>
using namespace std;

class Person 
{
public:
    // ملحوظة خطيرة: الـ Down Casting مش هيشتغل في C++ أصلاً إلا لو الأب فيه دالة virtual
    virtual void Print() { cout << "I am a Person\n"; }
};

class Employee : public Person 
{
public:
    void Print() override { cout << "I am an Employee\n"; }
    
    // دالة خاصة بالابن بس
    void GetSalary() { cout << "Salary is 5000\n"; } 
};

int main() 
{
    // ==========================================
    // 1. Up Casting (Safe)
    // ==========================================
    Employee emp;
    
    // ريموت أب بيشاور على أوبجيكت ابن (بيحصل أوتوماتيك)
    Person* ptrPerson = &emp; 
    
    ptrPerson->Print(); // هيطبع دالة الموظف لأنها virtual
    // ptrPerson->GetSalary(); // Error! ريموت الأب مفيش فيه زرار المرتب

    // ==========================================
    // 2. Down Casting (Risky)
    // ==========================================
    // عايزين نرجع نستخدم ريموت الابن عشان نوصل للمرتب
    // بنستخدم أداة اسمها dynamic_cast عشان نتأكد إن التحويل أمان
    
    Employee* ptrEmp = dynamic_cast<Employee*>(ptrPerson);

    // بنسأل: هل التحويل نجح؟ (يعني هل الشخص ده فعلاً طلع موظف متخفي؟)
    if (ptrEmp != nullptr) 
    {
        cout << "Down Casting Successful!\n";
        ptrEmp->GetSalary(); // اشتغلت أمان جداً
    } 
    else 
    {
        // لو الـ ptrPerson كان بيشاور على Person بجد مش Employee، التحويل هيفشل
        cout << "Down Casting Failed! This is not an Employee.\n";
    }

    return 0;
}
```
الأب يسمح له بالإشارة الى ابنه بينما الابن من قلة الاحترام وممنوع ان يؤشر على ابيه
![[Pasted image 20260706121727.png]]
#### Virtual Functions:

الـ **Virtual Functions** هي مجرد "أمر" بتكتبه للكومبايلر عشان يغير الطريقة اللي بيختار بيها الدالة اللي هتتنفذ.

عشان تفهمها، لازم تفهم إزاي الكومبايلر بيفكر الأول.

##### المشكلة: الكومبايلر متسرع (Early Binding)

تخيل إنك بتبني برنامج زي Word، وعندك كلاس أساسي اسمه `Document`، وكلاس تاني بيورث منه اسمه `PdfDocument`. الاتنين جواهم دالة اسمها `Export()`.

لو عملت كده:
```c++
Document* docPtr = new PdfDocument(); 
docPtr->Export();
```
**إيه اللي هيحصل هنا؟** الكومبايلر بيقرر هو هيشغل أنهي دالة **وقت الترجمة (Compile Time)**. بيبص للسطر ده ويقول: "الـ Pointer ده نوعه `Document`، إذن أنا هربط السطر ده بدالة الـ `Export` بتاعة الـ `Document`، ومش مهم إيه اللي هيتحط في الميموري بعدين".

النتيجة؟ هيطبع دالة الأب الأساسية، وهيتجاهل تماماً كود الـ PDF اللي إنت تعبت فيه. الميكانيزم ده اسمه **Static Binding** أو الربط المبكر.

##### الحل: كلمة `virtual` (Late Binding)

هنا بييجي دور الـ Virtual Function. لما بتروح لكلاس الأب (`Document`) وتكتب كلمة `virtual` قبل الدالة:
```c++
virtual void Export();
```
إنت هنا بتدي للكومبايلر أمر هندسي صريح: **"ماتتسرعش وماتقررش الدالة وقت الكومبايل. استنى لحد ما البرنامج يشتغل (Run Time)، وروح بص بنفسك جوه الميموري شوف الـ Object الحقيقي اللي متخزن هناك نوعه إيه، وشغل الدالة بتاعته."**
الميكانيزم ده اسمه **Dynamic Binding** أو الربط المتأخر.
```c++
#include <iostream>
using namespace std;
class clsPerson
{
public:
    virtual void Print()
    {
        cout << "Hi, i'm a person!\n ";
    }
};
class clsEmployee : public clsPerson
{
public:
    void Print()
    {
        cout << "Hi, I'm an Employee\n";
    }
};
class clsStudent : public clsPerson
{
public:
    void Print()
    {
        cout << "Hi, I'm a student\n";
    }
};
int main()
{
    clsEmployee Employee1;
    clsStudent Student1;
    Employee1.Print(); // Hi, I'm an Employee
    Student1.Print();  // Hi, I'm a student
    clsPerson *Person1 = &Employee1;
    clsPerson *Person2 = &Student1;
    Person1->Print(); // Hi, I'm an Employee
    Person2->Print(); // Hi, I'm a student
    // لو مكانش فيه كلمه فيرتشوال كان هيطبع بتاعت البيرسون الفيرتشوال قالتله يبص علي الاوبجيكت نفسه في الران تايم فيه ايه اللي هو قالتله شوف عيالك من الاخر
    return 0;
}
```
إزاي الـ C++ بتعرف نوع الـ Object في الميموري وقت التشغيل؟ أول ما بتكتب كلمة `virtual` في أي كلاس، الكومبايلر بيخلق جدول مخفي في الميموري اسمه **V-Table (Virtual Table)**. الجدول ده عبارة عن خريطة (Array of Function Pointers). كل Object بيتخلق بيبقى جواه Pointer خفي بيشاور على الجدول بتاعه. لما بتيجي تنادي الدالة، البرنامج بيروح للجدول ده، والجدول هو اللي بيوجهه للكود الصح في الميموري.

عشان كده، الـ Virtual Functions بتستهلك جزء بسيط جداً زيادة من الميموري (بسبب الجدول ده) وجزء بسيط من وقت المعالج، بس في المقابل بتديك مرونة هندسية مستحيل تبني سيستم كبير من غيرها.

###  Code Exercise (Cloud Server Manager):
#### Requirements 

 1. الـ Base Class (الأب): `clsServer`

- **Static Members & Methods:**
    
    - اعمل متغير `Static` اسمه `TotalServersCount` بيبدأ بـ 0.
        
    - اعمل `Static Method` بترجع الرقم ده.
        
- **Encapsulation & Properties:**
    
    - متغيرات خاصة (Private): `_IPAddress` (String) و `_RAM` (int).
        
    - اعمل للـ `_IPAddress` خاصية **Read-Only Property** (يعني دالة Get بس، مينفعش يتعدل بعد ما السيرفر يتكريت).
        
    - اعمل للـ `_RAM` خصائص **Set و Get**.
        
- **Constructors & Destructors:**
    
    - اعمل **Parameterized Constructor** بياخد الـ IP والـ RAM، وبيزود الـ `TotalServersCount` بواحد.
        
    - اعمل **Destructor** بيطبع رسالة إن السيرفر اللي الآي بي بتاعه كذا بيتعمله Shut down، وبينقص الـ `TotalServersCount` بواحد.
        
- **Abstraction:**
    
    - اعمل دالة مخفية (Private) اسمها `_CheckHardware()` بتطبع بس سطر "Checking CPU and RAM...".
        
- **Virtual Functions:**
    
    - اعمل دالة `public` اسمها `Boot()` (خليها جاهزة للـ Overriding). الدالة دي بتنادي جواها على `_CheckHardware()` وبعدين تطبع: "Server [IP] is booting up...".
        

 2. الـ Derived Class الأول (الابن): `clsDatabaseServer`

- بيورث من `clsServer`.
    
- عنده متغير خاص بيه اسمه `_DBType` (مثلاً MySQL أو MongoDB).
    
- اعمل **Constructor** بياخد (IP, RAM, DBType)، ويباصي الـ IP والـ RAM لـ **Constructor بتاع الأب**.
    
- اعمل **Function Overriding** لدالة `Boot()`: خليها تطبع الأول بيانات الـ `clsServer` الأصلية (عشان نطبق الـ DRY ولا تكرر الكود)، وبعدين تطبع سطر زيادة: "Starting Database Engine: [DBType]".
    

 3. الـ Derived Class التاني (الابن): `clsWebServer`

- بيورث من `clsServer`.
    
- عنده متغير خاص بيه اسمه `_DomainName`.
    
- اعمله Constructor زي اخوه بيباصي للأب.
    
- اعمل **Overriding** لدالة `Boot()`: تطبع بيانات الأب، وبعدين تطبع: "Hosting Domain: [DomainName]".
    

 4. الـ Main Function (الوحش: Up Casting & Late Binding)

- كريت Object من `clsDatabaseServer` و Object من `clsWebServer`.
    
- اعمل **مصفوفة (Array) من الـ Pointers من نوع الأب `clsServer*`** حجمها 2.
    
- خلي البوينتر الأول يشاور على سيرفر الداتابيز، والبوينتر التاني يشاور على سيرفر الويب (ده هو الـ **Up Casting**).
    
- اعمل `For Loop` بتلف على المصفوفة دي، وبتنادي دالة `Boot()` لكل بوينتر (عشان نختبر الـ **Dynamic/Late Binding** ونشوف هيطبع صح ولا لأ).
    
- في آخر الـ main، اطبع الـ Static method عشان نشوف عدد السيرفرات اللي شغالة كام.

#### Solution
```c++
#include <iostream>
using namespace std;

class Server
{
private:
    int _RAM;
    string _IPAddress;
    void _CheckHardware()
    {
        cout << "Checking CPU and RAM...\n";
    }

public:
    static int TotalServersCount;
    static int ServersCount()
    {
        return TotalServersCount;
    }

    string GetIPAdderss()
    {
        return _IPAddress;
    }
    int GetRAM()
    {
        return _RAM;
    }
    void SetRAM(int RAM)
    {
        _RAM = RAM;
    }

    Server(string IPAddress, int RAM) : _IPAddress(IPAddress), _RAM(RAM)
    {
        TotalServersCount++;
    }
    ~Server()
    {
        cout << "Server with IP: " << _IPAddress << " is Shutting Down \n";
        TotalServersCount--;
    }
    virtual void Boot()
    {
        _CheckHardware();
        cout << "Server " << _IPAddress << " is booting up...\n";
    }
};

int Server::TotalServersCount = 0;

class DatabaseServer : public Server
{
private:
    string _DBType;

public:
    DatabaseServer(string IPAddress, int RAM, string DBType)
        : Server(IPAddress, RAM), _DBType(DBType)
    {
    }
    void Boot() override
    {
        Server::Boot();
        cout << "Starting Database Engine: " << _DBType << " \n";
    }
};

class WebServer : public Server
{
private:
    string _DomainName;

public:
    WebServer(string IPAddress, int RAM, string DomainName)
        : Server(IPAddress, RAM), _DomainName(DomainName)
    {
    }

    void Boot() override
    {
        Server::Boot();
        cout << "Hosting Domain: " << _DomainName << "\n";
    }
};

int main()
{

    DatabaseServer DB("192.1.1.168", 128, "MongoDB");
    WebServer Web("192.1.1.169", 256, "algoforge.ai");

    Server *Pointer[2];
    Pointer[0] = &DB;
    Pointer[1] = &Web;

    // for (Server i : Pointer)
    // {
    //     *Pointer[i]->Boot(); XXXXXX Wrong
    // }

    // for (Server* i : Pointer)
    // {
    //     i->Boot(); Right
    // }

    for (int i = 0; i < 2; i++)
    {
        Pointer[i]->Boot();
    }

    return 0;
}
```
## 3) Abstraction:

Hiding The Implementation of a Function .
#### Abstract Class:
هو كلاس لا يمكن انشاء كائنات منه , بيبقي في دوال عاديه باكواد و دوال غير مكتمله الكلاسات التانيه اللي هتورثه هتكملها 
علشان اخلي الكلاس Abstract هكتب قبل كلمه كلاس abstract
لو عملت داله غير مكتمله هكتب قبلها abstract ---> مثال abstract void calcArea(); // abstract method 

علشان اكمل الداله في الكلاس اللي هيورث هعمل اوفررايد 

```java
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

---
# Extras:

## 1) Static Member

في العادي، أي متغير (Member Variable) بتكتبه جوه الـ Class، كل ما تعمل Object جديد، الـ Compiler بيحجزله مساحة خاصة بيه في الميموري. يعني لو عندك متغير اسمه `m_health`، وعملت 10 شخصيات، هيبقى عندك 10 نسخ من `m_health` في الميموري، كل شخصية ليها النسخة بتاعتها اللي مفيش شخصية تانية تقدر تلمسها.

![[Pasted image 20260702164000.png]]

**لكن الـ Static Member حاجة تانية خالص:** ده متغير **مشترك** بين كل الـ Objects اللي طالعة من نفس الـ Class. الـ Compiler بيحجزله مكان **واحد بس** في الميموري، وكل الـ Objects بتبص عليه وتعدل فيه هو هو. الـ Static Member ده مِلك للـ Class نفسه، مش مِلك للـ Object.
```cpp
class nums
{
  static int x;
}
```
الاهم متنساش تعمله Initialization تاني تحت الكلاس 

```cpp
int nums::x = 0;
```

```cpp
#include <iostream>
#include <string>

class Character {
private:
std::string m_name;
int m_health;

public:
// ده Static Member (عداد مشترك لكل الشخصيات)
static int player_count;

// Constructor
Character(std::string name, int health) {
m_name = name;
m_health = health;
// كل ما شخصية جديدة تتخلق، نزود العداد المشترك
player_count++;
}

~Character() {
// كل ما شخصية تموت، ننقص العداد المشترك
player_count--;
}

};
// القاعدة الذهبية في C++: الـ Static Members لازم يتعملها Initialization بره الـ Class
int Character::player_count = 0;

int main() {
// Class لسه معملناش أي شخصيات، بس نقدر نوصل للعداد عن طريق اسم الـ
std::cout << "Initial Players: " << Character::player_count << "\n"; // هيطبع 0

Character p1("Geralt", 100);
Character p2("Ciri", 100);

// دلوقتي العداد زاد مرتين
std::cout << "Current Players: " << Character::player_count << "\n"; // هيطبع 2

// الـ Objects ممكن برضه تشوف المتغير ده، وهتلاقيه نفس الرقم

std::cout << "P1 sees player count as: " << p1.player_count << "\n"; // هيطبع 2
std::cout << "P2 sees player count as: " << p2.player_count << "\n"; // هيطبع 2

return 0;
}
```

## 2) Static Function:
زى ما الـ Static Variables مشتركة بين كل الـ Objects، الـ **Static Methods** (أو Functions) هى كمان دوال مِلك للـ Class نفسه، مش مِلك لـ Object بعينه.
عشان تستخدم دالة (Method) عادية جوه الكلاس، لازم الأول تعمل Object، وبعدين تنادى الدالة دى من خلاله. يعنى لو الدالة اسمها `jump()`، لازم تعمل `Character p1;` وبعدين تقول `p1.jump();`.

لكن لو الدالة `static`، إنت مش محتاج تعمل Object أصلاً. الدالة دى شغالة وموجودة على مستوى الكلاس نفسه، وتقدر تناديها مباشرة كده: `Character::print_game_rules();`.

```cpp
#include <iostream>
using namespace std;

class clsA
{
public:
static int Function1()
{
return 10;
}
int Function2()
{
return 20;
}
};
int main()
{
// The following line calls static function directly via class not through the object
// At class level you can call only static methods and static members
cout << clsA::Function1() << endl;
// static methods can also be called throught the object.
clsA A1, A2;
cout << A1.Function1() << endl;
cout << A1.Function2() << endl;\
cout << A2.Function1() << endl;
}
```

الـ Static Method **ممنوع منعاً باتاً** إنها تتعامل مع أي متغير عادي (Non-Static) جوه الكلاس (زي `m_name` أو `m_health`).

**ليه؟** لأن المتغيرات العادية مابتتخلقش غير لما تعمل Object. لكن الدالة الـ `static` شغالة حتى لو مفيش Objects خالص. فلو الدالة الـ `static` حاولت تقرأ `m_name`، الكومبايلر هيقولها: "أنا معرفش إنتي بتتكلمي عن ياتا `m_name`؟ بتاع `player1` ولا `player2`؟ الداتا دي مش موجودة عندي دلوقتي!".

- **الخلاصة:** الـ Static Method تقدر بس تقرأ وتعدل في الـ **Static Variables** اللي زيها.