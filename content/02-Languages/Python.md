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

python

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

# Check Points

ال**Checkpoint 1 — حلقات 1 إلى 20 (Syntax أساسي + Data types)**  
مراجعة سريعة، المفاهيم دي عندك من C++ (variables, strings, numbers). الفرق بس في الـ syntax وindentation.  
ال**Task:** اكتب سكريبت بسيط بياخد اسم وعمر من المستخدم ويطبع جملة formatted باستخدام f-strings (مش +).
***DONE***


ال**Checkpoint 2 — حلقات 21 إلى 32 (Lists, Tuples, Sets, Dictionaries)**  
هنا ركز أكتر — دي أقرب لـ STL بتاعتك بس بمرونة مختلفة تمامًا (dynamic, مفيش type واحد للـ container).  
ال**Task:** اكتب برنامج بياخد list of numbers ويستخدم methods مختلفة (append, sort, list comprehension) عشان يرجع لك list تانية فيها بس الأرقام الزوجية × 2. حاول تعمله بـ list comprehension مش loop عادي.





**Checkpoint 3 — حلقات 33 إلى 55 (Operators, Conditions, Loops)**  
مراجعة سريعة، فيه بس ملاحظة إن `while...else` و`for...else` مش موجودين في C++.  
**Task:** اكتب password guesser بسيط باستخدام while loop وبردو جرب الـ else بتاعته.

**Checkpoint 4 — حلقات 56 إلى 64 (Functions: packing/unpacking, scope, recursion, lambda)**  
تركيز فعلي — الـ `*args`/`**kwargs` والـ lambda مفهوم جديد عليك تمامًا.  
**Task:** اكتب function بتاخد عدد غير محدد من الأرقام (`*args`) وترجع المجموع، وواحدة تانية بـ lambda بتعمل نفس الحاجة لرقمين بس.

**Checkpoint 5 — حلقات 65 إلى 75 (Files Handling + Built-in Functions زي map/filter/reduce)**  
تركيز فعلي — الـ functional style (map/filter/reduce) مختلف عن أسلوب C++ الاعتيادي.  
**Task:** اكتب سكريبت بيقرا ملف نصي، ويستخدم `filter` عشان يجيب بس الأسطر اللي فيها كلمة معينة، ويكتبها في ملف تاني.

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