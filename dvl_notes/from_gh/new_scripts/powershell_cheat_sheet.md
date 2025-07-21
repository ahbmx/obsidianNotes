# PowerShell Cheat Sheet

## Basic Commands

### Get Help
```powershell
Get-Help <cmdlet>            # Get help for a cmdlet
Get-Help <cmdlet> -Examples  # Show examples
Get-Help <cmdlet> -Full      # Show full help
Get-Command                  # List all available commands
Get-Command -Verb Get        # List all commands with "Get" verb
```

### File System
```powershell
Get-ChildItem (gci, ls, dir)  # List directory contents
Get-ChildItem -Recurse        # List recursively
Get-ChildItem -Hidden         # Show hidden files
Get-Item <path>               # Get file/directory info
Copy-Item (copy, cp) <src> <dest>  # Copy file/directory
Move-Item (move, mv) <src> <dest>  # Move file/directory
Remove-Item (del, rm) <path>  # Delete file/directory
New-Item -ItemType File <path>  # Create new file
New-Item -ItemType Directory <path>  # Create new directory
Test-Path <path>             # Check if path exists
```

### Processes
```powershell
Get-Process (ps)             # List running processes
Get-Process -Name <name>     # Get specific process
Stop-Process -Name <name>    # Stop process by name
Stop-Process -Id <id>        # Stop process by ID
Start-Process <path>         # Start a process
```

### Services
```powershell
Get-Service                  # List all services
Get-Service -Name <name>     # Get specific service
Start-Service -Name <name>   # Start a service
Stop-Service -Name <name>    # Stop a service
Restart-Service -Name <name> # Restart a service
```

### System Information
```powershell
Get-ComputerInfo             # Get comprehensive system info
Get-Date                     # Get current date/time
Get-Host                     # Get host information
$env:USERNAME                # Get current username
$env:COMPUTERNAME            # Get computer name
```

## Working with Objects

### Filtering and Selecting
```powershell
Get-Process | Where-Object {$_.CPU -gt 100}  # Filter by CPU usage
Get-Service | Where-Object {$_.Status -eq "Running"}  # Filter running services
Get-Process | Select-Object Name, ID, CPU    # Select specific properties
```

### Sorting
```powershell
Get-Process | Sort-Object CPU -Descending  # Sort by CPU usage
Get-Service | Sort-Object Status, Name     # Sort by status then name
```

### Grouping
```powershell
Get-Process | Group-Object Company         # Group processes by company
```

### Formatting Output
```powershell
Get-Process | Format-Table -AutoSize       # Display as table
Get-Service | Format-List                  # Display as list
Get-Process | Out-GridView                 # Display in graphical grid
```

## Variables and Data Types

### Variables
```powershell
$var = "value"              # Create variable
$var                        # Access variable
Remove-Variable var         # Remove variable
Get-Variable                # List all variables
```

### Arrays
```powershell
$array = 1,2,3,4            # Create array
$array = @(1,2,3,4)         # Alternative syntax
$array[0]                   # Access first element (0-based)
$array += 5                 # Add element
$array.Count                # Get count
```

### Hashtables
```powershell
$hash = @{Name="John"; Age=30}  # Create hashtable
$hash.Name                     # Access by key
$hash["Name"]                  # Alternative access
$hash.Keys                     # Get all keys
$hash.Values                   # Get all values
```

## Scripting Basics

### Loops
```powershell
# For loop
for ($i=0; $i -lt 10; $i++) { Write-Output $i }

# Foreach loop
foreach ($item in $collection) { Write-Output $item }

# While loop
while ($condition) { # code }

# Do-While loop
do { # code } while ($condition)
```

### Conditionals
```powershell
if ($condition) {
    # code
} elseif ($otherCondition) {
    # code
} else {
    # code
}

switch ($value) {
    "A" { # code }
    "B" { # code }
    default { # code }
}
```

### Functions
```powershell
function Get-HelloWorld {
    param(
        [string]$Name = "World"
    )
    Write-Output "Hello, $Name!"
}

Get-HelloWorld -Name "John"
```

## Remote Management

### Basic Remoting
```powershell
Enter-PSSession -ComputerName Server01  # Start interactive session
Exit-PSSession                         # Exit interactive session
Invoke-Command -ComputerName Server01 -ScriptBlock { Get-Process }  # Run command remotely
```

### Sessions
```powershell
$session = New-PSSession -ComputerName Server01
Invoke-Command -Session $session -ScriptBlock { Get-Service }
Remove-PSSession $session
```

## Modules and Packages

