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

| Method           | Description                                                                                                               |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `b.title()`      | Capitalizes the first letter of **every word**. If a digit precedes a word, the letter right after it is also capitalized |
| `b.capitalize()` | Capitalizes only the **first letter of the whole string**, the rest becomes lowercase                                     |
| `g.upper()`      | Converts the entire string to UPPERCASE                                                                                   |
| `g.lower()`      | Converts the entire string to lowercase                                                                                   |
| `s.swapcase()`   | Flips the case: uppercase becomes lowercase and vice versa                                                                |

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

