class naming convention in python is `PascalCase`


When Creating Object Python Looks For The Built In `__init__` Method
`__init__` Method Called Every Time You Create Object From Class
`__init__` Method Is Initialize The Data For The Object
Any Method With Two Underscore in The Start and End Called Dunder or Magic Method

- **في C++:** عشان تعمل Object جديد في الـ Heap، كنت بتكتب كده: `Member* member_one = new Member();`
    
- **في بايثون:** مفيش Pointers صريحة ومفيش `new`. كل الـ Objects في بايثون بتتكريت في الـ Heap أوتوماتيك (والـ Garbage Collector بينضفها). عشان تاخد نسخة، بتكتب اسم الكلاس كأنه دالة: `member_one = Member()`

- **في C++:** الـ Constructor هو دالة بتاخد **نفس اسم الكلاس** (يعني `Member()`)، وبتشتغل أوتوماتيك أول ما تاخد Object.
    
- **في بايثون:** بايثون معندهاش حتة إن الدالة تتسمى باسم الكلاس. الـ Constructor في بايثون اسمه دايماً **`__init__`** (اختصار لـ Initialization). الدالة دي بايثون بتنادي عليها أوتوماتيك فوراً أول ما بتكتب `Member()`.

- **في C++:** إنت عارف الـ Pointer الخفي اللي اسمه `this->`، اللي بيشاور على الـ Object الحالي، والكومبايلر بيبعته في الكواليس لأي Member Function من غير ما إنت تكتبه في الـ Parameters.
    
- **في بايثون:** مبدأ بايثون هو "الوضوح أحسن من الخفاء". عشان كده بايثون **بتجبرك** تكتب الـ `this` بإيدك كأول مدخل (Parameter) في أي دالة جوه الكلاس، بس في بايثون بنسميه **`self`**.
    
    -  لازم أول Parameter في كل دالة (Instance Method) جوه الكلاس يكون `self`. ولما بتيجي تستدعي الدالة إنت مش بتبعت الـ `self`، بايثون بتبعته مكانك أوتوماتيك!
        
    -  معلومة بيقولك إنك برمجياً تقدر تسميه أي اسم (يعني تقدر تسميه `this` لو حابب)، بس كـ Standard بين كل مبرمجين بايثون لازم تسميه `self`.

```python
class Member:
    def __init__(self):
        pass

    def print_num(self, num):
        print(num)


member_one = Member() # Create an Object
member_one.print_num(4) # 4
```

لما إنت بتكتب `member_one = Member()`، بايثون في الكواليس بتشغل دالتين ورا بعض:

1. دالة سحرية اسمها **`__new__`**: دي اللي بتعادل كلمة `new` في C++، وظيفتها تحجز مكان للكائن في الـ Heap الميموري وترجعه فاضي.
    
2. دالة **`__init__`**: بتاخد الكائن الفاضي ده (عن طريق `self`) وتبدأ تحط جواه الداتا.
    

نحن كمبرمجين بايثون، بنسبة 99% مبنكتبش `__new__` خالص وبنسيب بايثون تحجز الميموري براحتها، وكل شغلنا بيكون في الـ `__init__` عشان نظبط البيانات.

علشان تاكسس اتربيوت (فاريابل) من بتوع الكلاس لازم تكتب `classname.attr` حتي لو انت جوه الكلاس نفسه او `self.attr`
في C++، إنت ممكن جوه الدالة تكتب اسم المتغير حاف كده `name = "Ahmed";` والكومبايلر ذكي كفاية إنه يقول "أه، هو يقصد `this->name`".

في بايثون، القاعدة الصارمة هي **"الوضوح أحسن من الخفاء" (Explicit is better than implicit)**. لو كتبت اسم المتغير حاف من غير `self` أو اسم الكلاس، بايثون هتعتبره **متغير محلي (Local Variable)** خاص بالدالة دي بس، وهترميه في الزبالة (Garbage Collector) أول ما الدالة تخلص!

عشان كده إنت دايماً محتاج تحدد "النطاق" (Scope) بتاع المتغير ده، وهنا بيظهر الفرق بين `self.attr` و `ClassName.attr`، وهما بيمثلوا نوعين مختلفين من المتغيرات (بالظبط زي C++):

