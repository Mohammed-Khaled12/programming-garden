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


