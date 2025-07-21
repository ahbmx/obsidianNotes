# Perl Cheat Sheet

## Basic Syntax
```perl
#!/usr/bin/perl
use strict;    # Enforce good programming practices
use warnings;  # Enable warnings
```

## Variables
```perl
my $scalar = "string";    # Scalar variable
my @array = (1, 2, 3);    # Array
my %hash = (key => 'value'); # Hash

# Special variables
$_  # Default variable
@_  # Subroutine arguments
$!  # Error message
$0  # Program name
```

## Operators
```perl
# Numeric: == != < > <= >= <=>
# String: eq ne lt gt le ge cmp
# Logical: && || ! and or not
# Ternary: condition ? true_expr : false_expr
# Range: .. (1..10)
```

## Control Structures
```perl
# If-elsif-else
if (condition) { ... }
elsif (condition) { ... }
else { ... }

# Unless (opposite of if)
unless (condition) { ... }

# While loop
while (condition) { ... }

# Until loop (opposite of while)
until (condition) { ... }

# For loop
for (my $i=0; $i<10; $i++) { ... }

# Foreach loop
foreach my $item (@array) { ... }
```

## Array Operations
```perl
my @array = (1, 2, 3);
push @array, 4;       # Add to end
pop @array;           # Remove from end
shift @array;         # Remove from beginning
unshift @array, 0;    # Add to beginning
my $len = @array;     # Array length
my $last = $array[-1]; # Last element
```

## Hash Operations
```perl
my %hash = (a => 1, b => 2);
$hash{c} = 3;          # Add key-value
delete $hash{a};       # Remove key-value
my @keys = keys %hash; # Get all keys
my @values = values %hash; # Get all values
```

## Regular Expressions
```perl
# Matching
if ($str =~ /pattern/) { ... }

# Substitution
$str =~ s/old/new/;

# Modifiers
# i - case insensitive
# g - global match
# m - multiline
# s - single line (dot matches newline)
# x - extended (ignore whitespace)

# Special characters
# . ^ $ * + ? { } [ ] \ | ( )
```

## Subroutines
```perl
sub name {
    my ($param1, $param2) = @_;
    # ...
    return $result;
}

# Call subroutine
my $result = name($arg1, $arg2);
```

## File Handling
```perl
# Open file
open(my $fh, '<', 'file.txt') or die "Can't open file: $!";

# Read file
while (my $line = <$fh>) {
    chomp $line; # Remove newline
    # ...
}

# Write to file
open(my $fh_out, '>', 'output.txt');
print $fh_out "text\n";
close $fh_out;
```

## Common Functions
```perl
chomp($var);       # Remove trailing newline
length($str);      # String length
substr($str,0,4);  # Substring
split(/ /, $str);  # Split string
join(",", @array); # Join array elements
sort @array;       # Sort array
reverse @array;    # Reverse array
```

## Perl One-Liners
```bash
# Print lines matching pattern
perl -ne 'print if /pattern/' file.txt

# Print lines 5-10
perl -ne 'print if 5..10' file.txt

# Replace text in file (in-place)
perl -pi -e 's/old/new/g' file.txt

# Sum numbers in a column
perl -lane '$sum += $F[0]; END { print $sum }' file.txt
```

## References
```perl
my $scalar_ref = \$scalar;
my $array_ref = \@array;
my $hash_ref = \%hash;

# Dereferencing
my $value = $$scalar_ref;
my @arr = @$array_ref;
my %hsh = %$hash_ref;

# Anonymous references
my $arr_ref = [1, 2, 3];
my $hash_ref = {a => 1, b => 2};
```

## Modules
```perl
use Module::Name;                  # Import module
use Module::Name qw(func1 func2);  # Import specific functions
require Module::Name;              # Load module at runtime
```

## CPAN
```bash
# Install module
cpan Module::Name

# List installed modules
instmodsh
```

## Object-Oriented Perl
```perl
package MyClass;

sub new {
    my $class = shift;
    my $self = { @_ };
    bless $self, $class;
    return $self;
}

sub method {
    my $self = shift;
    # ...
}

1;  # Required for packages

# Usage
my $obj = MyClass->new(attr => 'value');
$obj->method();
```