### 1. `self.attr` (Instance Attributes - متغيرات الكائن)

دي المتغيرات العادية اللي **خاصة بكل Object لوحده** (زي اسم العضو، سنه).

- بتتعرف دايماً جوه الـ `__init__`.
    
- عشان توصلها (سواء هتقرأها أو تعدلها) من أي دالة جوه الكلاس، **لازم** تستخدم `self.attr`.
    

### 2. `ClassName.attr` (Class Attributes - يعادل الـ Static Variables في C++)

دي المتغيرات **المشتركة بين كل الـ Objects** اللي طالعة من الكلاس ده (زي مثلاً عداد بيحسب إحنا عملنا كام Object لحد دلوقتي).

- بتتعرف بره الـ `__init__` (تحت اسم الكلاس مباشرة).
    
- عشان توصلها أو تعدلها من جوه أي دالة، **يُفضل دايماً** تستخدم اسم الكلاس `ClassName.attr`.

```python
class Member:
    # 1. Class Attribute (Similar to static variables in C++)
    # This variable is shared across all instances of the class
    users_count = 0

    def __init__(self, name):
        # 2. Instance Attribute (Specific to the current object)
        # Using 'self' binds the variable to the current instance
        self.name = name

        # Accessing the Class Attribute requires using the Class Name, not 'self'
        Member.users_count += 1

    def show_info(self):
        # Using 'self' to access the instance attribute
        print(f"My name is {self.name}")

        # Using the Class Name to access the shared class attribute
        print(f"Total members now: {Member.users_count}")


# Testing the code
m1 = Member("Ahmed")
m1.show_info()
# Output: My name is Ahmed | Total members now: 1

m2 = Member("Sara")
m2.show_info()
# Output: My name is Sara | Total members now: 2

```

 هنا إحنا بندخل في مستوى احترافي جداً في بايثون، والموضوع ده بالذات هيعوضك عن حاجة إنت افتقدتها جداً لما سيبت C++ وهي الـ **Method Overloading** (إنك تعمل أكتر من كونستراكتور).

  

الدوال (Methods) جوه الكلاس في بايثون بتتقسم لـ 3 أنواع رئيسية:

1. ال **Instance Methods:** اللي شرحناها، بتاخد `self` وبتتعامل مع بيانات الكائن (Object).
    
      
    
2. **Class Methods:** بتاخد الكلاس نفسه كـ Parameter، وبتتعامل مع بيانات الكلاس (Static Variables).
    
      
    
3. **Static Methods:** مبتاخدش لا `self` ولا كلاس، دي مجرد دالة عادية جداً بس محطوطة جوه الكلاس للتنظيم.
    
      
    

تعالى نفصص الاتنين الجداد ونربطهم بـ C++:

  

### 1. Class Methods (`@classmethod`)

بما إن بايثون بتمنع إنك تعمل أكتر من `__init__` (مفيش Constructor Overloading)، طيب لو أنا عايز أعمل Object بطرق مختلفة؟

مثلاً: مرة أبعتله الاسم والسن عادي `Member("Ahmed", 25)`، ومرة تانية عايز أبعتله نص كامل وهو يفصصه `Member("Ahmed-25")`؟

  

**في C++:** كنت هتعمل 2 Constructors.

**في بايثون:** بنستخدم الـ **Class Method** عشان نعمل حاجة اسمها (Alternative Constructors) أو Factory Methods.

  

- **طريقة عملها:** بنحط فوق الدالة ديكوريتور `@classmethod`.
    
- **الـ Parameter الأول:** بدل `self`، بتاخد مدخل إجباري اسمه **`cls`** (اختصار لـ class). بايثون بتبعتلها الكلاس نفسه ككائن مش نسخة منه.
    
      
    
- **استخدامها:** بتقدر تقرأ وتعدل في الـ Class Attributes، وتقدر تنشئ Objects جديدة وترجعها.
    
      
    

### 2. Static Methods (`@staticmethod`)

