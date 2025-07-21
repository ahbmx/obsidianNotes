Here's the modified Python regex that:
1. Finds WWN anywhere in the string (not just whole-string matches)
2. Captures the WWN in a named group called `symdev_wwn`
3. Still handles both colon-separated and non-colon formats

```python
import re

wwn_regex = re.compile(
    r'(?P<symdev_wwn>(?<!\S)(?:[0-9a-fA-F]{2}[:]?){7}[0-9a-fA-F]{2}(?!\S))', 
    re.IGNORECASE
)
```

### Key Changes:
1. Removed `^` and `$` anchors to allow matching anywhere in the string
2. Added `(?P<symdev_wwn>...)` named capture group
3. Added `(?<!\S)` and `(?!\S)` to ensure we match whole WWNs (not parts of longer hex strings)
4. Added `re.IGNORECASE` flag to make it case-insensitive (cleaner than `[a-fA-F]`)

### Usage Example:
```python
text = "The device with wwn 01:23:45:67:89:ab:cd:ef is offline. Backup wwn 0123456789abcdef is active."

matches = wwn_regex.finditer(text)
for match in matches:
    print(f"Found WWN: {match.group('symdev_wwn')}")
```

### Output:
```
Found WWN: 01:23:45:67:89:ab:cd:ef
Found WWN: 0123456789abcdef
```

This will properly:
- Find WWNs anywhere in text
- Capture them in the named group
- Handle both formats
- Avoid partial matches from longer hex strings
- Work case-insensitively
