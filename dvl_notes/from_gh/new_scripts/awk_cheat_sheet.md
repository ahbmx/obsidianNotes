# AWK Cheat Sheet

## Basic Syntax
```awk
awk 'pattern { action }' input_file
```

## Common Patterns
- `BEGIN { }` - Executes before processing any lines
- `END { }` - Executes after processing all lines
- `/regex/` - Lines matching regular expression
- `NR == 1` - First line (record)
- `NR > 1` - All lines except first
- `$1 == "value"` - First field equals "value"

## Built-in Variables
- `NR` - Number of Records (current line number)
- `NF` - Number of Fields in current record
- `FS` - Field Separator (default: whitespace)
- `OFS` - Output Field Separator (default: space)
- `RS` - Record Separator (default: newline)
- `ORS` - Output Record Separator (default: newline)
- `FILENAME` - Current input file name

## Field References
- `$0` - Entire line
- `$1` - First field
- `$2` - Second field
- `$NF` - Last field
- `$(NF-1)` - Second last field

## Common Actions
```awk
{ print $0 }          # Print entire line
{ print $1, $3 }      # Print 1st and 3rd fields
{ print NF }          # Print number of fields
{ print NR, $0 }      # Print line number and line
{ print $1 > "file" } # Redirect output to file
```

## Operators
- Arithmetic: `+ - * / % ^`
- Comparison: `== != < <= > >=`
- Logical: `&& || !`
- Regex: `~ !~` (matches/doesn't match)
- Ternary: `condition ? if-true : if-false`

## String Functions
```awk
length(string)        # Length of string
substr(string, start, length) # Substring
index(string, find)   # Position of substring
split(string, array, sep) # Split into array
tolower(string)       # Convert to lowercase
toupper(string)       # Convert to uppercase
gsub(regex, replacement) # Global substitution
sub(regex, replacement) # Single substitution
match(string, regex)  # Match regex, sets RSTART/RLENGTH
```

## Numeric Functions
```awk
int(x)       # Integer part
sqrt(x)      # Square root
exp(x)       # Exponential
log(x)       # Natural logarithm
sin(x), cos(x), atan2(y, x) # Trig functions
rand()       # Random number 0-1
srand([x])   # Seed random number generator
```

## Arrays
```awk
arr[key] = value      # Assign value
delete arr[key]       # Delete element
for (key in arr)      # Iterate through array
```

## Examples

### Basic Printing
```awk
awk '{print $1}' file.txt          # Print first column
awk '{print $NF}' file.txt         # Print last column
awk 'NR > 5 {print $0}' file.txt   # Print lines after 5
```

### Field Separators
```awk
awk -F',' '{print $2}' file.csv    # Use comma as separator
awk -F'\t' '{print $1}' file.tsv   # Use tab as separator
awk 'BEGIN {FS=":"} {print $1}' /etc/passwd
```

### Calculations
```awk
awk '{sum += $1} END {print sum}' file.txt  # Sum first column
awk '{avg = total/NR} END {print avg}'      # Calculate average
```

### Pattern Matching
```awk
awk '/error/ {print $0}' logfile    # Print lines containing "error"
awk '$3 > 100 {print $1, $3}' data  # Print if 3rd field > 100
```

### Advanced Examples
```awk
# Count lines in file
awk 'END {print NR}' file.txt

# Print unique lines
awk '!seen[$0]++' file.txt

# Sum values by key (like SQL GROUP BY)
awk '{sum[$1] += $2} END {for (k in sum) print k, sum[k]}' data

# Print lines longer than 80 chars
awk 'length($0) > 80' file.txt

# Print filename with line count
awk 'END {print FILENAME, NR}' file.txt
```

## Command Line Options
- `-F fs` - Set field separator
- `-v var=value` - Assign variable
- `-f script.awk` - Read program from file
- `-W option` - Enable extended features (GNU awk)


# AWK Regular Expressions (Regex) Cheat Sheet

## Basic Regex Syntax in AWK

### Anchors
- `^` - Start of line/string
- `$` - End of line/string
- `\b` - Word boundary
- `\B` - Not a word boundary

### Character Classes
- `.` - Any single character except newline
- `[abc]` - Any of a, b, or c
- `[^abc]` - Not a, b, or c
- `[a-z]` - Any lowercase letter
- `[A-Z]` - Any uppercase letter
- `[0-9]` - Any digit
- `\d` - Digit (in GNU awk, equivalent to `[0-9]`)
- `\D` - Non-digit
- `\w` - Word character (alphanumeric + underscore)
- `\W` - Non-word character
- `\s` - Whitespace character
- `\S` - Non-whitespace character

### Quantifiers
- `*` - 0 or more
- `+` - 1 or more (use `\+` in basic regex)
- `?` - 0 or 1 (use `\?` in basic regex)
- `{n}` - Exactly n times
- `{n,}` - n or more times
- `{n,m}` - Between n and m times

### Groups and Alternation
- `(regex)` - Group (capturing)
- `(?:regex)` - Non-capturing group (GNU awk)
- `|` - Alternation (OR)
- `\1`, `\2`, etc. - Backreferences to captured groups

### Special Characters
- `\` - Escape special character
- `\t` - Tab
- `\n` - Newline
- `\r` - Carriage return
- `\f` - Form feed
- `\v` - Vertical tab

## AWK Regex Operators
- `~` - Matches regex
- `!~` - Doesn't match regex

## Common AWK Regex Patterns

### Matching Patterns
```awk
# Lines starting with "Error"
/^Error/ {print}

# Lines ending with "warning"
/warning$/ {print}

# Lines containing "error" (case insensitive)
BEGIN {IGNORECASE=1}  # GNU awk only
/error/ {print}

# Lines where 2nd field matches date pattern (YYYY-MM-DD)
$2 ~ /^[0-9]{4}-[0-1][0-9]-[0-3][0-9]$/ {print}
```

### Substitution
```awk
# Replace "color" with "colour" (first occurrence per line)
{sub(/color/, "colour"); print}

# Replace all occurrences (global substitution)
{gsub(/color/, "colour"); print}

# Case-insensitive substitution (GNU awk)
{gsub(/color/, "colour", $0); print}  # Works on $0 by default
```

### Extraction
```awk
# Extract numbers from a string
{match($0, /[0-9]+/); print substr($0, RSTART, RLENGTH)}

# Extract email addresses
{match($0, /[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}/); 
 print substr($0, RSTART, RLENGTH)}
```

### Validation
```awk
# Validate IP address (simple version)
$0 ~ /^([0-9]{1,3}\.){3}[0-9]{1,3}$/ {print "Valid IP:", $0}

# Validate email (basic pattern)
$0 ~ /^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$/ {print "Valid email:", $0}
```

## GNU AWK Specific Extensions

### Regex Constants
```awk
BEGIN {
    email_regex = "@[[:alnum:]]+\\.[[:alpha:]]{2,}"
    if ("test@example.com" ~ email_regex) print "Valid"
}
```

### Bracket Expressions
- `[:alnum:]` - Alphanumeric characters
- `[:alpha:]` - Alphabetic characters
- `[:blank:]` - Space or tab
- `[:cntrl:]` - Control characters
- `[:digit:]` - Digits
- `[:graph:]` - Printable characters (not space)
- `[:lower:]` - Lowercase letters
- `[:print:]` - Printable characters (including space)
- `[:punct:]` - Punctuation
- `[:space:]` - Whitespace
- `[:upper:]` - Uppercase letters
- `[:xdigit:]` - Hexadecimal digits

### Regex Operators
- `\y` - Match at start or end of word
- `\<` - Start of word
- `\>` - End of word

## Practical Examples

### Log File Analysis
```awk
# Count errors by type from log file
/ERROR/ {errors[$5]++}  # Assuming error type is in 5th field
END {for (e in errors) print e, errors[e]}
```

### Data Cleaning
```awk
# Remove non-numeric characters from field 3
{gsub(/[^0-9.]/, "", $3); print $3}
```

### URL Extraction
```awk
# Extract URLs from text
{
    while (match($0, /https?:\/\/[[:alnum:]_\/?=&.-]+/)) {
        print substr($0, RSTART, RLENGTH)
        $0 = substr($0, RSTART + RLENGTH)
    }
}
```

### CSV Processing
```awk
# Handle quoted fields in CSV
BEGIN {FPAT = "([^,]+)|(\"[^\"]+\")"}  # GNU awk
{
    for (i=1; i<=NF; i++) {
        gsub(/^"|"$/, "", $i)  # Remove surrounding quotes
        print "Field", i, ":", $i
    }
}
```

Remember that AWK has two modes for regular expressions:
1. Basic Regular Expressions (BRE) - traditional UNIX style
2. Extended Regular Expressions (ERE) - more modern (use with `-E` option or in GNU awk)

GNU awk (gawk) provides many additional regex features beyond standard AWK.



# **AWK String Manipulation Cheat Sheet**  

AWK provides powerful string manipulation functions for text processing. Below is a detailed cheat sheet covering common string operations.

---

## **1. Basic String Operations**
### **Printing Strings**
```awk
{ print $0 }       # Print entire line
{ print $1 }       # Print first field
{ print $1, $3 }   # Print first and third fields
```

### **String Concatenation**
```awk
{ print $1 $2 }          # Concatenate without separator
{ print $1 " " $2 }      # Concatenate with space
{ print $1, $2 }         # Concatenate with OFS (default: space)
{ full = $1 "-" $2 }     # Store concatenated string in a variable
```

---

## **2. String Functions**
### **Length of a String**
```awk
{ print length($1) }     # Length of the first field
{ print length("text") } # Length of a literal string
```

### **Substring Extraction**
```awk
{ print substr($1, 2, 3) }  # From position 2, extract 3 chars
{ print substr($0, 1, 5) }  # First 5 chars of the line
```

### **Case Conversion**
```awk
{ print tolower($1) }    # Convert to lowercase
{ print toupper($1) }    # Convert to uppercase
```

### **Trimming Whitespace (Manual Approach)**
```awk
# Remove leading whitespace
{ sub(/^[ \t]+/, "", $1) }

# Remove trailing whitespace
{ sub(/[ \t]+$/, "", $1) }

# Remove both leading & trailing whitespace
{ gsub(/^[ \t]+|[ \t]+$/, "", $1) }
```

---

## **3. Search & Replace**
### **Single Replacement (`sub`)**
```awk
{ sub(/old/, "new", $1) }  # Replace first occurrence in $1
{ sub(/old/, "new") }      # Replace first occurrence in $0
```

### **Global Replacement (`gsub`)**
```awk
{ gsub(/old/, "new", $1) } # Replace all occurrences in $1
{ gsub(/old/, "new") }     # Replace all occurrences in $0
```

### **Replace Using Regex**
```awk
# Remove all digits
{ gsub(/[0-9]/, "", $1) }

# Replace multiple spaces with one
{ gsub(/ +/, " ", $0) }

# Remove special characters
{ gsub(/[^a-zA-Z0-9 ]/, "", $0) }
```

---

## **4. Splitting & Joining Strings**
### **Splitting into an Array (`split`)**
```awk
{
    split($0, arr, ",")   # Split line into array by comma
    for (i in arr) print arr[i]
}

{
    split("one:two:three", arr, ":")
    print arr[2]  # Output: "two"
}
```

### **Joining Fields (Using Loop)**
```awk
{
    str = ""
    for (i=1; i<=NF; i++) str = str $i "-"
    print substr(str, 1, length(str)-1)  # Remove trailing "-"
}
```

---

## **5. Searching in Strings**
### **Check if String Contains a Pattern**
```awk
# Using `index` (returns position or 0)
{ if (index($1, "text") > 0) print "Found" }

# Using regex match (`~`)
{ if ($0 ~ /pattern/) print "Match found" }
```

### **Find Position of Substring (`index`)**
```awk
{ pos = index($1, "substr") }
{ print "Position:", pos }
```

### **Regex Matching (`match`)**
```awk
{
    if (match($0, /[0-9]{3}-[0-9]{2}-[0-9]{4}/)) {
        print "Found SSN:", substr($0, RSTART, RLENGTH)
    }
}
```

---

## **6. Advanced String Manipulation**
### **Padding Strings**
```awk
# Left-pad with zeros (e.g., "5" → "005")
{ printf "%03d", $1 }

# Right-pad with spaces
{ printf "%-10s", $1 }
```

### **Extracting Filename from Path**
```awk
{
    split("/path/to/file.txt", parts, "/")
    filename = parts[length(parts)]
    print filename  # Output: "file.txt"
}
```

### **Extracting File Extension**
```awk
{
    if (match($0, /\.([^\.]+)$/, ext)) {
        print "Extension:", ext[1]
    }
}
```

---

## **7. AWK Built-in Variables for Strings**
| Variable | Description |
|----------|-------------|
| `FS`     | Input field separator (default: whitespace) |
| `OFS`    | Output field separator (default: space) |
| `RS`     | Input record separator (default: newline) |
| `ORS`    | Output record separator (default: newline) |
| `SUBSEP` | Separator for multi-dimensional arrays (default: "\034") |

---

## **8. Practical Examples**
### **1. Extract Domain from Email**
```awk
{
    split($0, parts, "@")
    print "Domain:", parts[2]
}
```

### **2. Count Words in a Line**
```awk
{ print "Words:", NF }
```

### **3. Reverse a String**
```awk
{
    str = "hello"
    rev = ""
    for (i=length(str); i>0; i--) rev = rev substr(str, i, 1)
    print rev  # Output: "olleh"
}
```

### **4. Remove Duplicate Spaces**
```awk
{ gsub(/ +/, " ", $0); print }
```

### **5. Capitalize First Letter**
```awk
{
    first = toupper(substr($1, 1, 1))
    rest = substr($1, 2)
    print first rest
}
```

---

### **Summary**
| Task | AWK Function |
|------|-------------|
| Get length | `length(str)` |
| Extract substring | `substr(str, start, length)` |
| Convert case | `tolower(str)`, `toupper(str)` |
| Replace first match | `sub(/old/, "new", str)` |
| Replace all matches | `gsub(/old/, "new", str)` |
| Split into array | `split(str, arr, separator)` |
| Find substring position | `index(str, "substr")` |
| Regex matching | `match(str, /regex/)` (sets `RSTART`, `RLENGTH`) |

This cheat sheet covers the most common AWK string manipulations. Let me know if you need more specific examples! 🚀
