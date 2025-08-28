# Secure Remote Administration Protocols (SSH)

This repository provides notes and references on **Secure Remote Administration Protocols**, focusing on **Secure Shell (SSH)**.  
Click the links below to jump to specific parts of the video.

---

## Secure Remote Administration Protocols

When it comes to **Secure Remote Administration Protocols**, **Secure Shell (SSH)** is the de facto standard.

### 1. Connecting to a Remote System
We start with a basic SSH connection from an **Ubuntu system** to a remote **Parrot OS system**:

```bash
ssh user@192.168.1.109
```

On first connection, you’ll see a fingerprint prompt:

```
The authenticity of host '192.168.1.109' can't be established.
ECDSA key fingerprint is SHA256:xxxx.
Are you sure you want to continue connecting (yes/no)?
```

- Type `yes` to continue.
- Enter the password when prompted.

At this point, you are connected to the remote system:
```
Parrot Sec
```

---

### 2. Issues with Password Authentication
Although SSH encrypts the connection by default, **using passwords over the network is not recommended**:
- Passwords can be intercepted.
- Trust relationships are more secure.

---

### 3. Setting Up SSH Key-Based Authentication

Generate a key pair on your local Ubuntu system:

```bash 
ssh-keygen
```

- Press **Enter** to accept the default file location.
- Optionally, set a passphrase (or leave it empty for no passphrase).

View the public key:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the key and add it to the `authorized_keys` file on the remote system:

```bash
nano ~/.ssh/authorized_keys
```

Paste the public key inside, then save and exit.

---

### 4. Logging in Without a Password
Now, when you connect again:

```bash
ssh user@192.168.1.109
```

You’ll be logged in **without entering a password**, thanks to the trust relationship created with SSH keys.

---

## ✅ Key Takeaways
- **Passwords are risky** → prefer **key-based authentication**.  
- **SSH encrypts communication**, but trust relationships (public/private keys) are safer.  
- Properly configured, SSH lets you **administer systems remotely without exposing sensitive credentials**.

---

## 📌 Next Steps
- Try setting up SSH keys between multiple systems.
- Explore using `ssh-copy-id` for easier public key distribution.
- Learn about hardening SSH (disable root login, restrict ports, use fail2ban, etc.).
