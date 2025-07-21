# Perl Cheat Sheet

## Basic Syntax

```perl
#!/usr/bin/perl
use strict;    # Enforce good programming practices
use warnings;  # Enable additional warnings
use v5.10;     # Use Perl 5.10 features
```

## Variables

### Scalars
```perl
my $scalar = "string";  # String
my $num = 42;           # Number
my $float = 3.14;       # Floating point
```

### Arrays
```perl
my @array = (1, 2, "three");
my $first = $array[0];  # Access element (zero-based)
$array[2] = 3;          # Modify element
my $len = @array;       # Array length
```

### Hashes (Associative Arrays)
```perl
my %hash = ("key1" => "value1", "key2" => "value2");
my $val = $hash{"key1"};  # Access value
$hash{"new_key"} = 42;    # Add new key-value pair
my @keys = keys %hash;    # Get all keys
```

## Special Variables

```perl
$_    # Default variable
@_    # Subroutine arguments
$0    # Program name
$!    # Error message
$.    # Current line number
@ARGV # Command line arguments
```

## Operators

### Numeric Operators
```perl
+ - * / % **  # Basic math
++ --         # Increment/decrement
== != < > <= >=  # Comparisons
```

### String Operators
```perl
.   # Concatenation
x   # Repetition (e.g., "a" x 3 = "aaa")
eq ne lt le gt ge  # String comparisons
```

### Logical Operators
```perl
&& || !   # Logical AND, OR, NOT
and or not  # Lower precedence versions
```

## Control Structures

### Conditionals
```perl
if ($condition) {
    # code
} elsif ($other_condition) {
    # code
} else {
    # code
}

# Ternary operator
$result = $condition ? $true_value : $false_value;
```

### Loops
```perl
# While loop
while ($condition) {
    # code
    last;   # break
    next;   # continue
}

# For loop
for (my $i = 0; $i < 10; $i++) {
    # code
}

# Foreach loop
foreach my $item (@array) {
    # code
}

# Until loop
until ($condition) {
    # code
}
```

## Regular Expressions

### Matching
```perl
if ($string =~ /pattern/) {
    # Match found
}

# Capture groups
if ($string =~ /(pattern1).*(pattern2)/) {
    my $capture1 = $1;
    my $capture2 = $2;
}
```

### Substitution
```perl
$string =~ s/pattern/replacement/;      # First occurrence
$string =~ s/pattern/replacement/g;     # Global replacement
$string =~ s/pattern/replacement/i;     # Case insensitive
```

### Common Regex Patterns
```perl
.       # Any character (except newline)
\w      # Word character [a-zA-Z0-9_]
\W      # Non-word character
\s      # Whitespace character
\S      # Non-whitespace character
\d      # Digit [0-9]
\D      # Non-digit
^       # Start of string
$       # End of string
*       # 0 or more
+       # 1 or more
?       # 0 or 1
{n}     # Exactly n times
{n,}    # At least n times
{n,m}   # Between n and m times
```

## Subroutines

```perl
sub greet {
    my ($name) = @_;  # Get arguments
    return "Hello, $name!";
}

my $message = greet("Alice");
```

### Prototypes
```perl
sub add($$) {  # Takes exactly two scalar arguments
    my ($a, $b) = @_;
    return $a + $b;
}
```

## File Handling

### Reading Files
```perl
open(my $fh, '<', 'file.txt') or die "Can't open file: $!";
while (my $line = <$fh>) {
    chomp $line;  # Remove newline
    # process $line
}
close($fh);
```

### Writing Files
```perl
open(my $fh, '>', 'output.txt') or die "Can't open file: $!";
print $fh "This goes to the file\n";
close($fh);
```

### File Tests
```perl
if (-e $file) { print "File exists\n"; }
if (-d $file) { print "It's a directory\n"; }
if (-f $file) { print "It's a regular file\n"; }
if (-s $file) { print "Size is ".(-s $file)."\n"; }
if (-r $file) { print "Readable\n"; }
if (-w $file) { print "Writable\n"; }
if (-x $file) { print "Executable\n"; }
```