# **Perl Regular Expressions (Regex) Cheat Sheet**  
Perl has powerful built-in regex support. Below is a detailed reference for pattern matching, substitution, and modifiers.

---

## **1. Basic Matching**
### **Syntax**
```perl
if ($str =~ /pattern/) { ... }   # True if match found
if ($str !~ /pattern/) { ... }   # True if no match
```

### **Special Characters**
| Character | Meaning |
|-----------|---------|
| `.` | Match any character (except newline) |
| `^` | Start of string (or line in multiline mode) |
| `$` | End of string (or line in multiline mode) |
| `\` | Escape special characters (`\.` matches a literal `.`) |
| `\|` | Alternation (`a\|b` matches `a` or `b`) |

### **Quantifiers**
| Quantifier | Meaning |
|------------|---------|
| `*` | 0 or more times (greedy) |
| `+` | 1 or more times (greedy) |
| `?` | 0 or 1 time (optional) |
| `{n}` | Exactly `n` times |
| `{n,}` | `n` or more times |
| `{n,m}` | Between `n` and `m` times |
| `*?` | Non-greedy `*` |
| `+?` | Non-greedy `+` |
| `??` | Non-greedy `?` |

---

## **2. Character Classes**
| Pattern | Matches |
|---------|---------|
| `[abc]` | `a`, `b`, or `c` |
| `[^abc]` | Any character **not** `a`, `b`, or `c` |
| `[a-z]` | Any lowercase letter |
| `[A-Z]` | Any uppercase letter |
| `[0-9]` | Any digit |
| `\d` | Digit (`[0-9]`) |
| `\D` | Non-digit (`[^0-9]`) |
| `\w` | Word character (`[a-zA-Z0-9_]`) |
| `\W` | Non-word character (`[^\w]`) |
| `\s` | Whitespace (`[ \t\n\r\f]`) |
| `\S` | Non-whitespace (`[^\s]`) |

---

## **3. Grouping & Capturing**
| Pattern | Meaning |
|---------|---------|
| `( ... )` | Capturing group |
| `(?: ... )` | Non-capturing group |
| `\1`, `\2`, ... | Backreference to captured group |
| `(?<name> ... )` | Named capture (access via `$+{name}`) |

### **Example**
```perl
"foo123" =~ /(\w+)(\d+)/;  # $1 = "foo", $2 = "123"
"foo123" =~ /(?<word>\w+)(?<num>\d+)/;  # $+{word} = "foo", $+{num} = "123"
```

---

## **4. Substitution (Search & Replace)**
### **Basic Syntax**
```perl
$str =~ s/pattern/replacement/;   # Replace first match
$str =~ s/pattern/replacement/g;  # Replace all matches (global)
```

### **Replacement Specials**
| Pattern | Meaning |
|---------|---------|
| `$1`, `$2`, ... | Insert captured group |
| `$&` | Entire matched string |
| `` $` `` | Everything before the match |
| `$'` | Everything after the match |
| `\U` | Uppercase next characters |
| `\L` | Lowercase next characters |

### **Example**
```perl
$str = "foo123";
$str =~ s/(\d+)/[$1]/;  # "foo[123]"
$str =~ s/(\w+)/\U$1/;  # "FOO123"
```

---

## **5. Modifiers (Flags)**
| Modifier | Meaning |
|----------|---------|
| `i` | Case-insensitive match |
| `g` | Global match (all occurrences) |
| `m` | Multiline mode (`^` and `$` match start/end of line) |
| `s` | Single-line mode (`.` matches newline) |
| `x` | Extended mode (ignore whitespace, allow comments) |
| `o` | Compile pattern once (optimization) |

### **Example**
```perl
$str =~ /foo bar/ix;  # Case-insensitive, ignores whitespace
```

---

## **6. Lookaheads & Lookbehinds**
| Pattern | Meaning |
|---------|---------|
| `(?=pattern)` | Positive lookahead |
| `(?!pattern)` | Negative lookahead |
| `(?<=pattern)` | Positive lookbehind |
| `(?<!pattern)` | Negative lookbehind |

### **Example**
```perl
# Match "foo" only if followed by "bar"
$str =~ /foo(?=bar)/;

# Match "foo" only if NOT followed by "bar"
$str =~ /foo(?!bar)/;
```

