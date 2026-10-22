

## Classes & Objects

- The naming convention for classes in Python is `PascalCase`.
- Everything in Python is an object, including classes themselves.
- A method whose name starts and ends with two underscores is called a **dunder** (double underscore) or **magic method**.

### Creating objects: C++ vs Python


|                  | C++                                          | Python                                                                                                                        |
| ---------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Create an object | Member* member_one = new Member();           | member_one = Member()                                                                                                         |
| Pointers / `new` | Explicit                                     | None. Every object lives on the heap automatically, and memory is cleaned up by reference counting plus the garbage collector |
| Constructor      | A function named after the class: `Member()` | Always named `__init__`                                                                                                       |
| Current object   | Hidden `this` pointer                        | Explicit first parameter, conventionally named `self`                                                                         |


### `__init__` and `self`

- When you write `Member()`, Python looks for the built-in `__init__` method and calls it **every time** an object is created.
- `__init__` initializes the data of the new object.
- `self` is the current object. Python's philosophy is "explicit is better than implicit", so unlike C++'s hidden `this`, you must declare it as the first parameter of every instance method. When you call the method, you don't pass it: Python does that for you.
- You _could_ name it anything (even `this`), but every Python programmer expects `self`.

```python
class Member:
    def __init__(self):
        pass

    def print_num(self, num):
        print(num)


member_one = Member()        # create an object
member_one.print_num(4)      # 4

# What Python really does with that call (self is just the object passed first):
Member.print_num(member_one, 4)    # 4
```

> Forgetting `self` in the definition (`def print_num(num):`) gives a confusing `TypeError` saying the method takes 1 positional argument but 2 were given, because Python still passes the object automatically.

### `__new__` vs `__init__`

`member_one = Member()` actually runs two steps behind the scenes:

