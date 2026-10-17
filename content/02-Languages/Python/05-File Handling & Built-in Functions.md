# File Handling & Built-in Functions 

## File Handling

```python
file = open("file_path", "mode")
```

### Modes

|Mode|Name|Description|
|---|---|---|
|`"r"`|Read|Default mode. Opens for reading, raises an error if the file doesn't exist|
|`"w"`|Write|Opens for writing. **Overwrites** the entire file if it exists; creates it if not|
|`"a"`|Append|Opens for writing at the end of the file. Creates the file if it doesn't exist|
|`"x"`|Create|Creates a new file, raises an error if the file already exists|

Add `"b"` to any mode (e.g. `"rb"`, `"wb"`) to open the file in **binary** mode (images, PDFs, etc.) instead of text mode.

### Raw strings for file paths

Use a raw string (`r"..."`) for Windows-style paths, so backslashes aren't interpreted as escape sequences:

```python
path = r"C:\Users\Mohammed\Desktop\file.txt"
# Without the r prefix, \U and \D could be misread as escape sequences
```

### Reading a file

```python
myfile = open("test_file.txt", "r")
print(myfile)       # <object containing file info, e.g. <_io.TextIOWrapper ...>>
print(myfile.name)  # test_file.txt

print(myfile.read())
# Hello Python1
# Hello Python2
# Hello Python3

myfile.close()
```

> ⚠️ **Important — the cursor only moves forward.** `read()` consumes the file from the current cursor position to the end. If you call `read()` and then immediately call `readline()` or `readlines()` on the **same open file**, they will return an empty result — the cursor is already at the end. To read the same content again, either reopen the file or call `myfile.seek(0)` to reset the cursor to the start.

```python
myfile = open("test_file.txt", "r")

print(myfile.readline())     # Hello Python1   -> reads a single line, cursor moves after it
print(myfile.readline())     # Hello Python2   -> reads the next line
print(myfile.readlines())    # ['Hello Python3\n']  -> reads all remaining lines into a list

myfile.close()
```

### Writing and appending

```python
myFile = open("New_File.txt", "w")
myFile.write("Hi, It's a New File!!")

mylist = ["Mohammed", "Khaled"]
myFile.writelines(mylist)

myFile.close()
```

> `writelines()` does **not** add newlines between items automatically — `["Mohammed", "Khaled"]` is written as `MohammedKhaled` glued together. If you want each item on its own line, add `"\n"` yourself: `[item + "\n" for item in mylist]`.

```python
myFile = open("New_File.txt", "a")

print(myFile.tell())     # current cursor position (in bytes)
myFile.seek(2)            # move the cursor to byte 2
print(myFile.tell())     # 2

myFile.truncate(5)        # cuts the file down to its first 5 bytes

myFile.close()
```

> `tell()` returns the cursor's current byte position; `seek(n)` moves it to byte `n`. Note that in `"a"` mode, writes always go to the end of the file regardless of where `seek()` moved the cursor — `seek`/`tell` in append mode mainly matter for reading, not for where new writes land.

### `os` module

```python
import os

os.getcwd()                  # current working directory
os.path.abspath(__file__)    # absolute path of the current script
os.path.dirname(__file__)    # directory containing the current script
os.chdir("new_path")         # change the current working directory
os.remove("path")            # delete a file
```

### The `with` statement — the recommended way to handle files

The examples above all require a manual `.close()`, and if an exception happens before that line, the file stays open (a real problem in a long-running backend service). The `with` statement handles closing automatically, even if an error occurs inside the block:

```python
with open("test_file.txt", "r") as myfile:
    content = myfile.read()
    print(content)
# the file is already closed here, automatically
```

> This is the idiomatic (Pythonic) way to work with files — use `open(...) as f:` by default instead of manual `open()`/`close()` pairs. The closest equivalent in C++ is RAII (a `std::fstream` destructor closing the file automatically when it goes out of scope) — `with` gives you that same "guaranteed cleanup" guarantee, just via a different mechanism (a context manager, not a destructor).

