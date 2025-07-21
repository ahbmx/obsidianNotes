# Python Logging Function with String Level and Output Options

Here's a comprehensive logging function that allows you to:
- Set the log level using a string (e.g., "DEBUG", "INFO", "WARNING", etc.)
- Choose to log to console, file, or both
- Configure log format and date format

```python
import logging
import sys
from typing import Optional

def setup_logger(
    name: str = "my_logger",
    log_level: str = "INFO",
    log_file: Optional[str] = None,
    console_log: bool = True,
    file_log: bool = False,
    log_format: str = "%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    date_format: str = "%Y-%m-%d %H:%M:%S"
) -> logging.Logger:
    """
    Setup and configure a logger with flexible options.
    
    Args:
        name: Name of the logger (default: "my_logger")
        log_level: Logging level as string (DEBUG, INFO, WARNING, ERROR, CRITICAL)
        log_file: Path to log file (required if file_log is True)
        console_log: Enable logging to console (default: True)
        file_log: Enable logging to file (default: False)
        log_format: Format string for log messages
        date_format: Format string for date/time in logs
        
    Returns:
        Configured logger instance
        
    Raises:
        ValueError: If invalid log_level is provided or file_log is True without log_file
    """
    # Convert string log level to logging level constant
    level = getattr(logging, log_level.upper(), None)
    if not isinstance(level, int):
        raise ValueError(f"Invalid log level: {log_level}")
    
    if file_log and not log_file:
        raise ValueError("log_file must be specified when file_log is True")
    
    # Create logger
    logger = logging.getLogger(name)
    logger.setLevel(level)
    
    # Clear any existing handlers to avoid duplicate logs
    logger.handlers.clear()
    
    # Create formatter
    formatter = logging.Formatter(log_format, datefmt=date_format)
    
    # Add console handler if enabled
    if console_log:
        console_handler = logging.StreamHandler(sys.stdout)
        console_handler.setLevel(level)
        console_handler.setFormatter(formatter)
        logger.addHandler(console_handler)
    
    # Add file handler if enabled
    if file_log and log_file:
        file_handler = logging.FileHandler(log_file)
        file_handler.setLevel(level)
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)
    
    return logger


# Example usage
if __name__ == "__main__":
    # Example 1: Console logging only
    logger1 = setup_logger("console_logger", log_level="DEBUG")
    logger1.debug("This is a debug message")
    logger1.info("This is an info message")
    logger1.warning("This is a warning message")
    
    # Example 2: File logging only
    logger2 = setup_logger("file_logger", log_level="INFO", 
                          log_file="app.log", console_log=False, file_log=True)
    logger2.info("This message goes to the log file only")
    
    # Example 3: Both console and file logging
    logger3 = setup_logger("combined_logger", log_level="WARNING",
                          log_file="combined.log", console_log=True, file_log=True)
    logger3.error("This message goes to both console and file")
```

## Features:

1. **Log Level by String**: You can specify the log level as a string ("DEBUG", "INFO", etc.)
2. **Flexible Output**:
   - Console logging (enabled by default)
   - File logging (when `file_log=True` and `log_file` is specified)
   - Both simultaneously
3. **Custom Formatting**: You can customize both the log message format and date format
4. **Prevents Duplicate Logs**: Clears existing handlers before adding new ones
5. **Type Hints**: Uses Python type hints for better code clarity
6. **Error Handling**: Validates log level and file path requirements

## Example Usage Patterns:

```python
# Minimal setup - console logging at INFO level
logger = setup_logger()

# Debug level logging to console only
logger = setup_logger(log_level="DEBUG")

# Info level logging to file only
logger = setup_logger(log_file="app.log", console_log=False, file_log=True)

# Error level logging to both console and file
logger = setup_logger(log_level="ERROR", log_file="errors.log", file_log=True)
```

This implementation provides a clean, flexible way to configure logging in your Python applications with minimal boilerplate code.
