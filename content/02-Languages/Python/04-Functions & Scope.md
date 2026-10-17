# Python Functions 

## Defining & Calling

```python
def func_name(parameter1, parameter2):
	implementation


func_name(argument1, argument2)
```

- **Parameter** = the variable name in the function _definition_.
- **Argument** = the actual value you pass when _calling_ the function.

---
## Default Parameters

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}")

greet("Mohammed")              # Hello, Mohammed
greet("Mohammed", "Welcome")   # Welcome, Mohammed
```

Parameters with defaults must come **after** the ones without (same rule as C++).

⚠️ **Mutable default trap (no C++ equivalent):** the default value is evaluated **once, when the function is defined**, not on every call. C++ evaluates default arguments at each call, so this behavior surprises people coming from it.
 
```python
 def add_item(item, items=[]):     # BAD
     items.append(item)
     return items
 
 print(add_item(1))   # [1]
 print(add_item(2))   # [1, 2]  <- the same list is reused across calls!
```
 Fix: use `None` as the default and create the list inside the function:
 
```python
 def add_item(item, items=None):
     if items is None:
         items = []
     items.append(item)
          return items
```

---

## Packing & Unpacking

Two mechanisms for dealing with an **unknown number** of arguments, or for spreading a container (list / tuple / dict) out into separate arguments or variables. Same symbols (`*`, `**`), opposite directions.

### 1. Packing — `*args` and `**kwargs` (in the definition)

A `*` before a parameter name **collects** any number of positional arguments into **one tuple**.

```python
def total(*numbers):
    print(numbers)          # tuple: (1, 2, 3, 4)
    return sum(numbers)

print(total(1, 2, 3, 4))    # 10
print(total(5))             # 5
print(total())              # 0
```

The name `numbers` is arbitrary; the convention is `args`.

`**` does the same for **keyword arguments**, collecting them into a **dictionary**:

```python
def user_info(**details):
    print(details)   # {'name': 'Mohammed', 'age': 22}
    for key, value in details.items():
        print(f"{key}: {value}")

user_info(name="Mohammed", age=22)
```

Both can be used together:

```python
def mix(*args, **kwargs):
    print(args)      # (1, 2, 3)
    print(kwargs)    # {'x': 10, 'y': 20}

mix(1, 2, 3, x=10, y=20)
```

> **Required order in the definition:** normal parameters → `*args` → `**kwargs`. Parameters placed _after_ `*args` become **keyword-only** (e.g. `def f(*args, sep="-")` can only receive `sep` by name).

### 2. Unpacking — `*` and `**` (in the call)

The reverse: you already have a container and want to spread its items out as separate arguments.

```python
def add(a, b, c):
    return a + b + c

nums = [1, 2, 3]
print(add(*nums))    # same as add(1, 2, 3)  -> 6

info = {"a": 10, "b": 20, "c": 30}
print(add(**info))   # same as add(a=10, b=20, c=30)  -> 60
```

- `*` spreads any iterable (list, tuple, set, string, range...) as **positional** arguments.
- `**` spreads a dict as **keyword** arguments — the keys must match the parameter names exactly.

### 3. Unpacking in assignments (not only function calls)

```python
a, b, *rest = [1, 2, 3, 4, 5]
print(a, b, rest)    # 1 2 [3, 4, 5]
```

`*rest` collects everything left over into a **list**. Useful when you only care about the first few items.

### Packing vs Unpacking

![[Pasted image 20260927214531.png]]

### Why it's useful

- **Flexible APIs:** functions that accept any number of inputs (like `print()`).
- **Forwarding data without unpacking manually:** with `config = {"host": "...", "port": 8080}`, write `func(**config)` instead of `func(config["host"], config["port"])`.
- **Decorators:** the wrapper function uses `*args, **kwargs` to accept whatever arguments the original function takes and pass them straight through.

> C++ comparison: this is the closest thing to variadic templates (`template<typename... Args>`), but resolved at **runtime** with no template machinery — at the cost of no compile-time type checking.

---
## Lambda (Anonymous Function)

A small function with **no name**, written as a single expression. Use it for simple, throwaway logic; use `def` for anything bigger.

```python
lambda arguments: expression
```

Key points:

1. It has no name.
2. It can be called inline without being defined first.
3. It can be returned from another function.
4. It's for simple functions; `def` handles the larger tasks.
5. Its body is **one single expression**, not a block of code.
6. Its type is `function`.

```python
# Regular function
def add(x, y):
    return x + y

print(add(5, 3))                    # 8

# Lambda equivalent
add = lambda x, y: x + y
print(add(5, 3))                    # 8

# Called inline, no name at all
print((lambda x, y: x + y)(5, 3))   # 8
```

> Assigning a lambda to a name (`add = lambda ...`) is only for demonstration. PEP 8 recommends `def` in that case. Lambdas earn their place as **short inline arguments**.

**Returning a lambda from a function:**

```python
def make_multiplier(n):
    return lambda x: x * n

double = make_multiplier(2)
print(double(5))    # 10
```

**Typical use — as an argument to another function:**

```python
people = [("Mohammed", 22), ("Ahmed", 19), ("Sara", 25)]
people.sort(key=lambda person: person[1])
print(people)   # [('Ahmed', 19), ('Mohammed', 22), ('Sara', 25)]
```

**Details worth knowing:**

- Lambdas can't contain statements (`if`/`for`/`return` blocks), but a **conditional expression** is fine: `lambda x: "even" if x % 2 == 0 else "odd"`.
- They can take default values and `*args`/`**kwargs` like normal functions.
- A lambda reads variables from the surrounding scope **by reference** (similar to `[&]` in C++), looked up when it's _called_, not when it's created.
# Task 4

## Requirements

**Task: Mini Toolkit**

**1. Return + Default Parameters:**  
اكتب function `stats(numbers, round_to=2)` بترجع **3 قيم مع بعض**: المجموع، المتوسط (مقرّب لـ `round_to` أرقام)، وأكبر رقم. استقبل النتيجة في 3 متغيرات مباشرة (unpacking) واطبعهم.

**2. `*args`:**  
اكتب function `longest(*words)` بترجع أطول كلمة من أي عدد كلمات يتبعتلها. لو مفيش كلمات اتبعتت ترجع `None`. (تلميح: `max()` عندها parameter اسمه `key`، وجرب تستخدم `len` فيه.)

## Solution

```python
# 1. Return + Default Parameters:
# ------------------------------------------------------
def stats(numbers, round_to=2):

    total = sum(numbers)
    avg = round(total / len(numbers), round_to)
    maxx = max(numbers)

    return total, avg, maxx


my_numbers = [10.5, 20.33, 40.8, 5.2, 15.7]

total_val, avg_val, max_val = stats(my_numbers)

# ------------------------------------------------------


# 2. `*args`:
# ------------------------------------------------------
def longest(*words):
    if len(words) == 0:
        return None

    # key=len tells the max() function to compare items by their length rather than their default alphabetical order
    return max(words, key=len)


# ------------------------------------------------------

```