---

## Built-in Functions

|Function|Description|
|---|---|
|`all(iterable)`|`True` if every item is truthy. `True` if the iterable is empty|
|`any(iterable)`|`True` if at least one item is truthy. `False` if the iterable is empty|
|`bin(n)`|Binary representation of an integer (`hex(n)` and `oct(n)` are the equivalents for hexadecimal/octal)|
|`id(var)`|The object's identity — guaranteed unique among all objects alive at the same time (CPython uses its memory address)|
|`sum(iterable, start)`|Sum of all items; `start` (optional) is added on top of the total|
|`round(num, digits)`|Rounds `num` to `digits` decimal places|
|`range(start, end, step)`|A sequence of numbers from `start` to `end` (exclusive), advancing by `step`|
|`print(*values, sep=" ", end="\n")`|`sep` is inserted between multiple printed values (default is a single space), `end` is appended after the last one (default is a newline)|
|`abs(n)`|Absolute value|
|`pow(num, exp, mod)`|`num` raised to `exp`; `mod` (optional) returns the result modulo `mod`|
|`min(...)` / `max(...)`|Smallest/largest of several arguments, or of one iterable|
|`slice(start, stop, step)`|Creates a slice object you can reuse for indexing, e.g. `s = slice(1, 3); mylist[s]`|
|`reversed(sequence)`|Returns a **reverse iterator** over the sequence (wrap in `list()` to see the items)|

---

## `map()`

Applies a function to **every** item of an iterable and gives back the results — no manual loop needed.

```python
map(function, iterable)
```

Think of it as a factory worker standing at a conveyor belt: every box (item) that passes gets exactly the same operation applied to it.

**Traditional way (`for` loop):**

```python
numbers = [1, 2, 3, 4, 5]
squared_numbers = []

for num in numbers:
    squared_numbers.append(num * num)

print(squared_numbers)   # [1, 4, 9, 16, 25]
```

**Same result with `map()` and a regular function:**

```python
def square(n):
	return n * n

numbers = [1, 2, 3, 4, 5]
squared_numbers = list(map(square, numbers))

print(squared_numbers)   # [1, 4, 9, 16, 25]
```

**Same result with `map()` and a `lambda`** (since the operation is simple, a full `def` isn't necessary):

```python
numbers = [1, 2, 3, 4, 5]
squared_numbers = list(map(lambda x: x * x, numbers))

print(squared_numbers)   # [1, 4, 9, 16, 25]
```

One line replaces both the loop and the calculation.

> **Note:** `map()` returns a `map` object (a lazy iterator), not a list — that's why it's memory-efficient. Wrap it in `list()` to actually see/use the results.

---

## `filter()`

Filters items based on a condition. Think of it as a checkpoint guard holding a rule ("no odd numbers allowed"): each item is tested — if the condition is `True` it passes through, if `False` it gets dropped.

```python
filter(function, iterable)
```

`function` must return `True` or `False` for each item.

**Traditional way:**

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8]
even_numbers = []

for num in numbers:
    if num % 2 == 0:
        even_numbers.append(num)

print(even_numbers)   # [2, 4, 6, 8]
```

**Same result with `filter()` and a `lambda`:**

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8]

even_numbers = list(filter(lambda x: x % 2 == 0, numbers))

print(even_numbers)   # [2, 4, 6, 8]
```

**`map` vs `filter`:**

- `map` transforms **every** item (5 items in → 5 items out, each changed).
- `filter` selects **some** items (5 items in → maybe 2 or 3 out, unchanged in value).

Like `map`, `filter()` also returns a lazy object — wrap it in `list()` to see the final result.

---

## `reduce()`

Collapses an entire iterable down to a **single value**, step by step.

Think of rolling a snowball: you take the first two items, combine them, then take that result and combine it with the third item, then the fourth... until only one value is left.