## Modules and Packages

### Using Modules
```perl
use Module::Name;            # Import all default symbols
use Module::Name qw(func);   # Import specific functions
use Module::Name ();         # No imports
```

### Creating a Module
```perl
package My::Module;

use strict;
use warnings;
use Exporter qw(import);

our @EXPORT_OK = qw(function1 function2);

sub function1 { ... }
sub function2 { ... }

1;  # Module must return true
```

## Object-Oriented Perl

### Creating a Class
```perl
package Person;

sub new {
    my ($class, %args) = @_;
    my $self = {
        name => $args{name} || 'Anonymous',
        age  => $args{age}  || 0,
    };
    bless $self, $class;
    return $self;
}

sub greet {
    my ($self) = @_;
    return "Hi, I'm $self->{name}";
}

1;
```

### Using the Class
```perl
use Person;

my $person = Person->new(name => 'Alice', age => 30);
print $person->greet();
```

## Common Functions

### String Functions
```perl
chomp($str);    # Remove trailing newline
chop($str);     # Remove last character
length($str);   # String length
substr($str, $offset, $length);  # Extract substring
index($str, $substr);  # Find position of substring
uc($str);       # Uppercase
lc($str);       # Lowercase
ucfirst($str);  # Uppercase first character
join($delim, @list);  # Join list into string
split(/pattern/, $str);  # Split string into list
```

### Array Functions
```perl
push(@array, $item);    # Add to end
pop(@array);            # Remove from end
shift(@array);          # Remove from beginning
unshift(@array, $item); # Add to beginning
sort(@array);           # Sort array
reverse(@array);        # Reverse array
grep { condition } @array;  # Filter array
map { transform } @array;   # Transform array
```

### Hash Functions
```perl
keys(%hash);    # Get all keys
values(%hash);  # Get all values
each(%hash);    # Iterate key-value pairs
exists($hash{$key});  # Check if key exists
delete($hash{$key});  # Remove key-value pair
```

### Time Functions
```perl
time();         # Current epoch time
localtime();    # Current local time
gmtime();      # Current UTC time
sleep($sec);    # Pause execution
```

## Command Line Arguments

```perl
my $script = $0;              # Script name
my $first_arg = $ARGV[0];     # First argument
my @all_args = @ARGV;         # All arguments
```

## References

### Creating References
```perl
my $scalar_ref = \$scalar;
my $array_ref = \@array;
my $hash_ref = \%hash;
my $sub_ref = \&subroutine;

# Anonymous references
my $anon_array = [1, 2, 3];
my $anon_hash = { key => 'value' };
my $anon_sub = sub { ... };
```

### Dereferencing
```perl
my $value = $$scalar_ref;
my @array = @$array_ref;
my %hash = %$hash_ref;
my $item = $array_ref->[0];
my $val = $hash_ref->{key};
&$sub_ref();
```

## Error Handling

### eval
```perl
eval {
    # code that might die
};
if ($@) {
    # handle error
}
```

### die and warn
```perl
die "Fatal error message\n" if $error_condition;
warn "Warning message\n" if $warning_condition;
```

## Useful Special Variables

```perl
$^O       # Operating system name
$^V       # Perl version
$^X       # Perl executable name
@INC      # Array of module search paths
%ENV      # Environment variables
$]        # Perl version number
$\        # Output record separator
$,        # Output field separator
$"        # List separator (for interpolation)
```

## Perl One-Liners

```bash
# Print lines matching a pattern
perl -ne 'print if /pattern/' file.txt

# Print line numbers with lines
perl -ne 'print "$.: $_"' file.txt

# Replace text in file (in-place)
perl -pi -e 's/old/new/g' file.txt

# Sum numbers in a column
perl -lane '$sum += $F[2]; END { print $sum }' file.txt

# Randomize lines in a file
perl -MList::Util=shuffle -e 'print shuffle(<>)' file.txt
```

