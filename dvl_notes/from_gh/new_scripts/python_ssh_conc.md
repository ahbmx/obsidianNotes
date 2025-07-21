# Python Function for Parallel SSH Command Execution with Paramiko

Here's a Python function that uses Paramiko to connect to multiple systems, run commands in parallel using ThreadPoolExecutor, and store the output to files. The credentials are stored in a JSON file, and the commands/filenames are specified in a dictionary.

```python
import paramiko
import json
from concurrent.futures import ThreadPoolExecutor
import os
from typing import Dict, List

def ssh_connect_and_execute(host: str, username: str, password: str, 
                           key_filename: str = None, port: int = 22,
                           commands_dict: Dict[str, str] = None,
                           output_dir: str = 'output'):
    """
    Connect to a host via SSH and execute multiple commands, saving output to files.
    
    Args:
        host: Hostname or IP address
        username: SSH username
        password: SSH password
        key_filename: Path to SSH private key (optional)
        port: SSH port (default: 22)
        commands_dict: Dictionary of {command: output_filename}
        output_dir: Directory to store output files
    """
    try:
        # Create SSH client
        client = paramiko.SSHClient()
        client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        
        # Connect using either password or key
        if key_filename:
            client.connect(hostname=host, port=port, username=username, 
                          key_filename=key_filename)
        else:
            client.connect(hostname=host, port=port, username=username, 
                          password=password)
        
        # Create output directory if it doesn't exist
        os.makedirs(output_dir, exist_ok=True)
        
        # Execute each command and save output
        for command, filename in commands_dict.items():
            stdin, stdout, stderr = client.exec_command(command)
            output = stdout.read().decode('utf-8')
            error = stderr.read().decode('utf-8')
            
            # Combine stdout and stderr
            full_output = f"Command: {command}\n\nOutput:\n{output}"
            if error:
                full_output += f"\n\nError:\n{error}"
            
            # Write to file
            filepath = os.path.join(output_dir, f"{host}_{filename}")
            with open(filepath, 'w') as f:
                f.write(full_output)
        
        client.close()
        return True, f"Successfully executed commands on {host}"
    
    except Exception as e:
        return False, f"Error on {host}: {str(e)}"

def run_parallel_ssh_commands(credentials_file: str, commands_dict: Dict[str, str],
                             max_workers: int = 5, output_dir: str = 'output'):
    """
    Run SSH commands on multiple hosts in parallel.
    
    Args:
        credentials_file: Path to JSON file containing SSH credentials
        commands_dict: Dictionary of {command: output_filename}
        max_workers: Maximum number of parallel connections
        output_dir: Directory to store output files
    """
    # Load credentials from JSON file
    try:
        with open(credentials_file) as f:
            credentials = json.load(f)
    except Exception as e:
        print(f"Error loading credentials file: {str(e)}")
        return
    
    # Validate credentials
    if not isinstance(credentials, list):
        print("Credentials file should contain a list of host configurations")
        return
    
    # Process each host in parallel
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        futures = []
        for host_info in credentials:
            # Get host-specific parameters with defaults
            host = host_info.get('host')
            username = host_info.get('username')
            password = host_info.get('password')
            key_filename = host_info.get('key_filename')
            port = host_info.get('port', 22)
            
            if not all([host, username]):
                print(f"Skipping invalid host entry: {host_info}")
                continue
            
            # Submit task to thread pool
            future = executor.submit(
                ssh_connect_and_execute,
                host=host,
                username=username,
                password=password,
                key_filename=key_filename,
                port=port,
                commands_dict=commands_dict,
                output_dir=output_dir
            )
            futures.append((host, future))
        
        # Process results as they complete
        for host, future in futures:
            success, message = future.result()
            status = "SUCCESS" if success else "FAILED"
            print(f"{status}: {message}")

# Example usage
if __name__ == "__main__":
    # Example commands dictionary (command: output_filename)
    commands = {
        "uname -a": "system_info.txt",
        "df -h": "disk_usage.txt",
        "free -m": "memory_usage.txt",
        "ps aux --sort=-%cpu | head -n 5": "top_processes.txt"
    }
    
    # Run the parallel SSH commands
    run_parallel_ssh_commands(
        credentials_file='ssh_credentials.json',
        commands_dict=commands,
        max_workers=4,
        output_dir='command_outputs'
    )
```

## Required JSON Credentials File (ssh_credentials.json)

```json
[
    {
        "host": "server1.example.com",
        "username": "admin",
        "password": "secretpassword",
        "port": 22
    },
    {
        "host": "server2.example.com",
        "username": "root",
        "key_filename": "/path/to/private_key"
    },
    {
        "host": "192.168.1.100",
        "username": "user",
        "password": "anotherpassword",
        "port": 2222
    }
]
```

## Features:

1. **Parallel Execution**: Uses ThreadPoolExecutor to run SSH commands on multiple hosts simultaneously
2. **Flexible Authentication**: Supports both password and key-based authentication
3. **JSON Configuration**: Stores credentials in an external JSON file
4. **Command Dictionary**: Accepts a dictionary mapping commands to output filenames
5. **Error Handling**: Captures and reports both command errors and connection failures
6. **Output Organization**: Creates a dedicated output directory with host-specific files

## Usage Instructions:

1. Install required packages: `pip install paramiko`
2. Create a JSON file with your SSH credentials (see example above)
3. Define your commands dictionary with the commands you want to run and their output filenames
4. Call `run_parallel_ssh_commands()` with your credentials file and commands dictionary
5. Check the output directory for the results

The script will create files named like `[hostname]_[filename]` in the specified output directory.
