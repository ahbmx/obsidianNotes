# Python Regex Cheat Sheet

## Basic Patterns
- `.` - Any character except newline
- `\w` - Word character (a-z, A-Z, 0-9, _)
- `\d` - Digit (0-9)
- `\s` - Whitespace (space, tab, newline)
- `\W` - Not a word character
- `\D` - Not a digit
- `\S` - Not whitespace
- `[abc]` - Any of a, b, or c
- `[^abc]` - Not a, b, or c
- `[a-z]` - Any lowercase letter
- `[A-Z]` - Any uppercase letter
- `[0-9]` - Any digit

## Anchors
- `^` - Start of string (or line in multiline mode)
- `$` - End of string (or line in multiline mode)
- `\b` - Word boundary
- `\B` - Not a word boundary
- `\A` - Start of string (regardless of multiline mode)
- `\Z` - End of string (regardless of multiline mode)

## Quantifiers
- `*` - 0 or more (greedy)
- `+` - 1 or more (greedy)
- `?` - 0 or 1 (greedy)
- `{3}` - Exactly 3
- `{3,}` - 3 or more
- `{3,5}` - 3, 4, or 5
- `*?` - 0 or more (lazy)
- `+?` - 1 or more (lazy)
- `??` - 0 or 1 (lazy)

## Groups and Capturing
- `(...)` - Capturing group
- `(?:...)` - Non-capturing group
- `(?P<name>...)` - Named capturing group
- `\1` - Reference first captured group
- `(?P=name)` - Reference named group
- `(?#...)` - Comment (ignored)

## Lookarounds
- `(?=...)` - Positive lookahead
- `(?!...)` - Negative lookahead
- `(?<=...)` - Positive lookbehind
- `(?<!...)` - Negative lookbehind

## Flags (Modifiers)
- `re.I` or `re.IGNORECASE` - Case insensitive
- `re.M` or `re.MULTILINE` - Multiline mode (^ and $ match start/end of line)
- `re.S` or `re.DOTALL` - Dot matches newline
- `re.X` or `re.VERBOSE` - Allow whitespace and comments
- `re.A` or `re.ASCII` - ASCII-only character classes
- `re.L` or `re.LOCALE` - Locale-aware matching (deprecated in Python 3)

## Special Characters
- `\n` - Newline
- `\t` - Tab
- `\r` - Carriage return
- `\\` - Backslash
- `\.` - Literal dot
- `\*` - Literal asterisk
- `\+` - Literal plus
- `\?` - Literal question mark
- `\{` - Literal curly brace
- `\}` - Literal closing curly brace
- `\(` - Literal parenthesis
- `\)` - Literal closing parenthesis
- `\[` - Literal square bracket
- `\]` - Literal closing square bracket
- `\|` - Literal pipe

## Common Patterns
- Email: `r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'`
- URL: `r'https?://(?:www\.)?[a-zA-Z0-9-]+\.[a-zA-Z]{2,}(?:/[^\s]*)?'`
- IPv4: `r'\b(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b'`
- Date (YYYY-MM-DD): `r'\d{4}-(?:0[1-9]|1[0-2])-(?:0[1-9]|[12][0-9]|3[01])'`
- Time (24-hour): `r'(?:[01][0-9]|2[0-3]):[0-5][0-9](?::[0-5][0-9])?'`
- HTML tag: `r'<([a-z]+)([^<]+)*(?:>(.*)<\/\1>|\s+\/>)'`
- Credit Card (Visa/Mastercard): `r'^(?:4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14})$'`

## Python re Module Methods
- `re.compile(pattern, flags=0)` - Compile regex pattern
- `re.search(pattern, string, flags=0)` - Search for first match
- `re.match(pattern, string, flags=0)` - Match at beginning of string
- `re.fullmatch(pattern, string, flags=0)` - Match entire string
- `re.findall(pattern, string, flags=0)` - Find all non-overlapping matches
- `re.finditer(pattern, string, flags=0)` - Return iterator of match objects
- `re.sub(pattern, repl, string, count=0, flags=0)` - Replace matches
- `re.split(pattern, string, maxsplit=0, flags=0)` - Split string by pattern

## Match Object Methods
- `group()` or `group(0)` - Entire match
- `group(1)` - First parenthesized subgroup
- `groups()` - All captured subgroups as tuple
- `start()` - Start position of match
- `end()` - End position of match
- `span()` - (start, end) tuple
- `groupdict()` - Dictionary of named groups

## Examples

