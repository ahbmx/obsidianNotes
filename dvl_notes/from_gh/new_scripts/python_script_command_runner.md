Great idea — sanitizing filenames using a **regex** is more reliable than simple string replacements. Here's how you can update the script to:

* Use a **regular expression** to remove or replace any characters that are unsafe or invalid for filenames (like `\/:*?"<>|`).
* Keep the filename readable while making it filesystem-safe.

---

### ✅ Full Script with Regex-Sanitized Filenames

```python
import os
import re
import subprocess
from datetime import datetime

# Define the output directory
OUTPUT_DIR = "output"
os.makedirs(OUTPUT_DIR, exist_ok=True)

# List of commands to run
commands = [
    "echo Hello, World!",
    "ls -l",
    "uname -a",
    "ls /nonexistent_folder",  # Will cause an error
    "cat /etc/passwd | grep root"
]

def sanitize_filename(name):
    # Replace all non-alphanumeric and safe characters with underscore
    return re.sub(r'[^A-Za-z0-9._-]', '_', name)

def run_command(command, output_dir):
    # Generate a safe filename with a timestamp
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    safe_command_name = sanitize_filename(command.strip())[:50]  # Limit filename length
    base_filename = f"{safe_command_name}_{timestamp}"
    output_file = os.path.join(output_dir, base_filename + "_output.txt")

    print(f"Running: {command}")
    try:
        result = subprocess.run(
            command,
            shell=True,
            stdout=subprocess.PIPE,
            stderr=subprocess.STDOUT,  # Combine stderr into stdout
            text=True
        )

        with open(output_file, 'w') as f_out:
            f_out.write(result.stdout)

        print(f"Combined output saved to {output_file}")

    except Exception as e:
        print(f"Failed to run command '{command}': {e}")

# Run all commands
for cmd in commands:
    run_command(cmd, OUTPUT_DIR)
```

---

### 🧼 Example:

For a command like:

```bash
cat /etc/passwd | grep root
```

The output filename will be something like:

```
cat_etc_passwd___grep_root_20250604_153012_output.txt
```

---

### ✅ Why This is Safe:

* The regex `[^A-Za-z0-9._-]` replaces anything **not** a letter, digit, dot, underscore, or hyphen.
* This keeps filenames valid on Windows, Linux, and macOS.

Let me know if you want to include more characters or hash long commands instead of trimming!