---

## **7. Special Regex Variables**
| Variable | Meaning |
|----------|---------|
| `$&` | Entire matched string |
| `` $` `` | Everything before the match |
| `$'` | Everything after the match |
| `$1`, `$2`, ... | Captured groups |
| `$+` | Last matched group |
| `@-` | Array of match start positions (`$-[0]` is full match) |
| `@+` | Array of match end positions |

### **Example**
```perl
"abc123" =~ /(\d+)/;
print $&;  # "123"
print $`;  # "abc"
print $1;  # "123"
```

---

## **8. Common Regex Examples**
### **Email Matching**
```perl
$email =~ /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
```

### **Extracting Dates (YYYY-MM-DD)**
```perl
$date =~ /(\d{4})-(\d{2})-(\d{2})/;  # $1=year, $2=month, $3=day
```

### **Removing Extra Whitespace**
```perl
$str =~ s/\s+/ /g;      # Replace multiple spaces with one
$str =~ s/^\s+|\s+$//g; # Trim leading/trailing spaces
```

---

## **9. Advanced Features**
### **Conditional Matching**
```perl
# Match "foo" followed by digits or word chars
$str =~ /foo(?(?=\d)\d+|\w+)/;
```

### **Embedded Code Execution**
```perl
# Replace matches with Perl code result
$str =~ s/(\d+)/ $1 * 2 /e;
```

### **Dynamic Regex Interpolation**
```perl
my $word = "foo";
$str =~ /$word/;  # Regex interpolates variables
```

---

### **Summary**
- Use `=~` for matching, `!~` for negated match.
- `s///` for substitution.
- `\d`, `\w`, `\s` for shorthand character classes.
- `( ... )` for capturing, `(?: ... )` for non-capturing.
- `/i`, `/g`, `/m`, `/s`, `/x` for modifiers.
- `$1`, `$2`, `$&`, `` $` ``, `$'` for match variables.

This cheat sheet covers most Perl regex features. For more details, see `perldoc perlre`.



# **Perl String Manipulation Cheat Sheet**  
Perl excels at text processing. Below is a detailed reference for string operations, including functions, regex, and formatting.

---

## **1. Basic String Operations**
### **String Creation**
```perl
my $str = "Hello, World!";
my $multiline = <<'END';  # Here-document
Line 1
Line 2
END
```

### **Concatenation**
```perl
my $str1 = "Hello";
my $str2 = "World";
my $combined = $str1 . ", " . $str2;  # "Hello, World"
```

### **Repetition**
```perl
my $repeated = "abc" x 3;  # "abcabcabc"
```

### **String Length**
```perl
my $length = length($str);  # 13 for "Hello, World!"
```

---

## **2. Substring Operations**
### **Extracting Substrings**
```perl
substr($str, $offset, $length);
```
- **Example:**
  ```perl
  my $str = "Hello, World!";
  my $sub = substr($str, 7, 5);  # "World"
  ```

### **Modifying Substrings**
```perl
substr($str, $offset, $length) = $replacement;
```
- **Example:**
  ```perl
  substr($str, 7, 5) = "Perl";  # "Hello, Perl!"
  ```

### **Negative Offsets**
```perl
my $last_char = substr($str, -1);  # Last character
```

---

## **3. String Splitting & Joining**
### **Splitting into an Array**
```perl
my @words = split(/ /, "Hello World");  # ("Hello", "World")
my @chars = split(//, "abc");          # ("a", "b", "c")
```

### **Joining an Array into a String**
```perl
my $str = join(", ", "a", "b", "c");  # "a, b, c"
```

### **Splitting on Whitespace**
```perl
my @fields = split;  # Default splits on /\s+/
```

---

## **4. String Trimming & Padding**
### **Removing Whitespace**
```perl
$str =~ s/^\s+|\s+$//g;  # Trim both ends
chomp($str);            # Remove trailing newline
```

### **Padding Strings**
```perl
my $padded = sprintf("%-10s", "Hi");  # "Hi        " (left-aligned)
my $padded = sprintf("%10s", "Hi");   # "        Hi" (right-aligned)
```

