# Playlist Link

https://youtube.com/playlist?list=PLDoPjvoNmBAyE_gei5d18qkfIe-Z8mocs&si=bU3O2ay8jj7vyW09
# Python Basics 

## Basics

- `print()` → prints output to the screen.
- **Indentation Error** → Python uses spaces instead of `{}` to define code blocks; any inconsistency in the number of spaces within the same block causes an error.
- `#` → single-line comment.
- `type(x)` → returns the data type of `x`.
- **Everything in Python is an object** — even primitive types like `int` and `str` are objects with their own methods.
- **Multiple assignment**:
```python
a, b, c = 1, 2, 3
```

---

## Escape Characters

|Character|Description|
|---|---|
|`\n`|Newline|
|`\t`|Horizontal tab|
|`\\`|Literal backslash|
|`\'`|Single quote inside a single-quoted string|
|`\"`|Double quote inside a double-quoted string|
|`\r`|Carriage return — moves the cursor to the start of the line|
|`\b`|Backspace — deletes the character before it|
|`\f`|Form feed — moves to a new page (rarely used)|
|`\v`|Vertical tab|
|`\xhh`|Character represented by hexadecimal value `hh`|

---

## Slicing & Indexing

**Slicing** is like indexing, but it returns a sub-sequence (multiple items at once) instead of a single item.

```python
sequence[start:end]        # end is exclusive
sequence[start:end:step]   # step = the gap between selected items
```

---

## String Methods

### Length & Cleaning

|Method|Description|
|---|---|
|`len(a)`|Length of the string|
|`a.strip()`|Removes whitespace from both ends|
|`a.rstrip()`|Removes whitespace from the right only|
|`a.lstrip()`|Removes whitespace from the left only|
|`a.strip("#")`|Removes a specific character/substring (here `#`) from both ends instead of whitespace|

### Capitalization

|Method|Description|
|---|---|
|`b.title()`|Capitalizes the first letter of **every word**. If a digit precedes a word, the letter right after it is also capitalized|
|`b.capitalize()`|Capitalizes only the **first letter of the whole string**, the rest becomes lowercase|
|`g.upper()`|Converts the entire string to UPPERCASE|
|`g.lower()`|Converts the entire string to lowercase|
|`s.swapcase()`|Flips the case: uppercase becomes lowercase and vice versa|

### Padding

|Method|Description|
|---|---|
|`d.zfill(width)`|Pads the string with leading zeros until it reaches the given width (useful for numbers)|
|`e.center(width, "#")`|Centers the string and fills the remaining space with the given character (here `#`) — defaults to spaces if no character is given|
|`c.ljust(width)`|**Left-justifies** — the string is placed on the left, and the remaining space is padded on the right (default padding is spaces)|
|`c.rjust(width)`|**Right-justifies** — the string is placed on the right, and the remaining space is padded on the left|

> ⚠️ The difference between `ljust` and `rjust` is the direction of alignment, not the same description.

### Split / Join

|Method|Description|
|---|---|
|`j.split(sep, maxsplit)`|Returns a list of substrings. If no `sep` is given, it splits on whitespace. `maxsplit=-1` (default) means no limit. Splitting starts from the left|
|`j.rsplit(sep, maxsplit)`|Works exactly like `split()`, except splitting starts from the **right** — this only makes a difference when `maxsplit` is set|
|`f.splitlines()`|Returns a list of lines, splitting at line breaks|
|`separator.join(iterable)`|Joins the elements of a list (all must be strings) into one string, placing the separator between them. **Correct syntax:** `"-".join(["a","b","c"])`, not `join(separator, list)`|

### Search

|Method|Description|
|---|---|
|`f.count(sub, start, end)`|Returns the number of non-overlapping occurrences of the substring|
|`i.startswith(sub, start, end)`|`True` if the string starts with the given prefix|
|`i.endswith(sub, start, end)`|`True` if the string ends with the given suffix|
|`a.index(sub, start, end)`|Returns the first index where the substring is found — **raises an error if not found**|
|`a.find(sub, start, end)`|Does exactly what `index()` does, except it **returns `-1` if the substring isn't found** instead of raising an error|

