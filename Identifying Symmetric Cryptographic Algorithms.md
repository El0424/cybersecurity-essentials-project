
# 🔐 Symmetric Key Encryption Hands-On Demo

This project is a beginner-friendly, hands-on demonstration of **symmetric key encryption** using command-line tools and mobile apps. The goal is to show how symmetric encryption works, its strengths, and its critical limitation — secure key exchange.

---

## 🎯 Purpose

To provide an educational and practical demonstration of symmetric key encryption using:
- Open-source tools (like `aescrypt`)
- PowerShell & Windows Subsystem for Linux
- A mobile encryption app (Android example)
- Real encryption/decryption commands

---

## 💡 What You'll Learn

- How symmetric encryption works using the same key for encryption and decryption.
- Why symmetric encryption is **fast** and **efficient**.
- Why secure **key exchange** is a major challenge.
- The role symmetric encryption plays in systems like TLS and hybrid encryption schemes.

---

## 🛠️ Tools Used

- Windows Subsystem for Linux (WSL)
- PowerShell
- `aescrypt` command-line tool
- Android encryption app (any file encryptor)

---

## 📁 File Structure

```
/symmetric-demo
│
├── secret_file.txt          # Original plaintext message
├── secret_file.aes          # Encrypted file output
├── decrypt_command.txt      # Sample decryption command
├── encrypt_command.txt      # Sample encryption command
```

---

## 🧪 Demo Walkthrough

### ✅ Encrypting a File (Command Line)

```bash
aescrypt -e -p ToughPassword secret_file.txt
```

- `-e`: Encrypt mode
- `-p`: Password for encryption
- `secret_file.txt`: File to encrypt

This generates a file: `secret_file.txt.aes`

### ✅ Decrypting a File (Command Line)

```bash
aescrypt -d -p ToughPassword secret_file.txt.aes
```

This returns the original `secret_file.txt`.

---

## 📱 Mobile Demo (Optional)

Use a mobile encryption app (e.g., Crypt4All Lite) to:
1. Encrypt a file using a passphrase.
2. Decrypt it and recover the original file.

> Note: Passwords like `Password123` are insecure in real use. Used here for demo only.

---

## ⚠️ Limitation: Key Exchange Problem

> Symmetric key encryption requires that both sender and receiver **know and protect** the same secret key. But how do you share that key securely?

You can't send it via email or text — it can be intercepted. This is where **asymmetric encryption** (public/private keys) comes in to solve this challenge.

---

## 🧠 Summary

| Feature | Symmetric Encryption |
|--------|------------------------|
| 🔐 Key Type | Same key used to encrypt and decrypt |
| ⚡ Speed | Very fast and efficient |
| 📦 Strength | AES is very strong with proper key |
| 🔗 Weakness | Secure key exchange is difficult |
| 🔄 Usage | Commonly used in hybrid encryption (e.g., TLS) |

---

## 🧾 License

This project is licensed under the MIT License.

---

## 🙌 Credits

Demonstration inspired by practical tutorials on symmetric key encryption and security fundamentals.

