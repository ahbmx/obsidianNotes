# Encrypting and Decrypting JSON Files in Python

You can encrypt and decrypt JSON files in Python using cryptographic libraries like `cryptography` or `pycryptodome`. Here's a step-by-step guide using the `cryptography` library:

## Installation
First, install the required package:
```bash
pip install cryptography
```

## Encryption and Decryption Example

```python
from cryptography.fernet import Fernet
import json

# Generate a key (keep this secret!)
# key = Fernet.generate_key()  # Uncomment to generate a new key
# print("Your secret key:", key.decode())  # Save this key securely

# For this example, we'll use a predefined key (in practice, use the above to generate)
# Replace this with your actual key or use the generation method above
key = b'your-secret-key-here'  # This should be 32 url-safe base64-encoded bytes

def encrypt_json(data, key):
    """
    Encrypt JSON data using Fernet symmetric encryption
    """
    f = Fernet(key)
    json_str = json.dumps(data)
    encrypted_data = f.encrypt(json_str.encode())
    return encrypted_data

def decrypt_json(encrypted_data, key):
    """
    Decrypt JSON data using Fernet symmetric encryption
    """
    f = Fernet(key)
    decrypted_data = f.decrypt(encrypted_data)
    return json.loads(decrypted_data.decode())

def save_encrypted_file(data, filename, key):
    """
    Encrypt data and save to file
    """
    encrypted_data = encrypt_json(data, key)
    with open(filename, 'wb') as f:
        f.write(encrypted_data)

def load_encrypted_file(filename, key):
    """
    Load and decrypt data from file
    """
    with open(filename, 'rb') as f:
        encrypted_data = f.read()
    return decrypt_json(encrypted_data, key)

# Example usage
if __name__ == "__main__":
    # Sample JSON data
    data = {
        "name": "John Doe",
        "age": 30,
        "email": "john@example.com"
    }
    
    # Encrypt and save to file
    save_encrypted_file(data, 'encrypted_data.bin', key)
    print("Data encrypted and saved to 'encrypted_data.bin'")
    
    # Load and decrypt from file
    decrypted_data = load_encrypted_file('encrypted_data.bin', key)
    print("Decrypted data:", decrypted_data)
```

## Important Security Notes

1. **Key Management**: The encryption key must be kept secret. Store it securely (not in your code).
2. **Key Generation**: Use `Fernet.generate_key()` to create a strong key.
3. **Password-based Encryption**: If you need to use a password instead of a key, you'll need to derive a key from the password using a KDF (Key Derivation Function).

## Alternative: Using PyCryptodome

If you prefer `pycryptodome`, here's a brief example:

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
import json, base64

def encrypt_json(data, key):
    cipher = AES.new(key, AES.MODE_EAX)
    json_str = json.dumps(data)
    ciphertext, tag = cipher.encrypt_and_digest(json_str.encode())
    return base64.b64encode(cipher.nonce + tag + ciphertext)

def decrypt_json(encrypted_data, key):
    encrypted_data = base64.b64decode(encrypted_data)
    nonce = encrypted_data[:16]
    tag = encrypted_data[16:32]
    ciphertext = encrypted_data[32:]
    cipher = AES.new(key, AES.MODE_EAX, nonce=nonce)
    return json.loads(cipher.decrypt_and_verify(ciphertext, tag).decode())

# Generate a key (AES-256 requires 32 bytes)
# key = get_random_bytes(32)
```

Remember to handle your encryption keys securely and never commit them to version control!