Concretely: it takes the first two items, applies the function, gets a result — then takes that result together with the next item and applies the function again, and so on until the iterable is exhausted.

> **Important:** unlike `map` and `filter`, `reduce()` is not a built-in in Python 3 — you must import it from `functools`.

```python
from functools import reduce

reduce(function, iterable)
```

`function` must take **two arguments** (not one, like `map`/`filter`).

**Traditional way:**

```python
numbers = [1, 2, 3, 4, 5]
total = 0

for num in numbers:
    total += num

print(total)   # 15
```

**Same result with `reduce()` and a `lambda`:**

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]
total = reduce(lambda x, y: x + y, numbers)

print(total)   # 15
```

**Step by step, behind the scenes:**

1. Takes the first two numbers (`1` and `2`), adds them → `3`
2. Takes that result (`3`) and adds the next number (`3`) → `6`
3. Takes that result (`6`) and adds the next number (`4`) → `10`
4. Takes that result (`10`) and adds the last number (`5`) → final result: **`15`**

### `map` vs `filter` vs `reduce` — the summary

|Function|Role|5 items in →|
|---|---|---|
|`map`|Transform|5 items out, each changed|
|`filter`|Select|Some subset out, unchanged|
|`reduce`|Collapse|1 single value out|

---

## `enumerate()`

Loops over an iterable and gives you **both** the index and the value on each pass, instead of tracking a counter manually.

```python
enumerate(iterable, start=0)
```

- `iterable`: the sequence to loop over.
- `start` (optional): the number to begin counting from — defaults to `0`.

**The tedious way (manual counter):**

```python
fruits = ["Apple", "Banana", "Mango"]
index = 0

for fruit in fruits:
    print(f"Index {index}: {fruit}")
    index += 1

# Index 0: Apple
# Index 1: Banana
# Index 2: Mango
```

**The clean way, with `enumerate()`:**

```python
fruits = ["Apple", "Banana", "Mango"]

for index, fruit in enumerate(fruits):
    print(f"Index {index}: {fruit}")

# Index 0: Apple
# Index 1: Banana
# Index 2: Mango
```

**Starting the count from 1 instead of 0** (e.g. showing a numbered list to a user):

```python
fruits = ["Apple", "Banana", "Mango"]

for number, fruit in enumerate(fruits, start=1):
    print(f"{number}- {fruit}")