1. **`__new__`** allocates the empty object on the heap (the real equivalent of C++'s `new`) and returns it.
2. **`__init__`** receives that empty object as `self` and fills it with data.

In practice you almost never write `__new__`. All your work goes in `__init__`.

---

## Attributes

Inside a class, a bare name like `name = "Ahmed"` is just a **local variable** that disappears when the method ends. C++ would silently treat it as `this->name`; Python won't. You must always say where the variable lives.

|                | Instance attribute     | Class attribute                                |
| -------------- | ---------------------- | ---------------------------------------------- |
| Written as     | self.attr              | ClassName.attr                                 |
| Belongs to     | Each object separately | The class, **shared** by all objects           |
| Defined        | Inside `__init__`      | Directly in the class body, outside any method |
| C++ equivalent | Normal member variable | `static` member variable                       |


```python
class Member:
    # Class attribute (like a static variable in C++): shared by all instances
    users_count = 0

    def __init__(self, name):
        # Instance attribute: specific to this object
        self.name = name

        # Modify the class attribute through the class name
        Member.users_count += 1

    def show_info(self):
        print(f"My name is {self.name}")
        print(f"Total members now: {Member.users_count}")


m1 = Member("Ahmed")
m1.show_info()
# My name is Ahmed
# Total members now: 1

m2 = Member("Sara")
m2.show_info()
# My name is Sara
# Total members now: 2
```

> ⚠️ **Trap 1: assigning through `self` shadows the class attribute.** Reading `self.count` finds the class attribute, but **assigning** to `self.count` creates a brand new _instance_ attribute and leaves the class attribute untouched:
> 
```python
 class Counter:
     count = 0
 
     def bad_increment(self):
         self.count += 1      # reads Counter.count (0), then creates self.count  1
 
 c1 = Counter()
 c1.bad_increment()
 print(c1.count)        # 1
 print(Counter.count)   # 0  -> the shared counter never changed
```

> 
> This is why you should modify class attributes through the class name.

> ⚠️ **Trap 2: a mutable class attribute is shared by every object.** `items = []` written in the class body means all objects append to the **same list** (the same idea as the mutable default argument trap). Per-object lists belong in `__init__` as `self.items = []`.

---

## Types of Methods

|Type|First parameter|Decorator|Works with|C++ equivalent|
|---|---|---|---|---|
|Instance method|`self`|none|The object's data|Normal member function|
|Class method|`cls`|`@classmethod`|The class and its class attributes|None (closest: a static factory function)|
|Static method|none|`@staticmethod`|Nothing from the object or class|`static` member function|

**Python has no constructor overloading** (you can't define several `__init__` methods). The common replacement is a **class method used as an alternative constructor** (also called a factory method), plus default arguments when that's enough.

```python
class Member:
    users_count = 0

    # 1. Standard constructor (instance method)
    def __init__(self, name, age):
        self.name = name
        self.age = age
        Member.users_count += 1

    # 2. Instance method: takes self
    def show_info(self):
        print(f"Name: {self.name}, Age: {self.age}")

    # 3. Class method: takes cls, used as an alternative constructor
    @classmethod
    def from_string(cls, data_string):
        # Example data_string: "Ahmed-25"
        name, age = data_string.split("-")
        # cls is the class itself, so cls(...) is the same as Member(...)
        return cls(name, int(age))

    # 4. Static method: takes neither self nor cls
    @staticmethod
    def is_valid_age(age):
        # A plain utility function that lives in the class namespace for organization
        return 0 < age < 120


m1 = Member("Ali", 30)
m1.show_info()                       # Name: Ali, Age: 30

m2 = Member.from_string("Osama-36")  # alternative constructor, called on the class
m2.show_info()                       # Name: Osama, Age: 36

test_age = 150
if Member.is_valid_age(test_age):
    print("Valid age")
else:
    print(f"{test_age} is an invalid age!")    # 150 is an invalid age!

print(f"Total members created: {Member.users_count}")    # Total members created: 2
```

> Use `cls(...)` instead of hardcoding `Member(...)` inside a class method: if a subclass calls `from_string`, `cls` is the **subclass**, so you get an object of the right type.
> 
> `0 < age < 120` is a chained comparison. It's valid Python and reads like the math, unlike C++ where you'd need `age > 0 && age < 120`.

Quick summary:

- `def method(self)`: normal member function, works with the object.
- `@classmethod`: works with the class as a whole; mostly used as a factory or to modify class attributes through `cls`.
- `@staticmethod`: a plain function placed inside the class scope for organization; touches no object or class state.

---

## Inspecting Objects (Class Info)

```python
m = Member("Ali", 30)

print(type(m))                    # the class of m: <class '__main__.Member'>
print(isinstance(m, Member))      # True
print(m.__dict__)                 # {'name': 'Ali', 'age': 30} -> the instance attributes
print(Member.__name__)            # Member

print(hasattr(m, "age"))          # True
print(getattr(m, "age"))          # 30
setattr(m, "age", 31)             # same as m.age = 31
```

---

## Magic (Dunder) Methods

You almost never call these by hand. You do something ordinary (`+`, `print`, `len`) and Python looks inside your class for the matching dunder method. If it exists it runs; if not, you get an error. They are how your class behaves like a built-in type such as `int` or `list`, and they're Python's version of **operator overloading** and constructors/destructors.

|Method|Triggered by|C++ equivalent|
|---|---|---|
|`__init__`|`Class()`|Constructor|
|`__del__`|The object is destroyed|Destructor (but not reliable, see below)|
|`__str__`|`print(obj)`, `str(obj)`|`operator<<` for display|
|`__repr__`|`repr(obj)`, debugger, REPL, objects inside a list|None|
|`__add__`|`a + b`|`operator+`|
|`__eq__`|`a == b`|`operator==`|
|`__lt__`|`a < b`|`operator<`|
|`__len__`|`len(obj)`|None|
|`__getitem__`|`obj[i]`|`operator[]`|
|`__call__`|`obj()`|`operator()` (functor)|
|`__iter__` / `__next__`|`for x in obj`|Iterators|
|`__enter__` / `__exit__`|`with obj:`|RAII|

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # print(v) -> readable text (must return a string)
    def __str__(self):
        return f"Vector Details: (X: {self.x}, Y: {self.y})"

    # repr(v) -> unambiguous text for developers
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

    # v1 + v2 -> v1.__add__(v2); self is the left operand, other is the right one
    def __add__(self, other):
        if not isinstance(other, Vector):
            return NotImplemented
        return Vector(self.x + other.x, self.y + other.y)

    # v1 == v2
    def __eq__(self, other):
        if not isinstance(other, Vector):
            return NotImplemented
        return self.x == other.x and self.y == other.y

    # len(v)
    def __len__(self):
        return 2


v1 = Vector(10, 20)
v2 = Vector(5, 15)

print(v1)                       # Vector Details: (X: 10, Y: 20)
print(repr(v1))                 # Vector(10, 20)

v3 = v1 + v2                    # Python runs v1.__add__(v2)
print(v3)                       # Vector Details: (X: 15, Y: 35)

print(len(v1))                  # 2
print(v1 == Vector(10, 20))     # True
```

Notes:

- **`__str__` vs `__repr__`:** `__str__` is for end users, `__repr__` is for developers. If only `__repr__` is defined, `print()` falls back to it. Without either, you get a memory address like C++.
- **Return `NotImplemented`** (not an exception) from operator methods when the other operand is a type you don't support. Python then tries the other operand's reflected method (such as `__radd__`) and finally raises a clean `TypeError`. Don't confuse it with `NotImplementedError`, which is an exception (see Polymorphism below).
- **Defining `__eq__` makes the class unhashable** unless you also define `__hash__`, so its objects can't be used as set items or dictionary keys (see the hashability notes in the collections checkpoint).

### `__del__` is NOT a reliable C++ destructor

```python
class Resource:
    def __del__(self):
        print("Resource destroyed")


r = Resource()
del r       # removes the name r; nothing else references the object, so it's destroyed
# Resource destroyed
```

`del r` only removes a _name_. The object is destroyed when its last reference disappears (CPython uses reference counting), and the timing is not guaranteed in other cases (reference cycles, objects still alive at interpreter shutdown, other Python implementations). C++ destructors run deterministically at scope exit, which is what makes RAII work. **Don't rely on `__del__` to close files or database connections.** Use a `with` statement and a context manager (`__enter__` / `__exit__`) instead.

---

## Inheritance

Four key differences from C++:

1. **Syntax:** the parent goes in parentheses after the child's name: `class Developer(Employee):` instead of `class Developer : public Employee`.
2. **No `virtual` / `override`:** every method is effectively virtual (resolved at runtime). To override, just write a method with the same name in the child.
3. **`super()`:** replaces C++'s initializer list. It refers to the parent class so you can call the parent's `__init__` (or **any** parent method).
4. **No access modifiers:** there's no `public`/`private`/`protected`. The child inherits **everything**. A leading underscore is only a convention (see Encapsulation).

```python
# Base class (parent)
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def show_info(self):
        print(f"Employee Name: {self.name} | Salary: {self.salary}")

    def calculate_bonus(self):
        return self.salary * 0.10


# Derived class (child): parent name in parentheses
class Developer(Employee):
    def __init__(self, name, salary, programming_language):
        # Call the parent's constructor, then add the child-specific attribute
        super().__init__(name, salary)
        self.programming_language = programming_language

    # Method overriding: same name as the parent's method
    def show_info(self):
        super().show_info()          # reuse the parent's version (DRY)
        print(f"Specialization: {self.programming_language}")

    # Overriding completely, without calling the parent
    def calculate_bonus(self):
        return self.salary * 0.20


emp1 = Employee("Ahmed", 5000)
emp1.show_info()                         # Employee Name: Ahmed | Salary: 5000
print(f"Bonus: {emp1.calculate_bonus()}")    # Bonus: 500.0

dev1 = Developer("Osama", 8000, "Python")
dev1.show_info()
# Employee Name: Osama | Salary: 8000
# Specialization: Python
print(f"Bonus: {dev1.calculate_bonus()}")    # Bonus: 1600.0

print(isinstance(dev1, Employee))            # True  -> a Developer IS an Employee
print(issubclass(Developer, Employee))       # True
```

`super()` isn't only for constructors. Calling `super().show_info()` reuses the parent's printing code instead of duplicating it (DRY: Don't Repeat Yourself).

### Method Overriding

A child class defines a method with the same name as one in the parent to change or extend its behavior. In C++ this needs `virtual` in the parent and `override` in the child. In Python, writing the same name is enough, and `super()` gives access to the parent's version.

---

## Multiple Inheritance & MRO

A class can inherit from more than one parent: `class Child(Father, Mother):`.

**The problem:** if both parents have `speak()` and the child doesn't override it, which one runs? Python decides using the **MRO (Method Resolution Order)**, computed with the C3 linearization algorithm. In simple terms:

1. Look in the child class itself.
2. Then the parent written first (**left to right**).
3. Then the parent written next.
4. Then their ancestors, ending at `object`, the base of every Python class.

> **The key point about `super()`:** in multiple inheritance, `super()` does **not** mean "my direct parent". It means **"the next class in the MRO"**.

```python
class Animal:
    def make_sound(self):
        print("Animal is making a sound")


class Bird(Animal):
    def make_sound(self):
        print("Bird is Chirping")
        super().make_sound()       # the NEXT class in the MRO, not necessarily Animal


class Airplane(Animal):
    def make_sound(self):
        print("Airplane is Roaring")
        super().make_sound()


# The diamond shape: inherits from Bird (left) and Airplane (right)
class FlyingObject(Bird, Airplane):
    def make_sound(self):
        print("FlyingObject is starting...")
        super().make_sound()


obj = FlyingObject()
obj.make_sound()
# FlyingObject is starting...
# Bird is Chirping
# Airplane is Roaring
# Animal is making a sound

# Reveal the MRO
for cls in FlyingObject.mro():       # same data as FlyingObject.__mro__
    print(cls.__name__)
# FlyingObject
# Bird
# Airplane
# Animal
# object
```

### Constructors with multiple parents

```python
class BaseOne:
    def __init__(self):
        print("Base One Initialized")


class BaseTwo:
    def __init__(self):
        print("Base Two Initialized")


class Derived(BaseOne, BaseTwo):
    pass


my_var = Derived()
# Base One Initialized
```

Why only one line? `Derived` has no `__init__`, so Python walks the MRO, finds `BaseOne.__init__` first, runs it, and **stops**.

> ⚠️ **C++ difference:** in C++, if the child has no constructor, the compiler calls the default constructors of **all** the base classes. Python runs only the **first** `__init__` it finds along the MRO. And if the child defines its own `__init__` without calling `super()`, no parent constructor runs at all.

**The proper fix is cooperative inheritance:** every class calls `super().__init__()`, so the chain follows the MRO through all of them.

```python
class BaseOne:
    def __init__(self):
        print("Base One Initialized")
        super().__init__()


class BaseTwo:
    def __init__(self):
        print("Base Two Initialized")
        super().__init__()


class Derived(BaseOne, BaseTwo):
    def __init__(self):
        print("Derived Initialized")
        super().__init__()


my_var = Derived()
# Derived Initialized
# Base One Initialized
# Base Two Initialized
```

Calling a parent directly by name (`BaseTwo.__init__(self)`) also works in a simple case like this, but it's fragile: in a diamond hierarchy it can run the same constructor twice. Prefer the cooperative `super()` chain.

---

## Polymorphism

Same interface (same method name), different behavior depending on the object's type.

### C++ vs Python

- **C++:** to store different objects together you need a common base class, an array of base pointers (`Base* arr[]`), and `virtual` so the right version runs.
- **Python:** no pointers, and no requirement to inherit at all. Python uses **Duck Typing**: "if it walks like a duck and quacks like a duck, it's a duck". It doesn't check the object's type, it only asks: **does this object have the method I'm calling?**

### Three forms

1. **Built-in polymorphism:** `len()` works on a string, a list, and a dict because each type implements `__len__`.
2. **Inheritance polymorphism:** the classic version. Children override a parent's method, and each object runs its own version in a loop.
3. **Duck typing:** unrelated classes that happen to share a method name can be used interchangeably.

```python
import math


# 1. Polymorphism with inheritance
class Shape:
    def calculate_area(self):
        raise NotImplementedError("Derived class must implement this method")


class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def calculate_area(self):
        return self.width * self.height


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def calculate_area(self):
        return round(math.pi * self.radius ** 2, 2)


# 2. Duck typing: NO inheritance from Shape at all
class ServerRoom:
    def __init__(self, length, width):
        self.length = length
        self.width = width

    def calculate_area(self):
        return self.length * self.width


# A list of mixed, unrelated objects: no base class pointers needed
my_objects = [Rectangle(10, 5), Circle(7), ServerRoom(20, 15)]

for obj in my_objects:
    class_name = obj.__class__.__name__
    print(f"Area of {class_name}: {obj.calculate_area()}")

# Area of Rectangle: 50
# Area of Circle: 153.94
# Area of ServerRoom: 300
```

### Forcing children to implement a method

Put this inside the parent's method (as in `Shape` above):

```python
raise NotImplementedError("Derived Class Must Implement This Method")
```

> This only fails when the method is **called**. Abstract Base Classes (below) are stricter: they fail when the object is **created**. (Also remember: `NotImplementedError` is an exception you raise; `NotImplemented` is a value returned from operator methods.)

**Summary:** in C++, polymorphism is tied tightly to inheritance and pointers. In Python it's about **matching method names**. As long as objects share the method name, you can treat them the same way in loops and functions, whatever their family tree.

---

## Encapsulation

C++ and Java enforce privacy: a `private` member can't be touched from outside, and the compiler guards it. Python's philosophy is "we trust the programmer": there's no real enforcement, only increasing levels of warning through underscores.

|Style|Meaning|Enforced?|
|---|---|---|
|`self.name`|**Public** (default)|n/a|
|`self._salary`|**Protected** by convention: for use inside the class and its subclasses|No. Python lets you read and write it from anywhere|
|`self.__password`|**Private** through **name mangling**|Partly. Python renames it internally to `_ClassName__password`|

**Name mangling** applies to names that start with two underscores and do **not** end with two underscores. Accessing `obj.__password` from outside raises `AttributeError`, but anyone who knows the class name can still reach `obj._ClassName__password`. Its main purpose is to avoid accidental name clashes in subclasses, not to provide security.

---

## Getters, Setters & `@property`

In C++ you'd make a variable private and write `getSalary()` and `setSalary(int s)`. In Python, `obj.get_salary()` is considered un-Pythonic. Instead, `@property` lets you use a method **like a plain attribute** while Python runs getter/setter logic behind the scenes. (It's the equivalent of C#'s `{ get; set; }`.)

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner            # public
        self._currency = "USD"        # protected (convention only)
        self.__balance = balance      # private (name mangling)

    # Getter: used like a variable -> account.balance
    @property
    def balance(self):
        print("Getter executed in the background")
        return self.__balance

    # Setter: runs on assignment -> account.balance = 5000
    @balance.setter
    def balance(self, new_amount):
        print("Setter executed in the background")
        if new_amount < 0:
            raise ValueError("Balance cannot be negative")
        self.__balance = new_amount


account = BankAccount("Mohammed", 1000)

print(account.owner)         # Mohammed
print(account._currency)     # USD  (works, but it breaks the convention)

print(account.balance)       # runs the getter, prints 1000
account.balance = 2500       # runs the setter

try:
    account.balance = -500
except ValueError as e:
    print(e)                 # Balance cannot be negative

# print(account.__balance)   # AttributeError

# The "hack": use the mangled name
print(account._BankAccount__balance)     # 2500
```

Same idea with a getter, a setter, and an optional deleter:

```python
class Server:
    def __init__(self, hostname):
        self.hostname = hostname      # goes through the setter, so validation runs here too

    @property
    def hostname(self):
        return self._hostname

    # The setter must use the same name as the property
    @hostname.setter
    def hostname(self, new_name):
        if len(new_name) < 3:
            raise ValueError("Hostname is too short!")
        self._hostname = new_name

    # Deleter (optional)
    @hostname.deleter
    def hostname(self):
        print("Deleting hostname...")
        del self._hostname


srv = Server("Web-Server-01")

print(srv.hostname)          # Web-Server-01  (getter, no parentheses)
srv.hostname = "DB-01"       # setter, with validation
# srv.hostname = "PC"        # ValueError: Hostname is too short!
del srv.hostname             # Deleting hostname...
```

> ⚠️ If `__init__` wrote `self._hostname = hostname` directly, it would **bypass** the setter, and an invalid hostname could be set at construction time. Assigning through `self.hostname` makes the validation apply from the start.

### Why use `@property`

- **Validation:** enforce rules before a value changes, so users can't corrupt the object's state.
    
- **Computed attributes:** calculate a value on demand instead of storing it.
    
    ```python
    import datetime
    
    
    class Person:
        def __init__(self, birth_year):
            self.birth_year = birth_year
    
        @property
        def age(self):
            return datetime.datetime.now().year - self.birth_year
    ```
    
- **Read-only attributes:** define only the getter. Assigning to it raises `AttributeError`.
    
- **Painless refactoring:** if existing code uses a plain `obj.name` and you later need validation, converting `name` to a property requires **no change** to any code that uses it. (This is also why Python code doesn't write getters and setters up front.)
    

---

## Abstract Base Classes (ABC)

The Python way to build abstract classes and interfaces: the equivalent of pure virtual functions in C++ or `abstract` in Java and C#. An ABC is a **blueprint or contract** that forces subclasses to implement certain methods, and you **can't create an object** of the abstract class itself.

Python has no `abstract` keyword, so you import from the built-in `abc` module.

```python
from abc import ABC, abstractmethod


# 1. The abstract base class
class CloudResource(ABC):

    # Mandatory: every subclass must implement these
    @abstractmethod
    def deploy(self):
        pass

    @abstractmethod
    def get_cost(self):
        pass

    # A normal method with working code can live in an ABC too
    def log_creation(self):
        print("Resource creation logged in the system.")


# 2. A concrete child class
class AzureVM(CloudResource):
    def deploy(self):
        print("Deploying Virtual Machine in Azure...")

    def get_cost(self):
        return "Cost is $50/month"


# resource = CloudResource()   # TypeError: Can't instantiate abstract class CloudResource ...

vm = AzureVM()                 # works
vm.deploy()                    # Deploying Virtual Machine in Azure...
vm.log_creation()              # Resource creation logged in the system.
```

**What if a subclass forgets a mandatory method?** Python raises a `TypeError` when you try to **create the object**, not when the missing method is later called, which protects you from runtime surprises in big systems:

```python
class AzureSQL(CloudResource):
    def deploy(self):
        print("Deploying SQL database...")
    # get_cost is missing!


# db = AzureSQL()   # TypeError: Can't instantiate abstract class AzureSQL ...
```

### Why use ABCs

- **Standardization:** anyone adding a new class (for example AWS support next to Azure) must provide `deploy` and `get_cost` with those exact names.
- **Safe polymorphism:** you can loop over VMs, databases, and storage calling `deploy()` and be 100% sure the method exists on all of them.
- **No half-built objects:** you can't instantiate a generic `CloudResource` that has no real implementation.

### `ABC` vs `metaclass=ABCMeta`

A **metaclass** is "the class that creates classes" (the default one is `type`). `ABCMeta` is the metaclass Python uses to build abstract classes. The two spellings give the **same result**:

```python
from abc import ABC, ABCMeta


class A(metaclass=ABCMeta):    # the long form (the only option before Python 3.4)
    pass


class B(ABC):                  # the short form: ABC is essentially just a class defined with metaclass=ABCMeta
    pass
```

**Always use `class MyClass(ABC):`.** It's the modern standard and easier to read. You'd only need the long form in a rare advanced case where your class must also use a different metaclass (a class can only have one metaclass, so you'd combine them yourself).

---

## Beyond the Playlist: `@dataclass`

Most classes in real backend code are just containers of data, and writing `__init__`, `__repr__`, and `__eq__` by hand for each is boilerplate. `@dataclass` generates them from type hints:

```python
from dataclasses import dataclass


@dataclass
class Member:
    name: str
    age: int


m = Member("Ali", 30)
print(m)                          # Member(name='Ali', age=30)
print(m == Member("Ali", 30))     # True
```

This connects directly to your backend path: FastAPI's request and response models (Pydantic) are classes declared in this same style, attributes with type hints.