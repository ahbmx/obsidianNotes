# **AWK Cheat Sheet**  

AWK is a powerful text-processing language for pattern scanning and processing. It is particularly useful for manipulating structured data, extracting fields, and generating reports.  

---

## **1. Basic Syntax**  
```sh
awk 'pattern { action }' file
```
- If `pattern` is omitted, the `action` applies to every line.  
- If `action` is omitted, the default action is `{ print }`.  

### **Examples**  
```sh
awk '{ print }' file.txt          # Print all lines  
awk '/pattern/ { print }' file    # Print lines matching "pattern"  
awk '{ print $1 }' file.txt       # Print first field of each line  
```

---

## **2. Built-in Variables**  
| Variable | Description |  
|----------|------------|  
| `$0` | Entire input line |  
| `$1, $2, ..., $NF` | Fields (columns) in the line |  
| `NF` | Number of fields in the current line |  
| `NR` | Current record (line) number |  
| `FNR` | Record number in the current file |  
| `FS` | Field separator (default: whitespace) |  
| `OFS` | Output field separator (default: space) |  
| `RS` | Record separator (default: newline) |  
| `ORS` | Output record separator (default: newline) |  
| `FILENAME` | Name of the current input file |  
| `ARGC` | Number of command-line arguments |  
| `ARGV` | Array of command-line arguments |  

### **Examples**  
```sh
awk '{ print NR, $0 }' file.txt            # Print line numbers  
awk -F',' '{ print $1 }' file.csv          # Use comma as separator  
awk 'BEGIN { FS=":"; OFS="--" } { print $1, $3 }' /etc/passwd  
```

---

## **3. Patterns**  
### **Regex Patterns**  
```sh
awk '/error/ { print }' file.log          # Lines containing "error"  
awk '!/warning/ { print }' file.log       # Lines NOT containing "warning"  
awk '$1 ~ /^[0-9]+$/ { print }' file.txt  # First field is numeric  
```

### **Comparison Patterns**  
```sh
awk '$3 > 100 { print }' data.txt        # 3rd field > 100  
awk '$1 == "admin" { print }' users.txt  # 1st field equals "admin"  
awk 'NR >= 10 && NR <= 20' file.txt      # Lines 10 to 20  
```

### **Range Patterns**  
```sh
awk '/start/, /end/' file.txt            # Print lines between "start" and "end"  
```

### **BEGIN and END Blocks**  
```sh
awk 'BEGIN { print "Start" } { print } END { print "End" }' file.txt  
```

---

## **4. Actions (Common Operations)**  
### **Printing**  
```sh
awk '{ print $1, $3 }' file.txt          # Print columns 1 and 3  
awk '{ print "Line:", NR, "Fields:", NF }' file.txt  
```

### **Conditional Statements**  
```sh
awk '{ if ($1 > 50) print "High:", $0; else print "Low:", $0 }' data.txt  
```

### **Loops**  
```sh
awk '{ for (i=1; i<=NF; i++) print $i }' file.txt  # Print each field  
```

### **String Operations**  
```sh
awk '{ print length($0), $0 }' file.txt            # Line length  
awk '{ print substr($1, 1, 3) }' file.txt          # First 3 chars of 1st field  
awk '{ print toupper($1) }' file.txt               # Uppercase  
awk '{ gsub(/old/, "new"); print }' file.txt       # Replace text  
```

### **Math Operations**  
```sh
awk '{ sum += $1 } END { print sum }' numbers.txt  # Sum of first column  
awk '{ avg = total / NR } END { print avg }' data.txt  
```

---

## **5. Advanced AWK**  
### **Arrays (Associative Arrays)**  
```sh
awk '{ count[$1]++ } END { for (key in count) print key, count[key] }' log.txt  
```

### **Functions**  
```sh
awk 'function myfunc(x) { return x * 2 } { print myfunc($1) }' data.txt  
```

### **Multiple Files**  
```sh
awk '{ print FILENAME, $0 }' file1.txt file2.txt  
```

### **Using Shell Variables**  
```sh
var="hello"  
awk -v awk_var="$var" '{ print awk_var, $0 }' file.txt  
```

---

## **6. Common One-Liners**  
- **Count lines in a file:**  
  ```sh
  awk 'END { print NR }' file.txt  
  ```
- **Sum a column:**  
  ```sh
  awk '{ sum += $1 } END { print sum }' data.txt  
  ```
- **Print unique values in a column:**  
  ```sh
  awk '{ seen[$1]++ } END { for (i in seen) print i }' file.txt  
  ```
- **Extract lines between two patterns:**  
  ```sh
  awk '/start/,/end/' file.txt  
  ```
- **Print lines longer than 80 chars:**  
  ```sh
  awk 'length($0) > 80' file.txt  
  ```
- **Reverse fields in a line:**  
  ```sh
  awk '{ for (i=NF; i>=1; i--) printf "%s ", $i; print "" }' file.txt  
  ```

---