### Check Methods (return True/False)

|Method|Description|
|---|---|
|`.isalpha()`|All characters are letters (no digits or symbols)|
|`.isalnum()`|All characters are letters or digits|
|`.isspace()`|All characters are whitespace|
|`.islower()`|All characters are lowercase|
|`.istitle()`|Every word starts with an uppercase letter|

### Modifying Text

|Method|Description|
|---|---|
|`.replace(old, new, count)`|Replaces `old` with `new`. `count` is optional and limits the max number of replacements (if omitted, all occurrences are replaced)|
|`.expandtabs(tabsize)`|Replaces tab characters (`\t`) with `tabsize` spaces (default is 8)|

---

## String Formatting

### Old Style (`%`)

```python
print("my name is %s" % "Mohammed")   # my name is Mohammed
```

|Placeholder|Description|
|---|---|
|`%s`|string|
|`%d`|integer (digit)|
|`%f`|float|
|`%.2f`|float with 2 digits after the decimal point|

```python
print("I'm %s %s, I'm %d Years Old" % ("Mohammed", "Ahmed", 80))
# I'm Mohammed Ahmed, I'm 80 Years Old
```

### New Style (`.format()` and f-strings)

```python
print("My name is: {}".format("Mohammed"))
# My name is: Mohammed

print("My name is: {} and my Age is: {}".format("Mohammed", 56))
# My name is: Mohammed and my Age is: 56

print("My name is: {:s} and my Age is: {:d}".format("Mohammed", 56))
# My name is: Mohammed and my Age is: 56

myname = "Mohammed"
myAge = 56

print(f"My name is: {myname} and my Age is: {myAge}")
# My name is: Mohammed and my Age is: 56
```

> **Note:** f-strings are the currently preferred style (clearer and better performance than `%` and `.format()`). Best to use them in any new code.




---

# Task 1

## Requirements
اكتب سكريبت بايثون بيعمل الآتي:

1. ياخد من المستخدم: الاسم، العمر، والمدينة (3 مدخلات منفصلة بـ `input()`).
2. يطبع جملة واحدة متكاملة باستخدام **f-strings فقط** (ممنوع استخدام `+` أو `%` أو `.format()`).
3. يستخدم `.title()` على الاسم عشان يضمن إن أول حرف كابيتال حتى لو المستخدم كتبه بحروف صغيرة.
4. يحسب ويطبع سنة الميلاد التقريبية (السنة الحالية - العمر) جوه نفس الجملة الـ f-string (يعني تعمل العملية الحسابية جوه الأقواس المعقوفة `{}` مباشرة).
5. ال Bonus: استخدم `.zfill()` عشان تطبع العمر بصيغة رقمين دايمًا (مثلاً لو العمر 9 يطبعها `09`).

المخرج المتوقع شكله تقريبًا كده:

```
Hello Mohammed! You are 22 years old, born around 2003, and you live in Cairo.
```

## Solution

```python
from datetime import datetime

name = input("Please Enter Your Name \n").title()
age = int(input("Please Enter Your Age \n"))
city = input("Please Enter Your City \n")

print(
    f"My Name is: {name}, Age: {str(age).zfill(2)}, Born in {datetime.now().year - age} and Lives in {city}"
)


```


# Python Collections — Lists, Tuples, Sets, Dictionaries

## List

