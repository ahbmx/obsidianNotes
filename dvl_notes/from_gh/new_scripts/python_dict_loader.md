If your JSON configuration file contains nested dictionaries (a dict of dicts), you'll want to handle this structure properly when exposing the configuration variables. Here's how to modify the function to handle nested dictionaries:

## Updated Solution for Nested Dictionaries

```python
import json
import sys
from types import ModuleType

class ConfigModule(ModuleType):
    """A module-like object that stores configuration variables, including nested ones"""
    def __repr__(self):
        return f"<ConfigModule: {', '.join(self.__dict__.keys())}>"

def _create_nested_modules(base_module, name, data):
    """Recursively create modules for nested dictionaries"""
    if isinstance(data, dict):
        nested_module = ConfigModule(name)
        for key, value in data.items():
            setattr(nested_module, key, _create_nested_modules(nested_module, key, value))
        return nested_module
    else:
        return data

def load_config(config_path):
    """
    Load JSON configuration file with nested dictionaries and expose variables globally.
    
    Args:
        config_path (str): Path to the JSON configuration file
        
    Returns:
        ConfigModule: The configuration module containing all variables
    """
    try:
        # Load the JSON file
        with open(config_path, 'r') as f:
            config_data = json.load(f)
        
        # Create the main config module
        config_module = ConfigModule('config')
        
        # Process all items, handling nested dictionaries
        for key, value in config_data.items():
            setattr(config_module, key, _create_nested_modules(config_module, key, value))
        
        # Add the config module to sys.modules so it can be imported
        sys.modules['config'] = config_module
        
        return config_module
        
    except FileNotFoundError:
        raise FileNotFoundError(f"Configuration file not found at {config_path}")
    except json.JSONDecodeError:
        raise ValueError(f"Invalid JSON in configuration file at {config_path}")
```

## Example Usage with Nested Config

1. **Sample JSON config file (`config.json`)**:
```json
{
    "database": {
        "url": "postgres://user:pass@localhost:5432/db",
        "timeout": 30,
        "ssl": {
            "enabled": true,
            "cert_path": "/path/to/cert"
        }
    },
    "api": {
        "key": "your-api-key",
        "endpoints": {
            "users": "/api/v1/users",
            "products": "/api/v1/products"
        }
    },
    "debug": true
}
```

2. **Accessing nested values**:
```python
# Load the config
config = load_config('config.json')

# Access nested values
print(config.database.url)  # "postgres://user:pass@localhost:5432/db"
print(config.database.ssl.enabled)  # True
print(config.api.endpoints.users)  # "/api/v1/users"

# You can also import specific nested values
from config import database
print(database.timeout)  # 30

from config.api.endpoints import products
print(products)  # "/api/v1/products"
```

## Alternative Approach (Attribute Dictionary)

If you prefer a simpler approach without creating nested modules, you can use an attribute dictionary that allows dot notation access:

```python
class AttrDict(dict):
    """Dictionary that allows attribute access"""
    def __getattr__(self, key):
        try:
            val = self[key]
            return AttrDict(val) if isinstance(val, dict) else val
        except KeyError:
            raise AttributeError(key)
    
    def __setattr__(self, key, value):
        self[key] = value

def load_config_simple(config_path):
    """Simpler loader that uses AttrDict for nested access"""
    with open(config_path, 'r') as f:
        config_data = json.load(f, object_hook=lambda d: AttrDict(d))
    
    sys.modules['config'] = config_data
    return config_data

# Usage:
# config = load_config_simple('config.json')
# print(config.database.ssl.enabled)
```

Choose the approach that best fits your needs - the first one creates a more module-like structure while the second is simpler but may be less explicit about what's available in the configuration.