# 1- Apple
# 2- Banana
# 3- Mango
```

**Rule of thumb:** the moment you find yourself writing `i = 0` and manually incrementing it inside a loop just to track position, reach for `enumerate()` instead.
# Task 5
## Requirements

Task: Student Records System

نظام بسيط بيدير درجات طلاب من ملف نصي، فيه كل مرحلة بتبني على اللي قبلها.

#### 1. الملف والتخزين (File Handling)

اعمل ملف `students.txt`، كل سطر فيه بيانات طالب بالشكل ده (comma-separated):

```
Mohammed,85,90,78
Ahmed,60,70,65
Sara,95,88,92
```

اكتبه باستخدام `with open(...) as f:` (مش `open()`/`close()` يدوي).

#### 2. قراءة وتحويل البيانات (File Handling + Dict + List)

اكتب function `load_students(filename)`:

- تفتح الملف بـ `with`.
- تلوب على كل سطر، تستخدم `.strip()` و`.split(",")`.
- ترجع **dictionary**: الاسم key، والدرجات (محولة لـ `int` باستخدام `list` comprehension) هي الـ value.

```python
{"Mohammed": [85, 90, 78], ...}
```

- لو الملف مش موجود، استخدم `try/except FileNotFoundError` وارجع dictionary فاضي (لسه معملناش try/except بالتفصيل بس دي فرصة تتعرف على الشكل العام بتاعها، وهنشرحها بعمق في الـ checkpoint الجاي).

#### 3. الحسابات (Functions + `*args` + Default Parameters)

اكتب function `average(*grades, round_to=2)` — لاحظ استخدام `*args` هنا بدل ما تاخد list عادي، يعني تقدر تناديها بـ `average(85, 90, 78)` مباشرة.

#### 4. الترتيب والتصنيف (map / filter / lambda / Tuple)

- استخدم `map()` مع lambda عشان تعمل dictionary تاني فيه اسم كل طالب ومتوسطه.
- استخدم `filter()` مع lambda عشان تجيب بس الطلاب اللي متوسطهم ≥ 70.
- رتب الطلاب الناجحين تنازليًا حسب المتوسط باستخدام `sorted()` مع lambda في الـ `key`.
- النتيجة النهائية خليها **list of tuples**: `[("Sara", 91.67), ("Mohammed", 84.33)]` (tuple لأن كل عنصر ثابت مش هيتعدل).

#### 5. التقرير (enumerate + f-strings + Ternary)

اطبع تقرير النتيجة النهائية باستخدام `enumerate(..., start=1)`، وفي كل سطر استخدم **ternary operator** عشان تحط تعليق: `"Excellent"` لو المتوسط ≥ 90، وإلا `"Good"`.

```
1- Sara: 91.67 (Excellent)
2- Mohammed: 84.33 (Good)
```

#### 6. الملخص الكلي (reduce)

استخدم `reduce()` من `functools` عشان تحسب **مجموع كل متوسطات الطلاب الناجحين** مع بعض (رقم واحد نهائي)، واطبعه.

#### 7. الحفظ (File Handling + Set)

اكتب التقرير النهائي في ملف جديد `report.txt` باستخدام `with`، واستخدم `set` عشان تتأكد إن مفيش اسم طالب متكرر قبل ما تكتب (لو الأسماء dictionary keys أصلاً مينفعش تتكرر، بس اعمل الفحص ده صراحة كتمرين على الـ set، حتى لو زيادة في الحالة دي).

## Solution

```python
from functools import reduce

# 1. Create the source file
with open("students.txt", "w") as myFile:
    myFile.write("Mohammed,85,90,78\n")
    myFile.write("Ahmed,60,70,65\n")
    myFile.write("Sara,95,88,92\n")


# 2. Load students from file into a dict
def load_students(filename):
    students = {}
    try:
        with open(filename, "r") as file:
            for line in file:
                parts = line.strip().split(",")
                name = parts[0]
                grades = parts[1:]
                int_grades = [int(grade) for grade in grades]
                students[name] = int_grades
    except FileNotFoundError:
        return {}
    return students


# 3. Average using *args
def average(*grades, round_to=2):
    return round(sum(grades) / len(grades), round_to)


students = load_students("students.txt")
print(students)

# 4. map -> {name: average}, filter -> only passed, sorted -> descending
averages = dict(map(lambda item: (item[0], average(*item[1])), students.items()))
print(averages)

passed = dict(filter(lambda item: item[1] >= 70, averages.items()))
print(passed)

ranked = sorted(passed.items(), key=lambda item: item[1], reverse=True)
print(ranked)   # [('Sara', 91.67), ('Mohammed', 84.33)]

# 5. Report with enumerate + ternary
report_lines = []
for i, (name, avg) in enumerate(ranked, start=1):
    comment = "Excellent" if avg >= 90 else "Good"
    line = f"{i}- {name}: {avg} ({comment})"
    print(line)
    report_lines.append(line)

# 6. reduce -> total of all passed students' averages
total_avg = reduce(lambda x, y: x + y, [avg for _, avg in ranked])
print(f"Total of passed averages: {total_avg}")

# 7. Save report, checking uniqueness with a set
seen_names = set()
with open("report.txt", "w") as report_file:
    for i, (name, avg) in enumerate(ranked, start=1):
        if name in seen_names:
            continue
        seen_names.add(name)
        comment = "Excellent" if avg >= 90 else "Good"
        report_file.write(f"{i}- {name}: {avg} ({comment})\n")

print("\nReport saved to report.txt")
```