## **7. Command-Line Options**  
| Option | Description |  
|--------|-------------|  
| `-F` | Set field separator (`-F:`, `-F', '`) |  
| `-v` | Pass variable (`-v var=value`) |  
| `-f` | Read AWK script from file (`-f script.awk`) |  

---

# **Regex Matching in AWK**  

AWK provides powerful regex (regular expression) matching capabilities for pattern scanning and text extraction. Regex can be used in **patterns** or within **actions** using operators like `~` (match) and `!~` (not match).  

---

## **1. Basic Regex Matching Syntax**  

### **Matching Entire Line**  
```sh
awk '/regex/ { action }' file
```
- If a line matches `regex`, the `action` is executed.  

**Example:**  
```sh
awk '/error/ { print }' log.txt        # Print lines containing "error"  
awk '/^[0-9]/ { print }' data.txt     # Print lines starting with a digit  
```

---

### **Matching a Specific Field**  
Use the `~` (match) and `!~` (not match) operators:  
```sh
awk '$2 ~ /regex/ { print }' file     # 2nd field matches regex  
awk '$1 !~ /[0-9]/ { print }' file    # 1st field has no digits  
```

**Example:**  
```sh
awk '$3 ~ /^[A-Z]/ { print }' users.txt   # 3rd field starts with uppercase  
awk '$1 !~ /admin/ { print }' log.txt     # 1st field does not contain "admin"  
```

---

## **2. Common Regex Patterns**  

| Regex | Meaning | Example |
|-------|---------|---------|
| `^` | Start of line/string | `awk '/^start/ { print }'` |
| `$` | End of line/string | `awk '/end$/ { print }'` |
| `.` | Any single character | `awk '/a.b/ { print }'` (matches "aab", "axb") |
| `*` | Zero or more of the previous | `awk '/go*d/ { print }'` (matches "gd", "god", "good") |
| `+` | One or more of the previous | `awk '/go+d/ { print }'` (matches "god", "good") |
| `?` | Zero or one of the previous | `awk '/go?d/ { print }'` (matches "gd", "god") |
| `[]` | Character class | `awk '/[0-9]/ { print }'` (matches any digit) |
| `[^]` | Negated character class | `awk '/[^0-9]/ { print }'` (matches non-digit) |
| `|` | OR condition | `awk '/error|warning/ { print }'` |
| `()` | Grouping | `awk '/(error|warning)/ { print }'` |
| `\` | Escape special chars | `awk '/\.com/ { print }'` (matches ".com") |

---

## **3. Dynamic Regex Matching with Variables**  
You can store regex in a variable and use it later:  
```sh
awk -v pattern="error" '$0 ~ pattern' log.txt  
```

**Example:**  
```sh
awk -v domain="gmail.com" '$5 ~ domain' emails.txt  
```

---

## **4. Using `gsub`, `sub`, `match` for Regex Actions**  

### **`gsub(regex, replacement, string)`**  
- **Globally substitutes** all matches of `regex` with `replacement`.  
```sh
awk '{ gsub(/old/, "new"); print }' file.txt  
```

### **`sub(regex, replacement, string)`**  
- **Substitutes only the first match** (not global).  
```sh
awk '{ sub(/:/, "-"); print }' data.txt  
```

### **`match(string, regex)`**  
- Returns the **position** of the match (or `0` if no match).  
- Stores matched groups in `RSTART` and `RLENGTH`.  
```sh
awk '{ if (match($0, /[0-9]+/)) print substr($0, RSTART, RLENGTH) }' file.txt  
```

**Example:** Extract numbers from a line:  
```sh
awk '{ while(match($0, /[0-9]+/)) { print substr($0, RSTART, RLENGTH); $0=substr($0, RSTART+RLENGTH) } }' file.txt  
```

---

## **5. Practical Examples**  

### **1. Extract Email Addresses**  
```sh
awk '{ while(match($0, /[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}/)) { print substr($0, RSTART, RLENGTH); $0=substr($0, RSTART+RLENGTH) } }' emails.txt  
```

### **2. Find Lines with IP Addresses**  
```sh
awk '/[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+/ { print }' log.txt  
```

### **3. Replace Multiple Spaces with One**  
```sh
awk '{ gsub(/ +/, " "); print }' file.txt  
```

### **4. Extract Words in Uppercase**  
```sh
awk '{ while(match($0, /[A-Z][A-Z]+/)) { print substr($0, RSTART, RLENGTH); $0=substr($0, RSTART+RLENGTH) } }' file.txt  
```

### **5. Check if a Field is Numeric**  
```sh
awk '$1 ~ /^[0-9]+$/ { print "Numeric:", $1 }' data.txt  
```

---

## **6. Using `IGNORECASE` for Case-Insensitive Matching**  
```sh
awk 'BEGIN { IGNORECASE=1 } /error/ { print }' log.txt  
```

---

## **Conclusion**  
Regex in AWK is extremely powerful for:  
- Filtering lines (`/regex/`).  
- Matching fields (`$2 ~ /regex/`).  
- Substituting text (`gsub`, `sub`).  
- Extracting patterns (`match`).  
