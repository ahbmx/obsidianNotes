# Python Cheat Sheet

## Basics
```python
# Comment
print("Hello, World!")  # Output
x = 5                   # Variable assignment
```

## Data Types
```python
# Numeric
int_num = 10            # Integer
float_num = 10.5         # Float
complex_num = 3 + 4j     # Complex

# Sequence
str_val = "Hello"       # String
list_val = [1, 2, 3]    # List (mutable)
tuple_val = (1, 2, 3)   # Tuple (immutable)

# Mapping
dict_val = {"a": 1, "b": 2}  # Dictionary

# Set
set_val = {1, 2, 3}     # Set (unique, unordered)
frozen_set = frozenset({1, 2, 3})  # Immutable set

# Boolean
bool_val = True         # Boolean
```

## Operators
```python
# Arithmetic
x + y, x - y, x * y, x / y, x // y (floor), x % y (mod), x ** y (power)

# Comparison
x == y, x != y, x > y, x < y, x >= y, x <= y

# Logical
and, or, not

# Assignment
=, +=, -=, *=, /=, //=, %=, **=

# Membership
in, not in

# Identity
is, is not
```

## Strings
```python
s = "Python"
len(s)                  # Length
s[0], s[-1]             # Indexing
s[1:4]                  # Slicing
s.upper(), s.lower()    # Case conversion
s.strip()               # Remove whitespace
s.replace("P", "J")     # Replacement
s.split(" ")            # Split into list
" ".join(["a", "b"])    # Join list to string
f"Value: {x}"           # f-string (Python 3.6+)
```

## Lists
```python
lst = [1, 2, 3]
len(lst)                # Length
lst[0], lst[-1]         # Indexing
lst[1:3]                # Slicing
lst.append(4)           # Add item
lst.extend([5, 6])      # Add multiple items
lst.insert(1, 1.5)      # Insert at position
lst.remove(2)           # Remove item
lst.pop(1)              # Remove and return item
lst.index(3)            # Find index
lst.count(1)            # Count occurrences
lst.sort()              # Sort in place
sorted(lst)             # Return new sorted list
lst.reverse()           # Reverse in place
[x*2 for x in lst]      # List comprehension
```

## Dictionaries
```python
d = {"a": 1, "b": 2}
d["a"]                  # Access
d["c"] = 3              # Add/update
del d["b"]              # Delete
d.keys()                # All keys
d.values()              # All values
d.items()               # Key-value pairs
"a" in d                # Check key exists
{k: v*2 for k, v in d.items()}  # Dict comprehension
```

## Control Flow
```python
# If-elif-else
if x > 0:
    print("Positive")
elif x == 0:
    print("Zero")
else:
    print("Negative")

# For loop
for i in range(5):      # 0 to 4
    print(i)
    
for item in [1, 2, 3]:
    print(item)

# While loop
while x > 0:
    print(x)
    x -= 1

# Break/continue
for i in range(10):
    if i == 3:
        continue
    if i == 8:
        break
    print(i)
```

## Functions
```python
def greet(name="World"):
    """Documentation string"""
    return f"Hello, {name}!"

# Lambda function
square = lambda x: x ** 2

# *args and **kwargs
def func(*args, **kwargs):
    print(args)   # Tuple of positional args
    print(kwargs) # Dict of keyword args
```

## File Handling
```python
# Reading
with open("file.txt", "r") as f:
    content = f.read()

# Writing
with open("file.txt", "w") as f:
    f.write("Hello")

# Modes: 'r' (read), 'w' (write), 'a' (append), 'r+' (read/write)
```

## Exception Handling
```python
try:
    x = 1 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
except Exception as e:
    print(f"Error: {e}")
else:
    print("No errors")
finally:
    print("Always executes")
```

## Classes and OOP
```python
class MyClass:
    class_var = 10      # Class variable
    
    def __init__(self, x):
        self.x = x      # Instance variable
    
    def method(self):
        return self.x
    
    @classmethod
    def class_method(cls):
        return cls.class_var
    
    @staticmethod
    def static_method():
        return "Static"

# Inheritance
class ChildClass(MyClass):
    def __init__(self, x, y):
        super().__init__(x)
        self.y = y
```

## Modules
```python
import math
from math import sqrt
from math import pi as PI
```