- A list is **not** an array (no fixed type or fixed size — it's a dynamic, general-purpose container).
- Items are enclosed in square brackets `[]`.
- Lists are **mutable** → items can be added, removed, or edited after creation.
- Items are **not required to be unique** (duplicates allowed).
- A single list can hold **different data types** at once.

```python
myList = [1, 2, 3, "Four", 10.4, True]

print(myList)        # [1, 2, 3, 'Four', 10.4, True]
print(myList[3])     # Four
print(myList[-1])    # True

print(myList[1:3])   # [2, 3]      -> slicing (end excluded)
print(myList[::2])   # [1, 3, 10.4] -> extended slice: every 2nd item

myList[1] = 90
print(myList)        # [1, 90, 3, 'Four', 10.4, True]
```

### List Methods

|Method|Description|
|---|---|
|`list.append(item)`|Adds one item to the end. If you append a _list_, it's added as a single nested element, not merged|
|`list.extend(other_list)`|Concatenates two lists — merges the items of `other_list` into `list` (this is the difference from `append`)|
|`list.remove(item)`|Removes the **first** matching item by value|
|`list.pop(index)`|Removes and returns the item at `index` (default: last item)|
|`list.sort()`|Sorts the list in place, ascending by default|
|`list.sort(reverse=True)`|Sorts in place, descending|
|`list.reverse()`|Reverses the order of items in place|
|`list.clear()`|Removes all items, leaving an empty list|
|`list.copy()`|Returns a shallow copy of the list|
|`list.count(item)`|Returns how many times `item` appears|
|`list.index(item)`|Returns the index of the first occurrence of `item` (raises an error if not found)|
|`list.insert(index, item)`|Inserts `item` **before** the given index|

---

## Tuple

- Items are enclosed in parentheses `()`.
- The parentheses are actually optional — what makes it a tuple is the comma: `t = 1, 2, 3` is still a tuple. Use parentheses anyway for clarity.
- Items are accessed by index, same as lists.
- Tuples are **immutable** → once created, you cannot add, remove, or edit items.
- Items are **not required to be unique**.
- A tuple can hold different data types.
- Concatenate tuples with `+`.
- Repeat a tuple's contents with `*` (also works on lists and strings).

```python
my_table = (1, 2, 3, "Mohammed", "Ahmed", "Mohammed")

print(my_table)              # (1, 2, 3, 'Mohammed', 'Ahmed', 'Mohammed')
print(my_table[3])           # Mohammed
print(my_table.count("Mohammed"))  # 2

print(my_table * 2)
# (1, 2, 3, 'Mohammed', 'Ahmed', 'Mohammed', 1, 2, 3, 'Mohammed', 'Ahmed', 'Mohammed')
```

### Tuple Methods

|Method|Description|
|---|---|
|`tuple.count(item)`|Returns how many times `item` appears|
|`tuple.index(item)`|Returns the index of the first occurrence of `item`|

> Tuples only have these two methods (instead of the many list methods) precisely _because_ they're immutable — there's nothing to sort, insert, or remove in place.

---

## Set

- Items are enclosed in curly braces `{}`.
- Sets are **unordered** → no indexing, no slicing, no guaranteed order.
- Set items must be **hashable / immutable** (numbers, strings, tuples of immutables). Lists and dictionaries can't be set items because they're mutable and therefore unhashable.
- Items are **unique** — duplicates are automatically dropped.

### Set Methods

|Method|Description|
|---|---|
|`set.add(item)`|Adds a single item|
|`set.remove(item)`|Removes an item — **raises an error** if it doesn't exist|
|`set.discard(item)`|Removes an item — **no error** if it doesn't exist|
|`set.pop()`|Removes and returns an **arbitrary** item (sets have no order, so "arbitrary" here really does mean unpredictable)|
|`set.clear()`|Removes all items|
|`set.copy()`|Returns a shallow copy|
|`set1 \| set2` / `set1.union(set2)`|Returns a new set with all items from both sets|
|`set1.update(set2)`|Adds the items of `set2` into `set1` in place|
|`set1 - set2` / `set1.difference(set2)`|Returns items in `set1` that are **not** in `set2`|
|`set1.difference_update(set2)`|Updates `set1` in place to keep only the difference|
|`set1 & set2` / `set1.intersection(set2)`|Returns items present in **both** sets|
|`set1.intersection_update(set2)`|Updates `set1` in place to keep only the intersection|
|`set1 ^ set2` / `set1.symmetric_difference(set2)`|Returns items that are in **either** set but **not both**|
|`set1.symmetric_difference_update(set2)`|Updates `set1` in place with the symmetric difference|
|`set1.issuperset(set2)`|`True` if every item of `set2` is also in `set1`|
|`set1.issubset(set2)`|`True` if every item of `set1` is also in `set2`|
|`set1.isdisjoint(set2)`|`True` if the two sets share **no** common items|

---

## Dictionary

- Items are enclosed in curly braces `{}`.
- Items are stored as `key: value` pairs.
- Keys must be **immutable/hashable** (numbers, strings, tuples). Lists cannot be used as keys.
- Values can be of **any** data type — including another list, dict, etc.
- Keys must be **unique** — assigning to an existing key overwrites its value.
- **Note (Python 3.7+):** dictionaries are technically "not ordered" in the classic sense (you access by key, not by position), but since Python 3.7 they **do preserve insertion order** when you iterate over them. This wasn't guaranteed in older Python versions.


```python
user = {"name": "Mohammed", "age": 30, "Country": "Egypt"}

print(user)               # {'name': 'Mohammed', 'age': 30, 'Country': 'Egypt'}
print(user["Country"])    # Egypt
print(user.get("Country"))  # Egypt  -> safer: returns None instead of an error if key is missing

print(user.keys())        # dict_keys(['name', 'age', 'Country'])
print(user.values())      # dict_values(['Mohammed', 30, 'Egypt'])

# Two-Dimensional Dictionary (Nested Dict)
languages = {
    "One": {"name": "Html", "progress": "80%"},
    "Two": {"name": "Css", "progress": "90%"},
    "Three": {"name": "Js", "progress": "90%"},
}

print(languages)
print(languages["One"])
print(languages["Three"]["name"])  # Js
```

### Dictionary Methods

|Method|Description|
|---|---|
|`dict.clear()`|Removes all key-value pairs|
|`dict.update({"new_key": "new_value"})`|Adds/updates key-value pairs from another dict. **Correct syntax** takes a dict argument (`{}`), not a bare `"key": "value"` pair. Equivalent shortcut: `dict_name["new_key"] = value`|
|`dict.copy()`|Returns a shallow copy|
|`dict.keys()`|Returns a view of all keys|
|`dict.values()`|Returns a view of all values|
|`dict.items()`|Returns a view of all `(key, value)` pairs|
|`dict.setdefault(key, default)`|Returns the value of `key` if it exists; if not, **inserts** `key` with `default` and returns `default`. Useful to avoid a manual "if key not in dict" check|
|`dict.popitem()`|Removes and returns the **last inserted** key-value pair (as a tuple)|
|`dict.fromkeys(iterable, value)`|Creates a new dict, using every item of `iterable` as a key, all sharing the same `value`|


```python
# setdefault example
scores = {"Mohammed": 90}
scores.setdefault("Ahmed", 0)
print(scores)  # {'Mohammed': 90, 'Ahmed': 0}

# fromkeys example
keys = ["a", "b", "c"]
d = dict.fromkeys(keys, 0)
print(d)  # {'a': 0, 'b': 0, 'c': 0}
```


# Task 2
## Requirements

**Student Grades Manager**

اكتب برنامج بايثون بيدير درجات مجموعة طلاب، لازم يستخدم الأربعة أنواع (List, Tuple, Set, Dictionary) كل واحد في المكان المناسب له منطقيًا — مش عشوائي:

1. اعمل **dictionary** اسمه `students`، الـ key هو اسم الطالب، والـ value عبارة عن **list** فيها درجات الطالب في 3 مواد (أرقام).



```python
   students = {
       "Mohammed": [85, 90, 78],
       "Ahmed": [60, 70, 65],
       "Sara": [95, 88, 92],
   }
```

2. اكتب function `average(grades_list)` بترجع متوسط الدرجات (list → رقم واحد).
3. اطبع لكل طالب اسمه والمتوسط بتاعه (استخدم `.items()` عشان تلوب على الـ dictionary).
4. اعمل **tuple** ثابتة (immutable) اسمها `subjects` فيها أسماء المواد التلاتة بنفس الترتيب `("Math", "Science", "English")` — وطبع لكل طالب أعلى مادة عنده (يعني تربط بين index الدرجة الأعلى في الـ list وindex المادة المقابلة في الـ tuple).
5. اعمل **set** اسمه `passed_students` — أي طالب متوسطه ≥ 70 يتضاف اسمه للـ set (استخدم `.add()`). في الآخر اطبع الـ set وعدد الطلاب الناجحين (`len()`).
6. ال Bonus: اعمل set تاني اسمه `honor_students` لأي طالب متوسطه ≥ 90، وبعدين استخدم `.intersection()` بين الـ set اللي فات و`passed_students` عشان تتأكد إن كل الـ honor students هما بردو ناجحين (لازم تكون نفس الـ honor_students بالظبط).


## Solution

```python
# Dictionary Contains the name as a Key and a Value as a list of marks
students = {
    "Mohammed": [85, 90, 78],
    "Ahmed": [60, 70, 65],
    "Sara": [95, 88, 92],
}


def average(grades_list: list) -> float:
    return sum(grades_list) / len(grades_list)


for name, marks in students.items():
    print(f"{name}'s Average is {average(marks)}")

subjects = ("Math", "Science", "English")

for name, marks in students.items():
    max_grade = max(marks)
    max_index = marks.index(max_grade)
    print(f"{name}'s top subject: {subjects[max_index]} ({max_grade})")

passed_students = set()  # Because it's an empty set
for name, marks in students.items():
    if average(marks) >= 70:
        passed_students.add(name)

print(f"\nPassed students: {passed_students}")
print(f"Number of passed students: {len(passed_students)}")

honor_students = set()
for name, grades in students.items():
    if average(grades) >= 90:
        honor_students.add(name)

print(f"\nHonor students: {honor_students}")

check = honor_students.intersection(passed_students)
print(f"Honor ∩ Passed: {check}")
print("All honor students passed?", check == honor_students)

```

# Python Control Flow — Conditions & Loops 

## Boolean Operators

Python does **not** use `&&`, `||`, `!` — those are C++ syntax. Python uses plain English keywords instead:

| C++  | Python |
| ---- | ------ |
| `&&` | `and`  |
| \|\| | `or`   |
| `!`  | `not`  |

```python
if age > 18 and has_id:
    print("Allowed")
```

> Also worth knowing: `and`/`or` in Python don't strictly return `True`/`False` — they return one of the actual operand values (e.g. `0 or "hello"` returns `"hello"`). This is different from C++'s `&&`/`\|\|`, which always evaluate to a strict boolean.

`input()` **always returns a string** — even if the user types a number, you must explicitly convert it (`int(input(...))`) before doing math on it. This trips people up constantly since C++'s `cin >>` handles type conversion for you automatically based on the variable's declared type.

---

## Conditionals

```python
if condition:
    statement
elif condition:
    statement
else:
    statement
```

No parentheses needed around the condition, and no braces — indentation defines the block (same idea as always in Python).

### Ternary Operator

```python
statement_if_true if condition else statement_if_false
```

Example:

```python
status = "Adult" if age >= 18 else "Minor"
```

This is Python's equivalent of C++'s `condition ? a : b`, but written in a more readable "sentence" order.

---

## Membership Operators — `in` / `not in`

Checks whether a value exists inside a sequence (string, list, tuple, set, dict keys...).

```python
name = "Mohammed"
print("M" in name)   # True
print("E" in name)   # False

cities = ["Cairo", "Aswan", "Giza"]
user_city = input("What's your City?\n")

if user_city in cities:
    print(f"Hello to {user_city}")
else:
    print("Hello")
```

> There's no direct equivalent to this in C++ — the closest you'd get is manually looping and comparing, or `std::find`. In Python, `in` is a first-class operator that works on any iterable.

---

## While Loop

```python
while condition:
    statement
else:
    statement
```

- The `else` block runs **once, after the loop finishes normally** (i.e. the condition became `False`) — it is **skipped** if the loop was exited via `break`.
- The `else` is entirely optional — you can drop it if you don't need it.

---

## For Loop

```python
for item in iterable_object:
    statement
else:
    statement
```

Same `else` behavior as `while`: runs after the loop completes normally, skipped if `break` was hit.

> ⚠️ **C++ trap:** Python's `for` is fundamentally different from C++'s `for (init; condition; increment)`. It's a **for-each loop** — it always iterates over an iterable (list, range, string, dict...), there's no manual counter/condition/increment syntax. If you want a classic counted loop, you use `range()`.

### `range(start, end)`

```python
myRange = range(1, 100)

for num in myRange:
    print(f"{num}")
# Prints from 1 to 99 (end is exclusive, same rule as slicing)
```

---

## `break`, `continue`, `pass`

|Keyword|Description|
|---|---|
|`break`|Exits the loop immediately, skipping the rest of the iterations (and skipping the loop's `else`, if any)|
|`continue`|Skips the rest of the current iteration and jumps to the next one|
|`pass`|Does nothing — a placeholder used when Python syntactically requires a statement (e.g. an empty function/if body) but you have nothing to put there yet|

---

## Looping Over Dictionaries

```python
mySkills = {
    "HTML": "80%",
    "CSS": "90%",
    "JS": "70%",
    "PHP": "80%",
}

print(mySkills.items())
# dict_items([('HTML', '80%'), ('CSS', '90%'), ('JS', '70%'), ('PHP', '80%')])

# Method 1: loop over keys only, look up the value manually
for skill in mySkills:
    print(f"{skill} => {mySkills[skill]}")   # Key => Value

print("#" * 20)

# Method 2 (preferred): loop over key AND value together using .items()
for skill_key, skill_value in mySkills.items():
    print(f"{skill_key} => {skill_value}")   # Key => Value
```

> Method 2 is generally better style — it avoids doing a separate dictionary lookup (`mySkills[skill]`) for every iteration, since `.items()` gives you both pieces directly.

### Nested Dictionary Loop

```python
myUltimateSkills = {
    "HTML": {"Main": "80%", "Pugjs": "80%"},
    "CSS": {"Main": "90%", "Sass": "70%"},
}

for main_key, main_value in myUltimateSkills.items():
    print(f"{main_key} Progress Is: ")

    for child_key, child_value in main_value.items():
        print(f"- {child_key} => {child_value}")
```

# Task 3

## Requirements

**Grade Classifier + Inventory Checker**

**الجزء الأول — Grade Classifier (loop + break/continue + ternary):**

1. اعمل list فيها 6 درجات (أرقام من 0 لـ 100)، بعضها فوق 100 أو تحت 0 (قيم غلط قصدًا، زي `[85, 150, 60, -5, 92, 40]`).
2. اعمل `for` loop على الـ list:
    - لو الدرجة غلط (خارج range 0-100)، استخدم `continue` عشان تتخطاها (اطبع رسالة إنها اتجاهلت).
    - لو الدرجة صح، استخدم **ternary operator** عشان تحدد `"Pass"` لو ≥ 50 و`"Fail"` لو أقل، واطبعها.
3. حط `else` على الـ for loop بتطبع "Finished checking all grades" — وتأكد إنها بتتنفذ عادي لأنك مستخدمش `break` هنا.

**الجزء الثاني — Inventory Checker (while + membership + break):**

4. اعمل dictionary اسمه `inventory` فيه أسماء منتجات وأعدادها:

```python
   inventory = {"Laptop": 5, "Mouse": 0, "Keyboard": 12, "Monitor": 3}
```

5. اعمل `while True` loop بياخد من المستخدم اسم منتج (`input()`):
    - لو المستخدم كتب `"exit"` استخدم `break` واخرج من اللوب.
    - استخدم **membership operator** (`in`) عشان تتأكد إن اسم المنتج موجود في الـ `inventory`.
    - لو موجود: اطبع الكمية المتاحة (لو الكمية = 0 اطبع "Out of stock" بدل الرقم — استخدم ternary تاني هنا).
    - لو مش موجود: اطبع "Product not found".

**الجزء الثالث — Nested Loop (bonus):**

6. اعمل nested dictionary فيه فئات منتجات وتحتها منتجات فرعية بكمياتها:

```python
   categories = {
       "Electronics": {"Laptop": 5, "Mouse": 0},
       "Furniture": {"Chair": 8, "Desk": 2},
   }
```

7. اعمل nested loop (زي المثال اللي في النوتس) يطبع كل فئة وتحتها كل منتج وكميته، واستخدم `pass` كـ placeholder جوه `if` بتتحقق من حاجة (مثلاً لو الكمية أقل من 3) بس متكتبش فيها كود دلوقتي — بس عشان تتدرب على استخدام `pass` صح.

## Solution

```python
myList = [85, 150, 60, -5, 92, 40]

for num in myList:
    # if num not in range(101): Beacuse range only fet Ints
    if num < 0 or num > 100:
        print(f"Number {num} has been Ignored")
        continue
    else:
        print(f"{num} {'Pass' if num >= 50 else 'Fail'}")
else:
    print("Finished checking all grades")

inventory = {"Laptop": 5, "Mouse": 0, "Keyboard": 12, "Monitor": 3}

while True:
    product_name = input("Please Enter Product Name").capitalize()
    if product_name.upper() == "EXIT":
        break
    print(
        "Out of stock"
        if inventory.get(product_name) == 0
        else inventory.get(product_name)
        if product_name in inventory
        else "Product not found"
    )  # Don't try this at Home

categories = {
    "Electronics": {"Laptop": 5, "Mouse": 0},
    "Furniture": {"Chair": 8, "Desk": 2},
}

categories = {
    "Electronics": {"Laptop": 5, "Mouse": 0},
    "Furniture": {"Chair": 8, "Desk": 2},
}

for category, items_dict in categories.items():
    print(category)
    for product, available in items_dict.items():
        if available < 3:
            pass

        print(f"  {product} available {available}")

```



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

> ⚠️ **Mutable default trap (no C++ equivalent):** the default value is evaluated **once, when the function is defined**, not on every call. C++ evaluates default arguments at each call, so this behavior surprises people coming from it.
> 
> ```python
> def add_item(item, items=[]):     # BAD
>     items.append(item)
>     return items
> 
> print(add_item(1))   # [1]
> print(add_item(2))   # [1, 2]  <- the same list is reused across calls!
> ```
> 
> Fix: use `None` as the default and create the list inside the function:
> 
> ```python
> def add_item(item, items=None):
>     if items is None:
>         items = []
>     items.append(item)
>     return items
> ```

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

## Default Parameter Note

في بايثون، القيم الافتراضية (Default Parameters) بيتم إنشاؤها في الذاكرة "مرة واحدة بس" وقت قراءة بايثون لتعريف الـ Function، مش كل مرة بتستدعيها فيها. ولأن الـ List (نوع بيانات قابل للتعديل - Mutable)، فكل مرة بتستدعي الدالة من غير ما تبعت ليست جديدة، بايثون بيستخدم نفس الليست القديمة المحفوظة في الذاكرة وبيضيف عليها.
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


# Check Points


ال**Checkpoint 1 — حلقات 1 إلى 20 (Syntax أساسي + Data types)**  
مراجعة سريعة، المفاهيم دي عندك من C++ (variables, strings, numbers). الفرق بس في الـ syntax وindentation.  
ال**Task:** اكتب سكريبت بسيط بياخد اسم وعمر من المستخدم ويطبع جملة formatted باستخدام f-strings (مش +).
***DONE***


ال**Checkpoint 2 — حلقات 21 إلى 32 (Lists, Tuples, Sets, Dictionaries)**  
هنا ركز أكتر — دي أقرب لـ STL بتاعتك بس بمرونة مختلفة تمامًا (dynamic, مفيش type واحد للـ container).  
ال**Task:** اكتب برنامج بياخد list of numbers ويستخدم methods مختلفة (append, sort, list comprehension) عشان يرجع لك list تانية فيها بس الأرقام الزوجية × 2. حاول تعمله بـ list comprehension مش loop عادي.

***DONE***

ال **Checkpoint 3 — حلقات 33 إلى 55 (Operators, Conditions, Loops)**  
مراجعة سريعة، فيه بس ملاحظة إن `while...else` و`for...else` مش موجودين في C++.  
**Task:** اكتب password guesser بسيط باستخدام while loop وبردو جرب الـ else بتاعته.

***DONE***

ال **Checkpoint 4 — حلقات 56 إلى 64 (Functions: packing/unpacking, scope, recursion, lambda)**  
تركيز فعلي — الـ `*args`/`**kwargs` والـ lambda مفهوم جديد عليك تمامًا.  
**Task:** اكتب function بتاخد عدد غير محدد من الأرقام (`*args`) وترجع المجموع، وواحدة تانية بـ lambda بتعمل نفس الحاجة لرقمين بس.

***DONE***

**Checkpoint 5 — حلقات 65 إلى 75 (Files Handling + Built-in Functions زي map/filter/reduce)**  
تركيز فعلي — الـ functional style (map/filter/reduce) مختلف عن أسلوب C++ الاعتيادي.  
**Task:** اكتب سكريبت بيقرا ملف نصي، ويستخدم `filter` عشان يجيب بس الأسطر اللي فيها كلمة معينة، ويكتبها في ملف تاني.

***DONE***

**Checkpoint 6 — حلقات 76 إلى 85 (Modules, Iterators/Generators, Decorators)**  
تركيز فعلي جدًا — Generators و Decorators مفهوم مش موجود في C++ خالص.  
**Task:** اكتب generator function بترجع الـ Fibonacci sequence، وبعدين اكتب decorator بسيط بيحسب وقت تنفيذ أي function.

**Checkpoint 7 — حلقات 86 إلى 94 (Practical + Docstrings + Type Hinting)**  
مراجعة خفيفة، بس ركز على Type Hinting — دي هتفيدك جدًا وانت رايح FastAPI لأنها مبنية عليها.  
**Task:** اكتب function بسيطة وحط عليها type hints كاملة (parameters + return type) وdocstring واضح.

**Checkpoint 8 — حلقات 95 إلى 102 (Regular Expressions)** — اختياري دلوقتي، ممكن تأجله لحد ما تحتاجه فعليًا في مشروع.

**Checkpoint 9 — حلقات 103 إلى 116 (OOP كامل)**  
مهم جدًا، بس هنا الفرصة إنك تقارن بعقلية C++ (inheritance, polymorphism, encapsulation) وتشوف الفرق في الـ magic methods وproperty decorators.  
**Task:** حوّل كلاس C++ بسيط كنت كاتبه قبل كده (زي حاجة من مشروع الـ ClsCalculator) لنفس الكلاس بايثون، واستخدم `@property` بدل الـ getters/setters التقليدية.

**Checkpoint 10 — حلقات 117 إلى 127 (SQLite Databases)**  
مهم لمشروعك في الـ backend — دي أول تعامل حقيقي مع قاعدة بيانات من بايثون.  
**Task:** اعمل mini app بسيطة (skills tracker زي في الفيديوهات) بتعمل CRUD كامل (Create, Read, Update, Delete) على SQLite.

**Checkpoint 11 — حلقات 128 إلى 132 (Advanced: `__name__`, Logging, Unit Testing)**  
مهم جدًا لعادات احترافية — الـ logging والـ unit testing هتحتاجهم في أي backend project حقيقي.  
**Task:** خد الـ CRUD app اللي عملته، وضيفله logging بدل الـ print statements، واكتب 2-3 unit tests للفانكشنز الأساسية.

**Checkpoint 12 — حلقات 133 إلى 140 (Flask)** — اختياري بالنسبالك، بما إنك رايح FastAPI مباشرة ممكن تشوفهم بسرعة كخلفية عامة بس متتعمقش، لأن الفلسفة مختلفة (Flask sync مقابل FastAPI async).

**Checkpoint 13 — حلقة 141 (Web Scraping بـ Selenium)** — اختياري، مش مرتبط بهدفك الحالي، سيبه لآخر حاجة.

**Checkpoint 14 — حلقات 142 إلى 149 (Numpy)** — اختياري إلا لو هتشتغل بيانات/ML، مش أولوية للـ backend path بتاعك.

**Checkpoint 15 — حلقات 150 إلى 152 (Virtual Environments)**  
مهم جدًا وقريب النهاية بس متأجلوش — استخدمه من أول ما تبدأ أي مشروع فعلي عشان تتعود على العادة الصح بدري.