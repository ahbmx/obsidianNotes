# Bash Scripting Cheat Sheet

## Basic Script Structure
```bash
#!/bin/bash
# This is a comment

# Commands go here
echo "Hello, World!"
```

## Variables
```bash
variable_name="value"        # No spaces around =
echo $variable_name         # Use $ to access
echo "${variable_name}"     # Preferred syntax for interpolation

readonly CONSTANT="fixed"   # Read-only variable
unset variable_name        # Delete variable
```

## Special Variables
```bash
$0      # Script name
$1-$9   # Positional parameters (arguments)
$#      # Number of arguments
$@      # All arguments as separate words
$*      # All arguments as single word
$?      # Exit status of last command
$$      # Process ID of current shell
$!      # Process ID of last background command
```

## Arrays
```bash
array=("value1" "value2" "value3")
echo ${array[1]}           # Access element (0-based)
echo ${array[@]}           # All elements
echo ${#array[@]}          # Number of elements
array+=("value4")          # Append element
```

## Arithmetic Operations
```bash
((result = 5 + 3))         # Integer arithmetic
let "result = 5 + 3"       # Alternative syntax
result=$((5 + 3))          # Store result
echo $((5 % 3))            # Modulo operation
```

## Conditionals
```bash
# If-Then-Else
if [ condition ]; then
    commands
elif [ condition ]; then
    commands
else
    commands
fi

# Case statement
case $variable in
    pattern1)
        commands;;
    pattern2)
        commands;;
    *)
        default commands;;
esac
```

## Test Conditions
```bash
# File tests
[ -e file ]        # Exists
[ -f file ]        # Regular file
[ -d file ]        # Directory
[ -s file ]        # Not empty
[ -r file ]        # Readable
[ -w file ]        # Writable
[ -x file ]        # Executable

# String comparisons
[ "$a" = "$b" ]    # Equal
[ "$a" != "$b" ]   # Not equal
[ -z "$a" ]        # Empty string
[ -n "$a" ]        # Not empty string

# Numeric comparisons
[ $a -eq $b ]      # Equal
[ $a -ne $b ]      # Not equal
[ $a -lt $b ]      # Less than
[ $a -le $b ]      # Less or equal
[ $a -gt $b ]      # Greater than
[ $a -ge $b ]      # Greater or equal

# Combined conditions
[ condition1 ] && [ condition2 ]  # AND
[ condition1 ] || [ condition2 ]  # OR
```

## Loops
```bash
# For loop
for var in list; do
    commands
done

# C-style for loop
for ((i=0; i<10; i++)); do
    commands
done

# While loop
while [ condition ]; do
    commands
done

# Until loop
until [ condition ]; do
    commands
done

# Loop control
break       # Exit loop
continue    # Skip to next iteration
```

## Functions
```bash
function_name() {
    local local_var="value"  # Local variable
    commands
    return value            # Return status (0-255)
}

# Call function
function_name arg1 arg2

# Access arguments in function
$1, $2, etc.  # Function parameters
$#            # Number of arguments
$@            # All arguments
```

## Input/Output
```bash
echo "text"              # Print to stdout
printf "format" args     # Formatted output
read variable           # Read from stdin
read -p "Prompt: " var  # Prompt before reading
read -s password        # Silent input (for passwords)
```

## File Operations
```bash
cat file                 # Display file
head -n 5 file           # First 5 lines
tail -n 5 file           # Last 5 lines
grep pattern file        # Search for pattern
sed 's/old/new/g' file   # Find and replace
awk '{print $1}' file    # Print first column
wc -l file               # Count lines
```

## Process Management
```bash
command &               # Run in background
jobs                    # List background jobs
fg %1                   # Bring job 1 to foreground
kill %1                 # Kill job 1
kill 1234               # Kill process with PID 1234
ps                      # List processes
top                     # Interactive process viewer
```

## Exit Codes
```bash
exit 0      # Success
exit 1      # General error
exit 2      # Misuse of shell builtins
exit 126    # Command cannot execute
exit 127    # Command not found
exit 128+n  # Fatal error signal "n"
```

## Here Documents
```bash
cat <<EOF
This is a multi-line
text block that will be
passed to the cat command
EOF

# With variable substitution disabled
cat <<'EOF'
$variable will not be expanded here
EOF
```

## Debugging
```bash
#!/bin/bash -x           # Debug mode (trace execution)
set -x                   # Turn on debugging
set +x                   # Turn off debugging
set -e                   # Exit on error
set -o pipefail          # Fail pipeline if any command fails
trap 'cleanup' EXIT      # Run cleanup on exit
```

## Best Practices
1. Always quote variables: `"$var"`
2. Use `[[ ]]` instead of `[ ]` for tests (more features)
3. Prefer `$(command)` over backticks for command substitution
4. Use `#!/usr/bin/env bash` for better portability
5. Check for required commands at script start
6. Validate user input
7. Include help/usage information
8. Use meaningful exit codes