### Module Management
```powershell
Get-Module -ListAvailable     # List available modules
Import-Module <module>        # Import a module
Remove-Module <module>        # Remove a module
Find-Module <name>            # Find modules in gallery
Install-Module <name>         # Install module from gallery
Update-Module <name>          # Update module
```

### Package Management
```powershell
Find-Package <name>           # Find packages
Install-Package <name>        # Install package
Get-Package                   # List installed packages
```

## Error Handling

### Try/Catch
```powershell
try {
    # Code that might fail
} catch {
    Write-Output "Error occurred: $_"
} finally {
    # Cleanup code
}
```

### Error Action Preferences
```powershell
-ErrorAction SilentlyContinue  # Suppress errors
-ErrorAction Stop             # Treat as terminating error
-ErrorAction Continue         # Default - display and continue
-ErrorAction Inquire          # Ask user what to do
```

## File Operations

### Reading Files
```powershell
Get-Content <file>            # Read file line by line
Get-Content <file> -Raw       # Read entire file as single string
```

### Writing Files
```powershell
"text" | Out-File <file>       # Write to file (overwrite)
"text" | Out-File <file> -Append  # Append to file
Set-Content <file> "text"     # Alternative write method
```

### CSV Files
```powershell
Import-Csv <file>             # Import CSV data
$data | Export-Csv <file>     # Export to CSV
```

### JSON Files
```powershell
Get-Content <file> | ConvertFrom-Json  # Import JSON
$data | ConvertTo-Json | Out-File <file>  # Export to JSON
```

## Networking

### Basic Networking
```powershell
Test-NetConnection <host>     # Test network connection
Resolve-DnsName <host>        # DNS lookup
Get-NetIPAddress              # Get IP configuration
Get-NetTCPConnection          # Get active TCP connections
```

### Web Requests
```powershell
Invoke-WebRequest <url>       # Make web request (alias: iwr)
(Invoke-WebRequest <url>).Content  # Get page content
Invoke-RestMethod <url>       # For REST APIs (returns parsed response)
```

## Permissions
```powershell
Get-Acl <path>                # Get permissions for file/folder
Set-Acl <path> -AclObject $acl  # Set permissions
```

## Scheduled Tasks
```powershell
Get-ScheduledTask             # List scheduled tasks
Register-ScheduledTask        # Create new scheduled task
Unregister-ScheduledTask      # Remove scheduled task
```

## Event Logs
```powershell
Get-EventLog -LogName System  # Read system event log
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2}  # Filter events
```

## PowerShell Profile
```powershell
$PROFILE                      # Path to current profile
notepad $PROFILE              # Edit profile
Test-Path $PROFILE            # Check if profile exists
New-Item -ItemType File -Path $PROFILE -Force  # Create profile
```


# **PowerShell Regex Cheat Sheet**  

Regular expressions (regex) in PowerShell are used for pattern matching, text extraction, and string manipulation. PowerShell supports the **`.NET`** regex engine, which is powerful and feature-rich.

---

## **1. Basic Regex Syntax**
### **Common Metacharacters**
| Pattern | Description |
|---------|-------------|
| `.` | Matches any single character (except newline `\n`) |
| `\w` | Matches any word character (`[a-zA-Z0-9_]`) |
| `\W` | Matches any **non-word** character |
| `\d` | Matches any digit (`[0-9]`) |
| `\D` | Matches any **non-digit** character |
| `\s` | Matches any whitespace (space, tab, newline) |
| `\S` | Matches any **non-whitespace** character |
| `^` | Start of string (or line in multiline mode) |
| `$` | End of string (or line in multiline mode) |
| `\b` | Word boundary |
| `\B` | Not a word boundary |

### **Quantifiers (Repetition)**
| Pattern | Description |
|---------|-------------|
| `*` | 0 or more matches |
| `+` | 1 or more matches |
| `?` | 0 or 1 match (optional) |
| `{n}` | Exactly `n` matches |
| `{n,}` | `n` or more matches |
| `{n,m}` | Between `n` and `m` matches |

### **Character Classes**
| Pattern | Description |
|---------|-------------|
| `[abc]` | Matches `a`, `b`, or `c` |
| `[^abc]` | Matches any character **except** `a`, `b`, or `c` |
| `[a-z]` | Matches any lowercase letter |
| `[A-Z]` | Matches any uppercase letter |
| `[0-9]` | Matches any digit |

### **Groups & Capturing**
| Pattern | Description |
|---------|-------------|
| `(abc)` | Capturing group |
| `(?:abc)` | Non-capturing group |
| `(?<name>abc)` | Named capturing group (`$matches['name']`) |

---