## Useful Libraries
```python
# Math
import math
math.sqrt(16), math.pi, math.factorial(5)

# Random
import random
random.random(), random.randint(1, 10), random.choice([1, 2, 3])

# Datetime
from datetime import datetime
now = datetime.now()
now.year, now.month, now.day

# JSON
import json
json_str = json.dumps({"a": 1})  # Dict to JSON
data = json.loads(json_str)      # JSON to dict
```



# Python Regular Expressions (Regex) Cheat Sheet

## Basic Patterns
| Pattern | Matches                          | Example                     |
|---------|----------------------------------|-----------------------------|
| `.`     | Any character except newline     | `a.c` → "abc", "a c"        |
| `\w`    | Word character (a-z, A-Z, 0-9, _)| `\w\w` → "ab", "A1"         |
| `\d`    | Digit (0-9)                      | `\d\d` → "42"              |
| `\s`    | Whitespace (space, tab, newline) | `a\sb` → "a b"             |
| `\W`    | Not word character               | `\W` → "@", " "            |
| `\D`    | Not digit                        | `\D` → "a", "@"            |
| `\S`    | Not whitespace                   | `\S\S` → "ab", "1@"        |

## Quantifiers
| Pattern | Matches                          | Example                     |
|---------|----------------------------------|-----------------------------|
| `*`     | 0 or more                        | `a*` → "", "a", "aaa"       |
| `+`     | 1 or more                        | `a+` → "a", "aaa"           |
| `?`     | 0 or 1                           | `a?` → "", "a"              |
| `{n}`   | Exactly n times                  | `a{3}` → "aaa"              |
| `{n,}`  | n or more times                  | `a{2,}` → "aa", "aaaa"      |
| `{n,m}` | Between n and m times            | `a{2,4}` → "aa", "aaaa"     |

## Anchors & Boundaries
| Pattern | Matches                          | Example                     |
|---------|----------------------------------|-----------------------------|
| `^`     | Start of string (or line)        | `^a` → "a" in "abc"         |
| `$`     | End of string (or line)          | `a$` → "a" in "bba"         |
| `\b`    | Word boundary                    | `\bfoo\b` → "foo" in "a foo"|
| `\B`    | Not word boundary                | `foo\B` → "foo" in "foobar" |

## Character Classes
| Pattern       | Matches                          | Example                     |
|---------------|----------------------------------|-----------------------------|
| `[abc]`       | Any of a, b, or c                | `[aeiou]` → "a" in "cat"    |
| `[^abc]`      | Not a, b, or c                   | `[^0-9]` → "a" in "a1"      |
| `[a-z]`       | Any lowercase letter             | `[a-z]` → "b" in "B2b"      |
| `[A-Z]`       | Any uppercase letter             | `[A-Z]` → "B" in "B2b"      |
| `[0-9]`       | Any digit                        | `[0-9]` → "2" in "B2b"      |
| `[a-zA-Z0-9]` | Alphanumeric character           |                             |

## Groups & Capturing
| Pattern       | Matches                          | Example                     |
|---------------|----------------------------------|-----------------------------|
| `(abc)`       | Capture group                    | `(foo)bar` → "foo" in "foobar"|
| `(?:abc)`     | Non-capturing group              | `(?:foo)bar` → no capture   |
| `\1`, `\2`    | Backreference to group 1, 2      | `(a)\1` → "aa"              |
| `(?P<name>...)`| Named capture group             | `(?P<word>\w+)` → named group|

## Lookarounds
| Pattern       | Matches                          | Example                     |
|---------------|----------------------------------|-----------------------------|
| `(?=abc)`     | Positive lookahead               | `a(?=b)` → "a" in "ab"      |
| `(?!abc)`     | Negative lookahead               | `a(?!b)` → "a" in "ac"      |
| `(?<=abc)`    | Positive lookbehind              | `(?<=a)b` → "b" in "ab"     |
| `(?<!abc)`    | Negative lookbehind              | `(?<!a)b` → "b" in "cb"     |

## Special Characters
| Pattern | Matches                          | Example                     |
|---------|----------------------------------|-----------------------------|
| `\n`    | Newline                          |                             |
| `\t`    | Tab                              |                             |
| `\\`    | Literal backslash                | `\\` → "\"                  |
| `\|`    | OR operator                      | `a\|b` → "a" or "b"         |

## Flags/Modifiers
| Flag | Meaning                          | Example                     |
|------|----------------------------------|-----------------------------|
| `re.I` | Case insensitive                | `re.compile(r'a', re.I)`    |
| `re.M` | Multiline (^/$ match lines)     |                             |
| `re.S` | Dot matches newline             |                             |
| `re.X` | Verbose (ignore whitespace)     |                             |