```python
import re

# Basic search
text = "The quick brown fox jumps over the lazy dog"
match = re.search(r'fox', text)
if match:
    print("Found:", match.group())  # Found: fox

# Extracting email addresses
emails = "Contact us at support@example.com or sales@company.org"
email_pattern = r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'
found_emails = re.findall(email_pattern, emails)
print(found_emails)  # ['support@example.com', 'sales@company.org']

# Splitting with multiple delimiters
text = "apple,banana;cherry/orange"
items = re.split(r'[,;/]', text)
print(items)  # ['apple', 'banana', 'cherry', 'orange']

# Using groups
text = "Date: 2023-04-15"
date_match = re.match(r'Date: (\d{4})-(\d{2})-(\d{2})', text)
if date_match:
    year, month, day = date_match.groups()
    print(f"Year: {year}, Month: {month}, Day: {day}")

# Substitution with backreferences
text = "hello world"
new_text = re.sub(r'(\w+) (\w+)', r'\2 \1', text)
print(new_text)  # "world hello"

# Named groups
text = "James: 42, Sarah: 31"
pattern = r'(?P<name>\w+): (?P<age>\d+)'
for match in re.finditer(pattern, text):
    print(f"{match.group('name')} is {match.group('age')} years old")

# Lookahead example
text = "apple orange banana pear"
# Match 'orange' only if followed by 'banana'
match = re.search(r'orange(?=\sbanana)', text)
if match:
    print("Found:", match.group())  # Found: orange
```

## Tips
1. Use raw strings (`r'pattern'`) to avoid escaping backslashes
2. Compile frequently used patterns with `re.compile()` for better performance
3. Use `re.VERBOSE` flag for complex patterns to add comments and whitespace
4. Be careful with greedy vs lazy quantifiers
5. Test your regex patterns with online tools like regex101.com before implementation
6. Consider using `re.finditer()` for large texts instead of `re.findall()` to save memory
7. For complex text processing, consider combining regex with string methods

Remember that while regex is powerful, it's not always the best solution - sometimes simple string methods are more readable and maintainable.



# Greedy vs. Lazy Quantifiers in Regex

## Greedy Quantifiers (Default Behavior)

Greedy quantifiers match as much as possible while still allowing the overall pattern to match. They're the default behavior in regular expressions.

### Common greedy quantifiers:
- `*` - 0 or more (as many as possible)
- `+` - 1 or more (as many as possible)
- `?` - 0 or 1 (prefers 1 if possible)
- `{n,}` - At least n (as many as possible)
- `{n,m}` - Between n and m (as many as possible)

### Example:
```python
import re

text = "<div>content</div><div>more content</div>"

# Greedy match (default)
greedy_match = re.search(r'<div>.*</div>', text)
print(greedy_match.group())
# Output: <div>content</div><div>more content</div>
```

The greedy `.*` matches everything from the first `<div>` to the last `</div>` in the string.

## Lazy (Non-Greedy) Quantifiers

Lazy quantifiers (also called "reluctant" or "minimal") match as little as possible while still allowing the overall pattern to match. You make a quantifier lazy by adding a `?` after it.

### Common lazy quantifiers:
- `*?` - 0 or more (as few as possible)
- `+?` - 1 or more (as few as possible)
- `??` - 0 or 1 (prefers 0)
- `{n,}?` - At least n (as few as possible)
- `{n,m}?` - Between n and m (as few as possible)

### Example:
```python
# Lazy match
lazy_match = re.search(r'<div>.*?</div>', text)
print(lazy_match.group())
# Output: <div>content</div>
```

The lazy `.*?` stops at the first `</div>` it encounters.

## Key Differences

| Characteristic | Greedy | Lazy |
|---------------|--------|------|
| Behavior | Matches as much as possible | Matches as little as possible |
| Syntax | `*`, `+`, `?`, `{n,m}` | `*?`, `+?`, `??`, `{n,m}?` |
| Performance | May cause excessive backtracking | Generally more efficient |
| Typical Use | When you want to match everything between delimiters | When you want to match the shortest possible segment |

## Practical Examples

### 1. HTML Tag Matching
```python
html = "<p>Paragraph 1</p><p>Paragraph 2</p>"

# Greedy - matches everything between first <p> and last </p>
print(re.findall(r'<p>.*</p>', html)[0])
# Output: <p>Paragraph 1</p><p>Paragraph 2</p>

# Lazy - matches individual paragraphs
print(re.findall(r'<p>.*?</p>', html))
# Output: ['<p>Paragraph 1</p>', '<p>Paragraph 2</p>']
```

### 2. Extracting Quoted Text
```python
text = 'He said "Hello" and then "Goodbye"'

# Greedy - matches everything between first and last quote
print(re.findall(r'"(.*)"', text)[0])
# Output: Hello" and then "Goodbye

# Lazy - matches each quoted segment separately
print(re.findall(r'"(.*?)"', text))
# Output: ['Hello', 'Goodbye']
```

### 3. Number Extraction
```python
numbers = "12345"

# Greedy - matches all digits
print(re.match(r'\d+', numbers).group())
# Output: 12345

# Lazy - matches just one digit
print(re.match(r'\d+?', numbers).group())
# Output: 1
```