## **2. Using Regex in PowerShell**
### **`-match` Operator (Returns `$true`/`$false`)**
```powershell
"PowerShell" -match "Shell"  # Returns $true
"PowerShell" -match "^Power" # Starts with "Power" ($true)
"PowerShell" -match "hell$"  # Ends with "hell" ($true)
```

### **`-notmatch` Operator (Inverse of `-match`)**
```powershell
"PowerShell" -notmatch "Python"  # Returns $true
```

### **`-replace` Operator (Regex Replacement)**
```powershell
"Hello World" -replace "World", "PowerShell"  # "Hello PowerShell"
"123-456-7890" -replace "\D", ""  # Removes non-digits → "1234567890"
```

### **`Select-String` (Find Patterns in Files/Text)**
```powershell
Get-Content "log.txt" | Select-String -Pattern "error"
Select-String -Path "*.log" -Pattern "failed" -CaseSensitive
```

### **`[regex]` .NET Class (Advanced Matching)**
```powershell
[regex]::Matches("abc123", "\d+")  # Returns all digit matches
[regex]::IsMatch("test@example.com", "^\w+@\w+\.\w+$")  # Email check
```

---

## **3. Common Regex Examples**
### **Extract Numbers from a String**
```powershell
$text = "Price: $19.99"
if ($text -match "\d+\.\d+") {
    $matches[0]  # Returns "19.99"
}
```

### **Validate Email Format**
```powershell
$email = "user@example.com"
if ($email -match '^\w+@\w+\.\w+$') {
    Write-Output "Valid email"
}
```

### **Extract Text Between Brackets**
```powershell
$text = "Hello [PowerShell] World"
if ($text -match '\[(.*?)\]') {
    $matches[1]  # Returns "PowerShell"
}
```

### **Split a String by Multiple Delimiters**
```powershell
$text = "apple,banana;orange"
$text -split '[,;]'  # Returns @("apple", "banana", "orange")
```

### **Replace Multiple Spaces with One**
```powershell
$text = "Hello    World"
$text -replace "\s+", " "  # "Hello World"
```

---

## **4. Regex Modifiers**
| Modifier | Description |
|----------|-------------|
| `i` | Case-insensitive matching |
| `m` | Multiline mode (`^` and `$` match line starts/ends) |
| `s` | Single-line mode (`.` matches newlines) |