### **Centering Text**
```perl
use Text::Center;
my $centered = center("Hi", 10);  # "    Hi    "
```

---

## **5. Case Conversion**
| Function | Effect |
|----------|--------|
| `lc($str)` | Lowercase |
| `uc($str)` | Uppercase |
| `lcfirst($str)` | First character lowercase |
| `ucfirst($str)` | First character uppercase |

### **Example**
```perl
my $lower = lc("Hello");  # "hello"
my $upper = uc("Hello");  # "HELLO"
```

### **Inline Case Switching in Substitution**
```perl
$str =~ s/(\w+)/\U$1/g;  # Uppercase all words
$str =~ s/(\w+)/\L$1/g;  # Lowercase all words
```

---

## **6. String Searching & Replacing**
### **Finding Substrings**
```perl
my $pos = index($str, "World");  # Returns position or -1
my $rpos = rindex($str, "l");    # Last occurrence
```

### **Substitution (Regex-Based)**
```perl
$str =~ s/foo/bar/g;  # Replace all "foo" with "bar"
$str =~ s/foo/bar/i;  # Case-insensitive
```

### **Global Replace with Callback**
```perl
$str =~ s/(\d+)/ $1 * 2 /ge;  # Double all numbers
```

---

## **7. String Formatting**
### **`sprintf` (C-Style Formatting)**
```perl
my $formatted = sprintf("%.2f", 3.14159);  # "3.14"
my $hex = sprintf("%x", 255);              # "ff"
```

### **Common Format Specifiers**
| Specifier | Meaning |
|-----------|---------|
| `%s` | String |
| `%d` | Integer |
| `%f` | Floating-point |
| `%x` | Hexadecimal |
| `%o` | Octal |
| `%0Nd` | Zero-padded (e.g., `%05d` → `00123`) |

---

## **8. String Escaping & Quoting**
### **`quotemeta` (Escape Special Characters)**
```perl
my $escaped = quotemeta("$100");  # "\$100"
```

### **`q` and `qq` (Alternative Quotes)**
```perl
my $single = q(It's "cool");  # Single-quoted
my $double = qq(It's "$var"); # Double-quoted (interpolates)
```

### **Heredocs (Multiline Strings)**
```perl
my $text = <<'END';  # Non-interpolated
This is a long
multiline string.
END

my $interpolated = <<"END";  # Interpolated
Hello, $name!
END
```

---

## **9. String Comparison**
| Operator | Meaning |
|----------|---------|
| `eq` | String equality |
| `ne` | String inequality |
| `lt` | Less than (lexicographical) |
| `gt` | Greater than |
| `le` | Less than or equal |
| `ge` | Greater than or equal |
| `cmp` | Comparison (-1, 0, 1) |

### **Example**
```perl
if ($str1 eq $str2) { ... }
if ("apple" lt "banana") { ... }  # True
```

---

## **10. Advanced String Manipulation**
### **Unicode & UTF-8 Handling**
```perl
use utf8;  # Enable UTF-8 in source
binmode STDOUT, ":utf8";  # UTF-8 output

my $length = length(" café ");  # Correct Unicode length
```

### **Base64 Encoding/Decoding**
```perl
use MIME::Base64;
my $encoded = encode_base64("Hello");
my $decoded = decode_base64($encoded);
```

### **URL Encoding/Decoding**
```perl
use URI::Escape;
my $encoded = uri_escape("foo bar");  # "foo%20bar"
my $decoded = uri_unescape($encoded);
```

---

## **Summary**
- **Concatenation:** `.` operator (`$str1 . $str2`)
- **Substrings:** `substr($str, $start, $length)`
- **Splitting:** `split(/ /, $str)`
- **Joining:** `join(", ", @array)`
- **Case Conversion:** `lc()`, `uc()`, `lcfirst()`, `ucfirst()`
- **Trimming:** `s/^\s+|\s+$//g`, `chomp()`
- **Searching:** `index()`, `rindex()`
- **Comparison:** `eq`, `ne`, `lt`, `gt`, `cmp`
- **Formatting:** `sprintf("%.2f", $num)`

This cheat sheet covers most Perl string operations. For more details, see `perldoc perlfunc` and `perldoc perlunicode`.



