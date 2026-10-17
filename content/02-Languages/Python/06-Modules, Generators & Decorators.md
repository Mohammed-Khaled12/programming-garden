# Modules, Date/Time, Generators & Decorators

## Modules

A module is simply a `.py` file containing a collection of functions (and/or classes, variables) you can reuse.

```python
import random

print(random)   # <module 'random' from '/usr/lib/python3.12/random.py'>
print(f"Random Num: {random.random()}")

from functools import reduce
```

### Creating your own module

1. Create a `.py` file.
2. Put your functions inside it.
3. In your main file, import it: `import module_name`.

You can also give it an alias: `import module_name as alias_name`.

### Module vs Package

**Module** ---> A single `.py` file
**Package**---> A folder containing several modules grouped together

### External Packages

Downloaded from the internet, installed via **PIP** (Python's package manager) — install, update, delete. PIP installs both the package itself and its dependencies.

```shell
pip --version
# pip 24.0 from /usr/lib/python3/dist-packages/pip (python 3.12)

pip list
# Package        Version
# attrs          23.2.0
# Babel          2.10.3
# bcc            0.29.1

pip install termcolor
```

```python
import termcolor
import pyfiglet # ASCII art using pyfiglet module
```

---

## Date and Time

```python
import datetime

# Current date and time
print(datetime.datetime.now())          # 2026-09-30 11:06:44.176578

# Individual components
print(datetime.datetime.now().year)      # 2026
print(datetime.datetime.now().month)     # 9
print(datetime.datetime.now().day)       # 30

# Absolute min/max representable datetime
print(datetime.datetime.min)             # 0001-01-01 00:00:00
print(datetime.datetime.max)             # 9999-12-31 23:59:59.999999

# The time-only part
print(datetime.datetime.now().time())    # 11:13:10.323125
print(datetime.datetime.now().time().hour)   # 11
print(datetime.datetime.now().minute)    # 15
print(datetime.datetime.now().second)    # 55

# Min/max for a time-only object
print(datetime.time.min)                 # 00:00:00
print(datetime.time.max)                 # 23:59:59.999999

# A specific date (year, month, day, hour)
print(datetime.datetime(1967, 10, 23, 11))   # 1967-10-23 11:00:00
```

### Formatting with `strftime`

```python
mybirthday = datetime.datetime(2000, 3, 17)

print(mybirthday.strftime("%B"))          # March
print(mybirthday.strftime("%b"))          # Mar
print(mybirthday.strftime("%d %B %Y"))    # 17 March 2000
```

`strftime` uses format codes (`%d`, `%m`, `%Y`, `%H`, `%M`, `%S`, `%B`, `%b`...) to control the output string. Full reference: [strftime cheat sheet](https://www.bairesdev.com/tools/strftime/).

---

## Iterable vs Iterator

This distinction is the foundation Generators are built on.

**Iterable** ---> Any object you can loop over with `for` — it knows how to _produce_ an iterator`list`, `tuple`, `dict`, `str`, `set`, `range`
**Iterator** ---> The object that actually does the stepping — it remembers its current position and gives you the _next_ item each time you call `next()` on it, until it raises `StopIteration`|what `iter(some_list)` returns|

```python
mylist = [1, 2, 3]

my_iter = iter(mylist)     # get an iterator from the iterable

print(next(my_iter))       # 1
print(next(my_iter))       # 2
print(next(my_iter))       # 3
print(next(my_iter))       # raises StopIteration -> nothing left
```

A `for` loop is really doing this `iter()` + repeated `next()` dance automatically behind the scenes — that machinery is exactly what makes Generators (below) work with `for` loops for free.

> C++ comparison: an **iterable** is like a container (`std::vector`), and an **iterator** is like `vector::iterator` — the object that actually walks through it with `++`. Same split, different syntax.

---

## Generators

Generators are regular-looking functions that, instead of computing and returning the entire result at once, produce it **one item at a time**, on demand. A generator **is** an iterator — calling it gives you back an iterator object automatically.

The whole mechanism hinges on one keyword: **`yield`** instead of `return`.

### `return` vs `yield`

`return` ---> Builds the entire result, ends the function completely, and puts the whole thing in memory at once
`yield` ---> Produces one value, **pauses** the function and remembers its exact state, and resumes from that exact point the next time it's asked for a value|

### Example

**Regular function (`return`)** — if you asked it for a million numbers, it would build a million-item list in memory all at once, which could crash the machine for large enough data:

```python
def normal_function():
    nums = []
    for i in range(1, 4):
        nums.append(i)
    return nums

print(normal_function())
# [1, 2, 3]  (all produced and returned at once)
```

**Generator (`yield`)** — produces the numbers one at a time:

```python
def my_generator():
    for i in range(1, 4):
       yield i

# Calling it does NOT run the code yet — it returns a generator object
gen = my_generator()

print(next(gen))   # 1  -> the function pauses right here
print(next(gen))   # 2  -> resumes, runs until the next yield, pauses again
print(next(gen))   # 3
```

### How it's actually used

You rarely call `next()` by hand — the natural way to consume a generator is a `for` loop, since loops already know how to pull values from anything iterable, one at a time, automatically until it's exhausted:

```python
for number in my_generator():
   print(number)
```

### When to use Generators (the big advantage)

**Memory efficiency.** If you're reading a 10 GB file, reading it all at once with a normal function/list would fill up RAM and crash the program. A generator reads one line, processes it, discards it, then reads the next — so at any given moment it only holds **one line's worth** of data in memory, no matter how huge the source is.

> **Shortcut:** remember List Comprehensions — `[x for x in numbers]`? Swap the square brackets `[]` for parentheses `()` and you've written a **Generator Expression** in one line: `gen = (x for x in numbers)`. This isn't a list — it's a generator that produces values one at a time.

---

## Decorators

Also known as **"Metaprogramming"** 
A decorator is itself an object, and specifically a **higher-order function** — a function that takes another function as a parameter.

A decorator lets you **add new behavior to an existing function, without modifying that function's original code.**

Analogy: you bought a new phone (the phone is the original function). Before using it, you put a screen protector and a case on it (the decorator). You didn't change the phone itself internally — you added extra capability around it.

### How it works

Mechanically, a decorator is a function that takes another function as input, builds a "wrapper" function around it, and returns that wrapper.

```python
# 1. Define the decorator
def my_decorator(func):
    def wrapper():                        # the inner "wrapper" function
        print("Function is being processed")   # runs before the original
        func()                                   # calls the original function
        print("Function has processed")          # runs after the original
    return wrapper                        # return the wrapper

# 2. Apply it with @ right above the function you want to modify
@my_decorator
def say_hello():
    print("Welcome to Python!")

# 3. Call it like a normal function
say_hello()
```

**Output:**

```
Function is being processed
Welcome to Python!
Function has processed
```

### Real-world uses

Decorators save you from repeating the same code over and over. Common uses:

1. **Timing execution (`@timer`):** instead of writing timing code inside every single function you want to measure, write it once as a decorator and stick `@timer` on top of any function.
2. **Authentication (`@login_required`):** in web frameworks like Django or Flask, a page that requires login is written completely normally, then decorated with `@login_required`. The decorator checks login status and redirects if needed — without touching the page's own code at all.
3. **Logging:** record who called a function and when, in a log file, without adding logging code inside every function.

### Handling functions that take parameters

If the original function takes arguments (e.g. `say_hello(name)`), the `wrapper` must accept and forward them. To make a decorator work with **any** function regardless of how many arguments it takes, use `*args` and `**kwargs`:

```python
def smart_decorator(func):
    def wrapper(*args, **kwargs):
        print("Extra data before running...")
        return func(*args, **kwargs)   # forward any number of arguments
    return wrapper

@smart_decorator
def add_numbers(x, y):
    return x + y
```

### A decorator for a function with a fixed, known parameter

```python
def my_decorator(func):
    def wrapper(name1):
        print("Function is being processed")
        func(name1)
        print("Function has processed")
    return wrapper

@my_decorator
def sayHello(name):
    print(f"Hello {name}")

sayHello("Ahmed")
```

> This version only works for a function taking exactly one argument called the same way — `smart_decorator` above (with `*args, **kwargs`) is the general-purpose version you'd actually use in real code.

> **Worth knowing:** in real projects, people usually add `@functools.wraps(func)` on top of the `wrapper` function. Without it, the decorated function loses its original name and docstring (`sayHello.__name__` would print `"wrapper"` instead of `"sayHello"`) — `functools.wraps` fixes that. Not strictly needed to understand the concept, but it's the difference between a decorator you'd use in a toy example and one you'd ship in a real backend.

# Task 6

## Requirements

**Task: Log File Processor**

سكريبت واحد بسيط، بيستخدم Modules وGenerators وDecorators مع بعض بشكل منطقي:

1. ال **Module بسيط:** اعمل ملف تاني `log_helpers.py` فيه function واحدة `format_entry(message)` بترجع string فيها الوقت الحالي + الرسالة، بالشكل ده:

```
   [2026-09-30 11:15:00] Server started
```

(استخدم `datetime.datetime.now()` والـ formatting اللي اتعلمناه). `import` الملف ده في السكريبت الرئيسي.

2. ال **Generator:** اكتب function `log_generator(n)` بتستخدم `yield` وتـ generate رسائل تجريبية بالعدد `n` (مثلاً `"Event 1"`, `"Event 2"`, ... لحد `n`)، كل رسالة تتفرمت بـ `format_entry()` من الموديول.
3. ال **Decorator:** اكتب `@timer` decorator (استخدم `*args`/`**kwargs`) بيقيس وقت تنفيذ أي function وبيطبعه في الآخر (استخدم `time.time()` قبل وبعد).
4. اعمل function اسمها `write_logs(n)`، حطلها الـ `@timer` decorator، وجواها:
    - استخدمي الـ generator عشان تلوبي على الرسائل.
    - اكتبي كل رسالة في ملف `logs.txt` (استخدم `with open(...) as f:`).
5. نادي `write_logs(1000)` واطبع النتيجة — هيطلعلك وقت التنفيذ تلقائي من الـ decorator، وملف `logs.txt` فيه 1000 سطر.

## Solution 

```python
# log_helpers.py
import datetime


def format_entry(msg):

    dateTime = datetime.datetime.now()
    return f"[{dateTime}] {msg}"

```

```python
import log_helpers, time


def log_generator(n):
    for i in range(1, n + 1):
        yield log_helpers.format_entry(f"Event {i}")


def timer(func):
    def wrapper(*args, **kwargs):
        Exc1 = time.time()
        result = func(*args, **kwargs)
        Exc2 = time.time()
        print(f"Excution Time: {Exc2 - Exc1}")
        return result

    return wrapper


@timer
def write_logs(n):
    with open("logs.txt", "w") as f:
        f.writelines(f"{entry}\n" for entry in log_generator(n))


write_logs(1000)

```