دي بالظبط هي الـ **`static` member functions** اللي إنت عارفها في C++.

  

- **طريقة عملها:** بنحط فوق الدالة ديكوريتور `@staticmethod`.
    
      
    
- **الـ Parameters:** **مبتاخدش** لا `self` ولا `cls`. بتكتب المداخل اللي إنت عايزها بس.
    
      
    
- **استخدامها:** دالة مساعدة (Utility/Helper Function). هي ملهاش أي علاقة ببيانات الكائن (`self`) ولا بيانات الكلاس (`cls`)، هي بس دالة بتعمل حسبة معينة أو بتتأكد من حاجة، بس إحنا حطيناها جوه الكلاس عشان هي "منطقياً" تابعة ليه (بتنظم الكود بدل ما تبقى دالة عايمة بره في الفايل).
    

```Python
class Member:
    # Class Attribute (Shared among all instances)
    users_count = 0  

    # 1. Standard Constructor (Instance Method)
    def __init__(self, name, age):
        self.name = name
        self.age = age
        Member.users_count += 1

    # 2. Instance Method (Takes 'self')
    def show_info(self):
        print(f"Name: {self.name}, Age: {self.age}")

    # ---------------------------------------------------------
    # 3. Class Method (Takes 'cls' instead of 'self')
    # Used as an Alternative Constructor (Factory Method)
    # ---------------------------------------------------------
    @classmethod
    def from_string(cls, data_string):
        # Example data_string: "Ahmed-25"
        # Split the string by '-'
        name, age = data_string.split('-')
        
        # 'cls' here represents the 'Member' class itself
        # So cls(name, int(age)) is exactly like calling Member(name, int(age))
        # We return the newly created object
        return cls(name, int(age))

    # ---------------------------------------------------------
    # 4. Static Method (Takes neither 'self' nor 'cls')
    # Used as a utility/helper function related to the class logic
    # ---------------------------------------------------------
    @staticmethod
    def is_valid_age(age):
        # Just a normal function grouped inside the class namespace
        # It doesn't modify or read object/class states
        return age > 0 and age < 120


# ==========================================
# Testing the Concepts
# ==========================================

# Using the standard constructor
m1 = Member("Ali", 30)
m1.show_info()

# Using the Class Method as an alternative constructor
# We call it using the Class Name directly
m2 = Member.from_string("Osama-36")
m2.show_info()

# Using the Static Method to validate data before doing anything
# We call it using the Class Name directly
test_age = 150
if Member.is_valid_age(test_age):
    print("Valid age")
else:
    print(f"{test_age} is an Invalid age!")

# Checking the shared Class Attribute
print(f"Total members created: {Member.users_count}")
```

### الخلاصة (مقارنة سريعة لتثبيت المعلومة):

- ال **`def method(self):`** ⬅️ بتعادل الدوال العادية في C++. بتتعامل مع الـ Object نفسه (`this`).
    
      
    
- ال **`@classmethod`** ⬅️ بنستخدمها غالباً كـ Factory عشان نعوض غياب الـ Constructor Overloading، أو عشان نعدل في الـ Static Variables الخاصة بالكلاس ككل (عن طريق `cls`).
    
      
    
- ال **`@staticmethod`** ⬅️ بتعادل الـ `static` في C++. دالة عادية جداً بس محبوسة جوه الـ Scope بتاع الكلاس عشان التنظيم ومبتحتكش بالمتغيرات بشكل مباشر إلا لو ناديت عليها باسم الكلاس `ClassName.attr`.


الـ **Magic Methods** (أو زي ما بنسميها في بايثون الـ **Dunder Methods** عشان بتبدأ وتنتهي بـ Double Underscore `__`) هي السر اللي بيخلي بايثون لغة مرنة جداً.

  

عشان أربطهالك بـ C++ فوراً: الـ Magic Methods دي هي الـ **Operator Overloading** والـ **Constructors/Destructors** بتوع بايثون.

  

### إيه فكرتها "تحت الهود" (Under the Hood)؟