## CPAN Modules

### Commonly Used Modules
```perl
use Data::Dumper;       # Data structure pretty printing
use Getopt::Long;       # Command line option parsing
use JSON;               # JSON encoding/decoding
use LWP::Simple;        # Simple web requests
use File::Path;         # Create/remove directory trees
use File::Find;         # File system traversal
use DateTime;           # Date/time manipulation
use DBI;                # Database interface
use Moose;              # Modern object system
use Try::Tiny;          # Try/catch exception handling
```

## Best Practices

1. Always use `strict` and `warnings`
2. Use lexical variables (`my`) instead of package variables
3. Check return values of system calls and file operations
4. Use meaningful variable names
5. Comment complex code sections
6. Use modules instead of reinventing the wheel
7. Handle errors gracefully
8. Keep lines under 80 characters when possible
9. Use consistent indentation
10. Avoid using `$_` when it makes code less clear

This cheat sheet covers the most essential Perl concepts and syntax. For more detailed information, consult the official Perl documentation (`perldoc`) or visit https://perldoc.perl.org/.


### 1. File Parsing with Regex to CSV Example

```perl
#!/usr/bin/perl
use strict;
use warnings;
use Text::CSV;

# File parsing with regex example - output to CSV
my $logfile = 'server.log';
my $csv_file = 'log_analysis.csv';

# Check if file exists and is readable
unless (-e $logfile && -r $logfile) {
    die "Error: Cannot read file '$logfile'\n";
}

# Prepare CSV output
my $csv = Text::CSV->new({ binary => 1, auto_diag => 1, eol => "\n" });
open(my $csv_fh, '>', $csv_file) or die "Cannot open '$csv_file': $!";

# Write CSV header
$csv->print($csv_fh, ['Line Number', 'Timestamp', 'Error Type', 'IP Address', 'HTTP Status']);

open(my $fh, '<', $logfile) or die "Cannot open '$logfile': $!";

print "Processing log file: $logfile\n";
print "Results will be saved to: $csv_file\n";
print "=" x 50 . "\n";

my %error_counts;
my $line_count = 0;

while (my $line = <$fh>) {
    $line_count++;
    chomp $line;
    
    my ($timestamp, $error, $ip, $status) = ('', '', '', '');
    
    # Extract timestamp and error message
    if ($line =~ /^(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) .*ERROR: (.+)$/) {
        ($timestamp, $error) = ($1, $2);
        $error_counts{$error}++;
    }
    
    # Extract IP addresses
    if ($line =~ /(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})/) {
        $ip = $1;
    }
    
    # Extract HTTP status codes
    if ($line =~ /HTTP\/\d\.\d"\s+(\d{3})\s/) {
        $status = $1;
    }
    
    # Write to CSV if we found anything
    if ($timestamp || $error || $ip || $status) {
        $csv->print($csv_fh, [$line_count, $timestamp, $error, $ip, $status]);
    }
}

close($fh);
close($csv_fh);

# Generate summary CSV
my $summary_csv = 'error_summary.csv';
open(my $summary_fh, '>', $summary_csv) or die "Cannot open '$summary_csv': $!";
$csv->print($summary_fh, ['Error Type', 'Occurrences']);
foreach my $error (sort keys %error_counts) {
    $csv->print($summary_fh, [$error, $error_counts{$error}]);
}
close($summary_fh);

print "\nProcessing complete.\n";
print "Detailed results saved to: $csv_file\n";
print "Error summary saved to: $summary_csv\n";
print "Processed $line_count lines.\n";
```

### 2. System Command Execution with Regex Parsing to CSV Example