## When to Use Each

**Use greedy quantifiers when:**
- You want to match everything between two markers
- You're working with well-structured data where greediness won't cause problems
- You need to capture the largest possible valid match

**Use lazy quantifiers when:**
- You want to match the smallest possible segment between delimiters
- You're parsing HTML/XML (to avoid matching across tags)
- You're working with text that contains multiple similar patterns
- You want to improve regex performance by reducing backtracking

## Performance Considerations

Greedy quantifiers can sometimes lead to catastrophic backtracking, especially with nested patterns or ambiguous text. Lazy quantifiers often perform better in these cases because they don't try to match everything at once.

```python
# Potentially problematic greedy pattern
re.match(r'<div>(.*)<div>(.*)</div>(.*)</div>', html)

# Better with lazy quantifiers
re.match(r'<div>(.*?)<div>(.*?)</div>(.*?)</div>', html)
```

Remember that neither approach is universally better - choose based on your specific matching requirements.



# Handling Missing Capture Groups in Python Regex

When working with regex capture groups in Python, you may encounter situations where not all groups you defined are actually present in the input string. Here's how to handle these cases effectively:

## Understanding the Behavior

By default, when a capture group doesn't match anything:
- The group will return `None` if you try to access it directly
- It will be included in `groups()` as `None`
- It won't appear in `groupdict()` if it's a named group

## Common Approaches

### 1. Checking for `None` Values

```python
import re

text = "Date: 2023-04"
pattern = r'Date: (\d{4})-(\d{2})-(\d{2})'  # 3 groups defined
match = re.search(pattern, text)

if match:
    # Safe way to access groups
    year = match.group(1)  # '2023'
    month = match.group(2)  # '04'
    day = match.group(3)  # None
    
    # Provide default values
    day = match.group(3) if match.group(3) is not None else '01'
```

### 2. Using `groups()` with Default Values

```python
if match:
    # groups() returns a tuple with None for missing groups
    year, month, day = match.groups('01')  # '01' is default for missing groups
    print(year, month, day)  # 2023 04 01
```

### 3. Working with Optional Groups

Make groups optional with `?` quantifier:

```python
pattern = r'Date: (\d{4})-(\d{2})(?:-(\d{2}))?'  # last group is optional
match = re.search(pattern, "Date: 2023-04")
if match:
    print(match.groups())  # ('2023', '04', None)
```

### 4. Using Named Groups with `groupdict()`

```python
pattern = r'Date: (?P<year>\d{4})(?:-(?P<month>\d{2}))?(?:-(?P<day>\d{2}))?'
match = re.search(pattern, "Date: 2023")

if match:
    result = {k: v for k, v in match.groupdict().items() if v is not None}
    print(result)  # {'year': '2023'}
    
    # With defaults
    defaults = {'year': '0000', 'month': '01', 'day': '01'}
    result = {**defaults, **match.groupdict()}
    print(result)  # {'year': '2023', 'month': '01', 'day': '01'}
```

### 5. Using the `or` Operator for Defaults

```python
if match:
    day = match.group(3) or '01'  # Uses '01' if group(3) is None or empty
```

## Best Practices

1. **Make groups explicitly optional** when they might not exist in the input
   ```python
   # Better than: r'Name: (\w+)(?: (\w+))?'
   pattern = r'Name: (\w+)(?: (\w+))?'
   ```

2. **Use named groups** for better readability and maintenance
   ```python
   pattern = r'(?P<first>\w+)(?: (?P<last>\w+))?'
   ```

3. **Provide sensible defaults** when groups might be missing
   ```python
   day = match.group('day') or '01'
   ```

4. **Validate matches** before accessing groups
   ```python
   if match and match.group(1):
       # Process the match
   ```

5. **Consider using `fullmatch()`** when you need all groups to be present
   ```python
   if re.fullmatch(r'\d{4}-\d{2}-\d{2}', text):
       # All parts are present
   ```

## Advanced Technique: Custom Wrapper Function

```python
def safe_match(pattern, text, defaults=None):
    match = re.search(pattern, text)
    if not match:
        return None if defaults is None else defaults
    
    if defaults:
        # Merge defaults with matched groups
        result = {**defaults, **match.groupdict()}
        # Handle positional groups if needed
        for i, group in enumerate(match.groups(), 1):
            if group is not None:
                result[f'group_{i}'] = group
        return result
    
    return match

# Usage:
result = safe_match(
    r'(?P<year>\d{4})-(?P<month>\d{2})(?:-(?P<day>\d{2}))?',
    '2023-04',
    defaults={'day': '01'}
)
# Returns {'year': '2023', 'month': '04', 'day': '01'}
```

Remember that handling missing groups gracefully makes your code more robust and prevents exceptions when processing real-world data that may not always match your expected patterns perfectly.



