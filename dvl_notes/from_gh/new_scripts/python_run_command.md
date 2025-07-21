# Python Function to Run Commands, Capture Output to File, and Return Output

Here's an enhanced version that:
1. Captures both stdout and stderr to a file
2. Also returns the output as a string for immediate parsing
3. Doesn't use typing annotations

```python
import subprocess

def run_command_capture_output(
    command,
    output_file=None,
    append=False,
    timeout=None,
    shell=False
):
    """
    Run a command, capture output to file (optional), and return output.
    
    Args:
        command: Command to run (string or list)
        output_file: If provided, write output to this file
        append: Append to file if True, else overwrite (default: False)
        timeout: Timeout in seconds (default: None)
        shell: Run through shell if True (default: False)
    
    Returns:
        tuple: (return_code, output_string)
    
    Example:
        retcode, output = run_command_capture_output(["ls", "-l"])
        retcode, output = run_command_capture_output("ls -l", "output.txt")
    """
    # Determine file mode
    file_mode = 'a' if append else 'w' if output_file else None
    
    # Run the command and capture output
    process = subprocess.Popen(
        command,
        stdout=subprocess.PIPE,
        stderr=subprocess.STDOUT,  # Merge stderr with stdout
        shell=shell,
        universal_newlines=True
    )
    
    try:
        stdout_data, _ = process.communicate(timeout=timeout)
    except subprocess.TimeoutExpired:
        process.kill()
        stdout_data, _ = process.communicate()
        raise
    
    # Write to file if requested
    if output_file and file_mode:
        with open(output_file, file_mode) as f:
            f.write(stdout_data)
    
    return process.returncode, stdout_data
```

## Key Features:

1. **Returns both return code and output** - You get a tuple `(return_code, output_string)`
2. **File output is optional** - Only writes to file if `output_file` is specified
3. **Still captures both streams** - stderr is merged with stdout
4. **Maintains all previous functionality** - timeout, append mode, etc.

## Usage Examples:

```python
# Example 1: Just capture output (no file)
retcode, output = run_command_capture_output(["ls", "-l"])
print(f"Return code: {retcode}")
print(f"Output:\n{output}")

# Example 2: Save to file and get output
retcode, output = run_command_capture_output("df -h", "disk_usage.txt")
if retcode == 0:
    parse_output(output)

# Example 3: Append to existing file
retcode, output = run_command_capture_output(
    "echo 'new data'",
    "log.txt",
    append=True
)

# Example 4: With timeout
try:
    retcode, output = run_command_capture_output(
        "long_running_process",
        timeout=30
    )
except subprocess.TimeoutExpired:
    print("Command timed out")
```

This version gives you the best of both worlds - persistent storage in a file when needed, plus immediate access to the output for parsing in your script.