```perl
#!/usr/bin/perl
use strict;
use warnings;
use Text::CSV;

# System command execution with regex parsing to CSV
my $os = $^O;  # Get operating system
my $csv_file = 'network_info.csv';

my $command = ($os eq 'MSWin32') ? 'ipconfig /all' : 'ifconfig';

print "Executing: $command\n";
print "Results will be saved to: $csv_file\n";
print "=" x 50 . "\n";

# Prepare CSV output
my $csv = Text::CSV->new({ binary => 1, auto_diag => 1, eol => "\n" });
open(my $csv_fh, '>', $csv_file) or die "Cannot open '$csv_file': $!";

# Write appropriate CSV header
if ($os eq 'MSWin32') {
    $csv->print($csv_fh, ['Adapter Name', 'IPv4 Address', 'MAC Address']);
} else {
    $csv->print($csv_fh, ['Interface Name', 'IPv4 Address', 'MAC Address', 'RX Packets', 'RX Errors']);
}

# Execute command and capture output
my @output = `$command`;
if ($? != 0) {
    die "Command failed with error: $!\n";
}

if ($os eq 'MSWin32') {
    # Windows ipconfig parsing
    my ($current_adapter, $ip, $mac) = ('', '', '');
    
    foreach my $line (@output) {
        chomp $line;
        
        # New adapter section
        if ($line =~ /^([^:]+):\s*$/) {
            # Save previous adapter data if we have it
            if ($current_adapter && ($ip || $mac)) {
                $csv->print($csv_fh, [$current_adapter, $ip, $mac]);
                ($ip, $mac) = ('', '');
            }
            $current_adapter = $1;
        }
        
        # Extract IPv4 address
        elsif ($line =~ /IPv4 Address[^:]+:\s*([\d.]+)/) {
            $ip = $1;
        }
        
        # Extract MAC address
        elsif ($line =~ /Physical Address[^:]+:\s*([\w-]+)/) {
            $mac = $1;
        }
    }
    
    # Save the last adapter
    if ($current_adapter && ($ip || $mac)) {
        $csv->print($csv_fh, [$current_adapter, $ip, $mac]);
    }
} else {
    # Unix/Linux ifconfig parsing
    my ($current_interface, $ip, $mac, $packets, $errors) = ('', '', '', '', '');
    
    foreach my $line (@output) {
        chomp $line;
        
        # New interface section
        if ($line =~ /^([^\s:]+):?\s/) {
            # Save previous interface data if we have it
            if ($current_interface && ($ip || $mac || $packets || $errors)) {
                $csv->print($csv_fh, [$current_interface, $ip, $mac, $packets, $errors]);
                ($ip, $mac, $packets, $errors) = ('', '', '', '');
            }
            $current_interface = $1;
        }
        
        # Extract IPv4 address
        if ($line =~ /inet (\d+\.\d+\.\d+\.\d+)/) {
            $ip = $1;
        }
        
        # Extract MAC address
        elsif ($line =~ /ether ([\da-fA-F:]+)/) {
            $mac = $1;
        }
        
        # Extract packet statistics
        elsif ($line =~ /RX packets (\d+).*errors (\d+)/) {
            ($packets, $errors) = ($1, $2);
        }
    }
    
    # Save the last interface
    if ($current_interface && ($ip || $mac || $packets || $errors)) {
        $csv->print($csv_fh, [$current_interface, $ip, $mac, $packets, $errors]);
    }
}

close($csv_fh);
print "\nCommand output processed successfully.\n";
print "Results saved to: $csv_file\n";
```

### Key Improvements:

1. **Added CSV Output**:
   - Both scripts now use the `Text::CSV` module for proper CSV handling
   - Output is saved to CSV files with appropriate headers
   - Data is properly escaped for CSV format

2. **Better Data Organization**:
   - The log parser creates two CSV files (detailed data and error summary)
   - The network command script adapts its output format based on OS

3. **Error Handling**:
   - Proper file opening checks
   - Command execution status checking

4. **Cleaner Output**:
   - Progress messages show where results are being saved
   - CSV files are properly closed after writing

To use these scripts, you'll need to install the `Text::CSV` module if you don't already have it:
```bash
cpan install Text::CSV
```

The CSV files produced can be easily opened in spreadsheet software like Excel or imported into databases for further analysis.