## Python `re` Module Methods
```python
import re

# Searching
re.search(pattern, string)    # Returns first match object or None
re.match(pattern, string)     # Only matches at start of string
re.fullmatch(pattern, string) # Whole string must match

# Finding all matches
re.findall(pattern, string)   # Returns list of all matches
re.finditer(pattern, string)  # Returns iterator of match objects

# Splitting
re.split(pattern, string)     # Split by pattern occurrences

# Replacing
re.sub(pattern, repl, string) # Replace matches with repl
re.subn(pattern, repl, string)# Returns (new_string, num_replacements)

# Compiling (for repeated use)
pattern = re.compile(r'\d+')  # Returns regex object
pattern.search(string)        # Same methods available
```

## Common Regex Examples
```python
# Email validation
email_pattern = r'^[\w\.-]+@[\w\.-]+\.\w+$'

# URL matching
url_pattern = r'https?://(?:[-\w.]|(?:%[\da-fA-F]{2}))+'

# Phone numbers (US format)
phone_pattern = r'\b\d{3}[-.]?\d{3}[-.]?\d{4}\b'

# Date (YYYY-MM-DD)
date_pattern = r'\d{4}-\d{2}-\d{2}'

# HTML tags
html_pattern = r'<[^>]+>'

# Password (8+ chars, 1 upper, 1 lower, 1 digit)
password_pattern = r'^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d]{8,}$'
```

## Match Object Methods
```python
match = re.search(r'(\d+) (\w+)', '100 apples')

match.group()    # Entire match → '100 apples'
match.group(1)   # First group → '100'
match.group(2)   # Second group → 'apples'
match.groups()   # All groups → ('100', 'apples')
match.start()    # Start position → 0
match.end()      # End position → 10
match.span()     # (start, end) → (0, 10)
```

## Tips & Tricks
1. Use raw strings (`r'pattern'`) to avoid escaping backslashes
2. For complex patterns, use `re.VERBOSE` flag and comments
3. Pre-compile patterns with `re.compile()` when reusing them
4. Be specific - greedy quantifiers (`*`, `+`) can match too much
5. Use non-capturing groups `(?:...)` when you don't need the captured text
6. Test your regex with online tools like regex101.com before implementing



# **Python String Manipulation Cheat Sheet**  

Python strings are **immutable**, meaning they cannot be changed after creation. However, you can create new strings using various methods.  

---

## **1. String Basics**
### **Creating Strings**
```python
s1 = "Hello"          # Double quotes
s2 = 'Python'         # Single quotes
s3 = """Multiline     # Triple quotes for multiline strings
String"""
s4 = str(123)         # Convert to string → "123"
```

### **Escape Characters**
| Escape | Meaning          | Example       |
|--------|------------------|---------------|
| `\n`   | Newline          | `"a\nb"` → `a` (newline) `b` |
| `\t`   | Tab              | `"a\tb"` → `a    b` |
| `\\`   | Backslash        | `"C:\\path"` → `C:\path` |
| `\"`   | Double quote     | `"He said, \"Hi\""` |
| `\'`   | Single quote     | `'It\'s me'` → `It's me` |

---

## **2. String Indexing & Slicing**
### **Accessing Characters**
```python
s = "Python"
print(s[0])    # 'P' (first character)
print(s[-1])   # 'n' (last character)
```

### **Slicing (Substrings)**
```python
s = "Python"
print(s[1:4])    # 'yth' (from index 1 to 3)
print(s[:3])     # 'Pyt' (start to index 2)
print(s[3:])     # 'hon' (index 3 to end)
print(s[::2])    # 'Pto' (every 2nd character)
print(s[::-1])   # 'nohtyP' (reverse string)
```

---

## **3. String Methods**
### **Case Conversion**
```python
s = "Python"
print(s.lower())      # 'python'
print(s.upper())      # 'PYTHON'
print(s.capitalize()) # 'Python'
print(s.title())      # 'Python' (capitalizes each word)
print(s.swapcase())   # 'pYTHON'
```

### **Checking String Properties**
```python
s = "Python123"
print(s.isalpha())    # False (only letters?)
print(s.isdigit())    # False (only digits?)
print(s.isalnum())    # True (letters or digits?)
print(s.islower())    # False (all lowercase?)
print(s.isupper())    # False (all uppercase?)
print(s.isspace())    # False (only whitespace?)
print(s.startswith("Py"))  # True
print(s.endswith("23"))    # True
```

