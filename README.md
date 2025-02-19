# cyber_project
import hashlib
import json
import os
from cryptography.fernet import Fernet

PASSWORD_FILE = "passwords.json"
KEY_FILE = "key.key"

# Generate a key if it doesn't exist
def generate_key():
    if not os.path.exists(KEY_FILE):
        key = Fernet.generate_key()
        with open(KEY_FILE, "wb") as key_file:
            key_file.write(key)

# Load the encryption key
def load_key():
    if not os.path.exists(KEY_FILE):
        generate_key()
    with open(KEY_FILE, "rb") as key_file:
        return key_file.read()

# Encrypt the password
def encrypt_password(password, key):
    cipher = Fernet(key)
    return cipher.encrypt(password.encode()).decode()

# Decrypt the password
def decrypt_password(encrypted_password, key):
    cipher = Fernet(key)
    return cipher.decrypt(encrypted_password.encode()).decode()

# Load passwords from the JSON file
def load_passwords():
    if os.path.exists(PASSWORD_FILE):
        with open(PASSWORD_FILE, "r") as file:
            return json.load(file)
    return {}

# Save passwords to the JSON file
def save_passwords(passwords):
    with open(PASSWORD_FILE, "w") as file:
        json.dump(passwords, file, indent=4)

# Function to add a new password
def add_password(account, password):
    key = load_key()
    passwords = load_passwords()
    passwords[account] = encrypt_password(password, key)
    save_passwords(passwords)
    print("Password saved securely!")

# Function to retrieve a password
def retrieve_password(account):
    key = load_key()
    passwords = load_passwords()
    if account in passwords:
        decrypted_password = decrypt_password(passwords[account], key)
        print(f"Stored password for {account}: {decrypted_password}")
    else:
        print("Account not found!")

# Main function
def main():
    while True:
        print("\nSecure Password Manager")
        print("1. Add Password")
        print("2. Retrieve Password")
        print("3. Exit")
        
        choice = input("Enter your choice: ")
        
        if choice == "1":
            account = input("Enter account name: ")
            password = input("Enter password: ")
            add_password(account, password)
        elif choice == "2":
            account = input("Enter account name: ")
            retrieve_password(account)
        elif choice == "3":
            break
        else:
            print("Invalid choice! Try again.")

# Run the main function
if __name__ == "__main__":
    main()