في بايثون، إنت كمبرمج نادراً جداً ما بتستدعي الدالة اللي فيها `__` بإيدك. إنت بتعمل عملية عادية جداً (زي إنك تجمع كائنين بـ `+`، أو تطبع كائن بـ `print`)، وبايثون في الكواليس بتروح تدور جوه الكلاس بتاعك: "هل المبرمج ده كاتب دالة سحرية للجمع؟ لو كاتبها، هنفذها.. لو مش كاتبها، هضرب Error".

  

تعالى ناخد أهم 4 دوال سحرية بيعادلوا مفاهيم إنت حافظها في C++:

  

### 1. `__str__` (يعادل الـ `operator<<` مع `cout`)

لو طبعت Object في C++ من غير ما تعمل Overload لعلامة `<<`، هيطبعلك عنوانه في الميموري. نفس الكلام في بايثون، لو عملت `print(obj)` هيطبعلك مكانه في الميموري. عشان تخلي الـ Object يطبع كلام مقروء، بتعمل دالة اسمها `__str__` ولازم ترجع String. بايثون بتناديها أوتوماتيك أول ما تستخدم `print()`.

  

### 2. `__add__` (يعادل `operator+`)

عشان تقدر تجمع `obj1 + obj2`، بايثون بتترجم السطر ده في الكواليس لـ `obj1.__add__(obj2)`. لو إنت معرف الدالة دي جوه الكلاس، هيتم الجمع بناءً على اللوجيك اللي إنت كاتبه.

  

### 3. `__len__` (مفيش بديل مباشر في C++)

لو عايز تستخدم دالة بايثون الشهيرة `len()` عشان تجيب طول الـ Object بتاعك، بايثون بتدور على دالة `__len__` جواه وتنفذها.

  

### 4. `__del__` (يعادل الـ Destructor `~ClassName`)

بتشتغل أوتوماتيك اللحظة اللي الـ Object بيتمسح فيها من الميموري (Garbage Collected) أو لو مسحته بإيدك بكلمة `del`. بتستخدم لو عايز تقفل فايل أو تقطع اتصال بقاعدة بيانات قبل ما الكائن يموت.


```Python
class Vector:
    # 1. Constructor
    def __init__(self, x, y):
        self.x = x
        self.y = y
        print(f"Vector Created in Memory")

    # 2. Destructor (~Vector in C++)
    # Triggered automatically when the object is destroyed
    def __del__(self):
        print(f"Vector is being deleted from Memory")

    # 3. String Representation (operator<< in C++)
    # Triggered automatically when using print()
    def __str__(self):
        # Must always return a string
        return f"Vector Details: (X: {self.x}, Y: {self.y})"

    # 4. Addition Operator Overload (operator+ in C++)
    # Triggered automatically when using the '+' sign
    def __add__(self, other):
        # 'self' is the left operand, 'other' is the right operand
        new_x = self.x + other.x
        new_y = self.y + other.y
        # Returns a completely new object
        return Vector(new_x, new_y)

    # 5. Length Magic Method
    # Triggered automatically when using len()
    def __len__(self):
        # Let's assume the length of a 2D Vector is always 2 components
        return 2


# ==========================================
# Testing the Magic Methods
# ==========================================

# Triggers __init__
v1 = Vector(10, 20)
v2 = Vector(5, 15)

# Triggers __str__ automatically (Instead of memory address)
print(v1)
# Output: Vector Details: (X: 10, Y: 20)

# Triggers __add__ automatically (v1.__add__(v2))
v3 = v1 + v2
print(v3)
# Output: Vector Details: (X: 15, Y: 35)

# Triggers __len__ automatically
print(len(v1))
# Output: 2

# Triggers __del__ automatically when we explicitly delete v1
# Or when the script finishes execution
del v1
```

**الخلاصة:** الـ Magic Methods هي الطريقة اللي بايثون بتديهالك عشان تخلي الكلاس بتاعك يتصرف كأنه (Built-in Data Type) زيه زي الـ `int` والـ `list`. إنت بتبرمج اللوجيك مرة واحدة جوه الدالة، وتستخدم الكلاس بعدين بعلامات عادية جداً زي `+`, `-`, `==`, `print` وبايثون هتهندل الباقي.

inheritance: 

```python

```