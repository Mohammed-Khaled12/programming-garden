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