### **Searching & Counting**
```python
s = "Hello World"
print(s.find("lo"))      # 3 (index of first occurrence, -1 if not found)
print(s.index("lo"))     # 3 (like find(), but raises ValueError if not found)
print(s.rfind("l"))      # 9 (last occurrence)
print(s.count("l"))      # 3 (number of occurrences)
```

### **Modifying Strings**
```python
s = "  Python  "
print(s.strip())       # 'Python' (removes leading/trailing whitespace)
print(s.lstrip())      # 'Python  ' (left strip)
print(s.rstrip())      # '  Python' (right strip)
print(s.replace("Py", "Ja"))  # '  Javthon  '
print("+".join(["A", "B", "C"]))  # 'A+B+C'
```

### **Splitting & Partitioning**
```python
s = "Python,Java,C++"
print(s.split(","))       # ['Python', 'Java', 'C++']
print(s.split(",", 1))    # ['Python', 'Java,C++'] (max 1 split)
print(s.rsplit(",", 1))   # ['Python,Java', 'C++'] (split from right)
print(s.partition(","))   # ('Python', ',', 'Java,C++') (splits into 3 parts)
```

### **Padding & Alignment**
```python
s = "Python"
print(s.ljust(10, "-"))   # 'Python----' (left-justify)
print(s.rjust(10, "-"))   # '----Python' (right-justify)
print(s.center(10, "-"))  # '--Python--' (center)
print("42".zfill(5))      # '00042' (zero-pad)
```

---

## **4. String Formatting**
### **f-Strings (Python 3.6+)**
```python
name = "Alice"
age = 25
print(f"{name} is {age} years old.")  # 'Alice is 25 years old.'
print(f"10 / 3 = {10 / 3:.2f}")       # '10 / 3 = 3.33' (2 decimal places)
```

### **`format()` Method**
```python
print("{} is {} years old.".format("Bob", 30))  # 'Bob is 30 years old.'
print("{1} comes before {0}".format("B", "A"))  # 'A comes before B'
print("{name} is {age} years old.".format(name="Alice", age=25))
```

### **Old-style (`%` formatting)**
```python
print("%s is %d years old." % ("Alice", 25))  # 'Alice is 25 years old.'
print("Pi = %.2f" % 3.14159)                 # 'Pi = 3.14'
```

---

## **5. Useful String Operations**
### **Checking Substrings**
```python
s = "Hello World"
print("World" in s)    # True
print("Python" not in s)  # True
```

### **String Length**
```python
s = "Python"
print(len(s))  # 6
```

### **Iterating Over Strings**
```python
for char in "Python":
    print(char)  # P, y, t, h, o, n
```

### **ASCII Conversions**
```python
print(ord("A"))  # 65 (ASCII value)
print(chr(65))   # 'A' (character from ASCII)
```

---

## **6. Advanced String Manipulation**
### **Regular Expressions (`re` module)**
```python
import re
text = "Email me at user@example.com"
match = re.search(r'[\w.-]+@[\w.-]+', text)
print(match.group())  # 'user@example.com'
```

### **Removing Punctuation**
```python
import string
s = "Hello, World!"
clean = s.translate(str.maketrans('', '', string.punctuation))
print(clean)  # 'Hello World'
```

### **Reversing a String**
```python
s = "Python"
print(s[::-1])  # 'nohtyP'
```

---

### **Summary Table**
| **Operation**       | **Example**                     | **Result**            |
|----------------------|---------------------------------|-----------------------|
| Length               | `len("Python")`                | `6`                   |
| Uppercase            | `"Python".upper()`             | `"PYTHON"`            |
| Lowercase            | `"Python".lower()`             | `"python"`            |
| Strip whitespace     | `"  Hi  ".strip()`             | `"Hi"`                |
| Replace substring    | `"Hello".replace("H", "J")`    | `"Jello"`             |
| Split into list      | `"A,B,C".split(",")`           | `["A", "B", "C"]`     |
| Join list to string  | `"-".join(["A", "B"])`         | `"A-B"`               |
| Check substring      | `"World" in "Hello World"`     | `True`                |
| Format string        | `f"Value: {x}"`                | `"Value: 10"` (if x=10) |

This cheat sheet covers the most essential string operations in Python. 🚀