**Example:**
```powershell
"Hello`nWorld" -match "(?s)Hello.World"  # `(?s)` enables single-line mode
```

---

## **5. Common Pitfalls**
- **Greedy vs. Lazy Matching**  
  - `.*` (greedy) matches as much as possible.  
  - `.*?` (lazy) matches as little as possible.  

- **Escaping Special Characters**  
  - Use `\` to escape `[ ] ( ) { } . * + ? ^ $ \ |`  

- **Performance**  
  - Avoid catastrophic backtracking (e.g., nested quantifiers like `(a+)+`).  

---

### **Summary**
- Use `-match` for pattern checking.
- Use `-replace` for substitutions.
- Use `Select-String` for file searches.
- Use `[regex]` for advanced operations.




# **PowerShell String Manipulation Cheat Sheet**

Strings are fundamental in PowerShell scripting. Here's a comprehensive guide to manipulating strings efficiently.

---

## **1. Basic String Operations**
### **Create a String**
```powershell
$str = "Hello, PowerShell!"
$str = 'Single quotes work too'
```

### **Get String Length**
```powershell
$str.Length  # Returns the number of characters
```

### **Concatenation (Joining Strings)**
```powershell
$str1 = "Hello"
$str2 = "PowerShell"
$combined = $str1 + " " + $str2  # "Hello PowerShell"
$combined = "$str1 $str2"        # String interpolation
```

### **Substrings (Extract Part of a String)**
```powershell
$str = "PowerShell"
$sub = $str.Substring(0, 5)      # "Power" (start at 0, take 5 chars)
$sub = $str.Substring(5)         # "Shell" (from index 5 to end)
```

### **Check if String Contains a Substring**
```powershell
$str = "PowerShell"
$str.Contains("Shell")           # Returns $true
$str -like "*Shell*"            # Wildcard match
$str -match "Shell"             # Regex match
```

### **Check if String Starts/Ends With**
```powershell
$str.StartsWith("Power")        # $true
$str.EndsWith("Shell")          # $true
```

---

## **2. String Formatting**
### **String Interpolation (Variables Inside Strings)**
```powershell
$name = "Alice"
$message = "Hello, $name!"      # "Hello, Alice!"
```

### **Format Strings (`-f` Operator)**
```powershell
"User: {0}, Age: {1}" -f "Alice", 30  # "User: Alice, Age: 30"
"Hex: 0x{0:X4}" -f 255                # "Hex: 0x00FF" (hex formatting)
```

### **Uppercase & Lowercase**
```powershell
$str.ToUpper()    # "POWERSHELL"
$str.ToLower()    # "powershell"
```

### **Trim Whitespace**
```powershell
"   PowerShell   ".Trim()       # "PowerShell" (both sides)
"   PowerShell   ".TrimStart()  # "PowerShell   " (left only)
"   PowerShell   ".TrimEnd()    # "   PowerShell" (right only)
```

### **Pad Strings (Align Text)**
```powershell
"PowerShell".PadLeft(15)       # "     PowerShell" (15 chars total)
"PowerShell".PadRight(15, '-') # "PowerShell------" (pads with '-')
```

---

## **3. String Splitting & Joining**
### **Split a String into an Array**
```powershell
$str = "Apple,Banana,Orange"
$arr = $str -split ","         # @("Apple", "Banana", "Orange")
$arr = $str.Split(",")         # Alternative method
```

### **Join an Array into a String**
```powershell
$arr = "Apple", "Banana", "Orange"
$str = $arr -join ", "         # "Apple, Banana, Orange"
$str = [string]::Join("; ", $arr) # "Apple; Banana; Orange"
```

### **Multi-line Strings (Here-Strings)**
```powershell
$multiLine = @"
Line 1
Line 2
"@
```

---

## **4. String Replacement**
### **Replace Substrings**
```powershell
$str = "Hello, World!"
$newStr = $str.Replace("World", "PowerShell")  # "Hello, PowerShell!"
$newStr = $str -replace "World", "PowerShell"  # Regex-based replace
```

### **Remove Characters**
```powershell
$str = "ABC123"
$newStr = $str -replace "\D", ""  # Remove non-digits → "123"
```

### **Regex Replacements (Advanced)**
```powershell
$str = "Phone: 123-456-7890"
$newStr = $str -replace "\D", ""  # "1234567890" (remove non-digits)
$newStr = $str -replace "(\d{3})-(\d{3})-(\d{4})", '($1) $2-$3'  # "(123) 456-7890"
```

---

## **5. String Parsing & Extraction**
### **Extract Text Between Delimiters**
```powershell
$str = "Name: Alice, Age: 30"
if ($str -match "Name: (.+), Age: (\d+)") {
    $name = $matches[1]  # "Alice"
    $age = $matches[2]   # "30"
}
```

### **Extract Filename & Extension**
```powershell
$path = "C:\Files\document.txt"
$filename = [System.IO.Path]::GetFileName($path)    # "document.txt"
$extension = [System.IO.Path]::GetExtension($path)  # ".txt"
$withoutExt = [System.IO.Path]::GetFileNameWithoutExtension($path) # "document"
```

---

## **6. String Validation**
### **Check if String is Empty/Null**
```powershell
$str = ""
[string]::IsNullOrEmpty($str)      # $true
[string]::IsNullOrWhiteSpace($str) # $true (also checks whitespace)
```

### **Check if String is a Number**
```powershell
$str = "123"
$isNumeric = $str -match "^\d+$"   # $true
$isNumeric = [int]::TryParse($str, [ref]$null)  # $true (try to convert)
```

---

## **7. Encoding & Escaping**
### **Convert to Base64**
```powershell
$str = "PowerShell"
$base64 = [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes($str))
$backToStr = [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($base64))
```

### **Escape Special Characters**
```powershell
[System.Security.SecurityElement]::Escape("<html>")  # "&lt;html&gt;"
```

### **Unescape HTML/XML**
```powershell
[System.Web.HttpUtility]::HtmlDecode("&lt;html&gt;")  # "<html>"
```

---

## **8. Performance Tips**
- **Use `StringBuilder` for heavy string manipulation** (avoids memory overhead):
  ```powershell
  $sb = [System.Text.StringBuilder]::new()
  $sb.Append("Hello").Append(" ").Append("PowerShell") | Out-Null
  $result = $sb.ToString()  # "Hello PowerShell"
  ```
- **Avoid `+=` for large strings** (creates new strings each time).

---

### **Summary**
- **Concatenation:** `+`, `-join`, string interpolation.
- **Substrings:** `.Substring()`, `-split`.
- **Searching:** `-match`, `.Contains()`, `.StartsWith()`.
- **Replacement:** `.Replace()`, `-replace`.
- **Validation:** `-match`, `[string]::IsNullOrEmpty()`.

This cheat sheet covers the most common string operations in PowerShell. 🚀
