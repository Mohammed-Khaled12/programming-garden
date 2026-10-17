# Zip, Pillow, Docstrings, Exceptions and Type Hinting

## `zip()` — looping over several iterables at once

`zip()` works exactly like a zipper: it takes two or more iterables and pairs up the items that share the same position, so you can walk through all of them in a single `for` loop. No manual index needed.

- It returns a **zip object** (a lazy iterator) holding the pairs.
- Its length is the length of the **shortest** iterable.

```python
names = ["Ahmed", "Mohammed"]
grades = [34, 99]
status = ["Fail", "Pass"]

for name, grade, stat in zip(names, grades, status):
    print(f"Student: {name} | grade: {grade} | Status: {stat}")

# Student: Ahmed | grade: 34 | Status: Fail
# Student: Mohammed | grade: 99 | Status: Pass
```

### Different lengths

`zip()` stops at the shortest iterable and silently drops the leftovers:

```python
letters = ["A", "B", "C", "D", "E"]
numbers = [1, 2, 3]

for letter, num in zip(letters, numbers):
    print(letter, num)

# A 1
# B 2
# C 3
```

If silently dropping data would be a bug, pass `strict=True` (Python 3.10+) to raise a `ValueError` when the lengths differ. If you want to keep going until the longest one ends, use `itertools.zip_longest`.

### Useful patterns

```python
names = ["Ahmed", "Mohammed"]
grades = [34, 99]

# Build a dictionary from two lists
print(dict(zip(names, grades)))    # {'Ahmed': 34, 'Mohammed': 99}

# Convert to a list of tuples
print(list(zip(names, grades)))    # [('Ahmed', 34), ('Mohammed', 99)]

# "Unzip" with the * unpacking operator (from the functions checkpoint)
pairs = [("a", 1), ("b", 2)]
letters, nums = zip(*pairs)
print(letters)    # ('a', 'b')
print(nums)       # (1, 2)
```

> ⚠️ **A zip object can only be consumed once**, just like any iterator (see the Iterable vs Iterator notes):
> 

```python
 z = zip(names, grades)
 print(list(z))    # [('Ahmed', 34), ('Mohammed', 99)]
 print(list(z))    # []  -> already exhausted
```

---

## Practical: Image Manipulation with Pillow

Install it with `pip install pillow`. The package is named **Pillow**, but you import it as **PIL** (the name of the older library it replaced).

```python
from PIL import Image

# Open the image (raw string so backslashes in the Windows path aren't treated as escapes)
my_image = Image.open(r"D:\Python\Files\game.jpg")

# Show it
my_image.show()

# Crop it: the box is (left, upper, right, lower) in pixels
my_box = (300, 300, 800, 800)
my_new_image = my_image.crop(my_box)
my_new_image.show()

# Convert the mode: "L" = 8-bit grayscale ("RGB" = color)
my_converted = my_image.convert("L")
my_converted.show()

# Useful extras
print(my_image.size)                 # (width, height)
my_new_image.save("cropped.png")     # write the result to disk
```

> Without the `r` prefix, `"D:\Python\Files\game.jpg"` is a bug waiting to happen: `\P` and `\F` aren't valid escape sequences, and a path like `"D:\new\table.jpg"` would silently turn `\n` and `\t` into a newline and a tab.

---

## Docstrings & Commenting vs Documenting

1. A docstring is a documentation string attached to a **module, class, or function**.
2. It's stored on the object and can be read at runtime through `__doc__` or `help()`.
3. It exists to explain what complex code _does_, to whoever is _using_ it.
4. There are one-line and multi-line docstrings.

```python
def elzero_function(name):
    """
    Elzero Function
      It says hello from Elzero.
    Parameter:
      name => Person name that uses the function
    Return:
      Returns a hello message to the person
    """
    print(f"Hello {name} From Elzero")


elzero_function("Ahmed")

print(dir(elzero_function))            # all attributes of the function object, including __doc__
print(elzero_function.__doc__)         # the raw docstring text
help(elzero_function)                  # a formatted help page built from the docstring
```

One-line version:

```python
def square(x):
    """Return x multiplied by itself."""
    return x * x
```

---

## Pylint — checking code quality

Pylint is a static analysis tool: it reads your code _without running it_ and reports style violations, likely bugs, unused variables, missing docstrings, and so on, then gives the file a score out of 10.

```shell
pip install pylint
pylint my_file.py
```

> In modern projects, many teams use **Ruff** instead (a much faster linter and formatter in one tool). The idea is identical, so what you learn with Pylint transfers directly.

---

## Errors and Exceptions

### Raising your own errors (`raise`)

Python raises errors by itself when code breaks (division by zero, using an undefined variable). But sometimes the code is perfectly valid _technically_ and wrong _logically_ (a business rule violation).

Example: a program asks for the user's age and gets `-5`. Python sees a normal integer and has no objection, but a negative age makes no sense. Use **`raise`** to throw an error yourself and stop the code:

```python
age = int(input("Enter your age: "))

if age < 0:
    raise ValueError("No age less than zero")
```

Pick the exception type that matches the problem: `ValueError` (right type, bad value), `TypeError` (wrong type), `KeyError`, `FileNotFoundError`, or a plain `Exception` when nothing fits.

---

## Exception Handling: `try`, `except`, `else`, `finally`

So the program doesn't crash in the user's face when something goes wrong, you wrap risky code in a safety net:

|Block|Runs when|
|---|---|
|`try`|Always first — put the code you suspect might fail in here|
|`except`|**Only** if an error happened inside `try`|
|`else`|**Only** if `try` finished with no errors|
|`finally`|**Always**, error or not. Used for cleanup (closing files, connections, databases)|

### Basic form

```python
age = int(input("Enter Your Age: "))

try:
    print(age / 0)
except ZeroDivisionError:
    print("Error: cannot divide by zero")
```

> ⚠️ Notice that in this example the `int(input(...))` line sits **outside** the `try`. If the user types `abc`, `int()` raises a `ValueError` and the program still crashes. Put the risky conversion _inside_ the `try`.

### All four blocks together

```python
def read_age():
    try:
        age = int(input("Enter your age: "))
        if age < 0:
            raise ValueError("Age cannot be negative")
    except ValueError as e:
        print(f"Invalid age: {e}")
        return None
    else:
        print("Input accepted")
        return age
    finally:
        print("Done")        # runs no matter which path was taken, even after a return
```

### Capturing the error details (`as e`)

Instead of printing a generic message, catch the error as an object and print Python's own message:

```python
try:
    with open("wrong_file.txt", "r") as file:
        content = file.read()
except FileNotFoundError as e:
    print(f"Sorry, there was a problem: {e}")
    # Sorry, there was a problem: [Errno 2] No such file or directory: 'wrong_file.txt'
```

### Handling several error types

Same reaction for several types: group them in a tuple. Different reactions: use separate `except` blocks (Python checks them from top to bottom and runs the first match).

```python
try:
    value = int(input("Number: "))
    print(10 / value)
except (ValueError, TypeError) as e:
    print(f"Bad input: {e}")
except ZeroDivisionError:
    print("Cannot divide by zero")
```

> ⚠️ **Avoid a bare `except:`** (or catching everything with `except Exception:` and doing nothing). It hides real bugs, including typos in your own code. Catch the **specific** exceptions you expect.

> **C++ comparison:** `try`/`except` is C++'s `try`/`catch`. Python adds `else` and `finally`; C++ has no `finally` because it relies on RAII (destructors) for cleanup, and Python's `with` statement is the closest equivalent to that. Python style also leans toward **EAFP** ("easier to ask forgiveness than permission": just try it and handle the exception) rather than checking every condition first, which is the more common C++ habit.

---
## Type Hinting

Type hints let you write down the types your variables should hold, and the types your functions accept and return (`int`, `str`, `list`, and so on).

> **Key fact:** Python is dynamically typed, so type hints are **not enforced** by the interpreter. Passing the wrong type won't stop the code by itself. Hints are notes for you, your teammates, and tools (your IDE, and checkers like `mypy` or `pyright`) so they can give accurate autocomplete and warn you about mistakes before you run anything.

### Variables

Variable name, a colon, then the type:

```python
name: str = "Ahmed"
age: int = 25
is_student: bool = True
```

### Functions

Parameter types go after each parameter name; the arrow `->` marks the return type.

```python
# Without hints
def add(x, y):
    return x + y

# With hints
def add(x: int, y: int) -> int:
    return x + y

print(add(5, 10))    # 15
```

### Lists and dictionaries (complex types)

```python
# A list containing only integers
scores: list[int] = [85, 90, 78]

# A dictionary: str keys (student name), int values (grade)
students_grades: dict[str, int] = {"Ahmed": 95, "Sara": 88}

# A function taking a list of strings and returning one string
def get_first_name(names: list[str]) -> str:
    return names[0]
```

> `list[int]` and `dict[str, int]` (lowercase) need Python 3.9+. In older code you'll see `from typing import List, Dict` instead.

### Optional values and "either/or" types

```python
# A value that is either a str or None (Python 3.10+ syntax)
def find_user(user_id: int) -> str | None:
    ...

# Older equivalent
from typing import Optional

def find_user_old(user_id: int) -> Optional[str]:
    ...
```

### Why type hints matter in real work

1. **Better autocomplete:** once the editor knows a variable is a `str`, typing `.` lists `upper()`, `split()`, and so on.
2. **Early error detection:** passing `"10"` to a function that declares `int` gets a red underline before you ever run the program.
3. **Documentation:** the function signature tells you what it needs and what it returns without reading its body.
4. **FastAPI relies on them:** this is the one place where type hints _do_ get enforced at runtime. FastAPI reads the hints on your endpoint parameters and request models and uses them to validate and convert incoming data automatically (through Pydantic). Writing `item_id: int` is not just a note there, it's the validation rule.# Check Points
