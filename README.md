
# 🔐 Linux PAM Password Policy Configuration Guide

## 📌 Table of Contents
1. [Creating a New VM](#1-creating-a-new-vm)
2. [IP Address Configuration](#2-ip-address-configuration)
3. [PAM Configuration](#3-pam-configuration)
4. [Verification](#4-verification)

---

## 1. Creating a New VM 🖥️

Clone the existing `myserver01` to create a new VM called `myserver03`.

---

## 2. IP Address Configuration 🌐

Change the IP address of the newly created VM:
- **Old IP address**: `10.0.2.15`
- **New IP address**: `10.0.2.21`

### 2.1 Editing Netplan Configuration

```bash
sudo vi /etc/netplan/00-installer-config.yaml
```

### 2.2 Port Configuration

Configure the ports for `myserver03`:

### 2.3 IP Configuration Completed

---

## 3. PAM Configuration 🛠️

### 3.1 Introduction to PAM

**PAM (Pluggable Authentication Module)** is a modular system that allows for flexible authentication methods for users and access control to services. It enables administrators to choose the authentication methods for applications and allows changes without recompilation.

- Key locations for PAM configuration:
  - Configuration files: `/etc/pam.d` or `/etc/pam.conf`
  - Module files: `/lib/security` or `/usr/lib/security`

### 3.2 Installing PAM

```bash
sudo apt install libpam0g-dev
sudo apt install libpam-pwquality
```

### 3.3 Configuring `pwquality.conf`

Open and modify the `/etc/security/pwquality.conf` file to add the following settings:

```bash
minlen = 8
dcredit = -1
ucredit = -1
lcredit = -1
ocredit = 0
```

#### Meaning of each setting:
- **`minlen = 8`**: Sets the minimum password length to 8 characters.
- **`dcredit = -1`**: Requires at least 1 digit in the password.
- **`ucredit = -1`**: Requires at least 1 uppercase letter in the password.
- **`lcredit = -1`**: Requires at least 1 lowercase letter in the password.
- **`ocredit = 0`**: Special characters are not mandatory.

### 3.4 Configuring `common-password` File

Edit the `/etc/pam.d/common-password` file and add the following:

```bash
password requisite pam_pwquality.so retry=3 minlen=8
```

#### Explanation of each field:
1. **`password`**: Manages the password-related authentication process.
2. **`requisite`**: If this module fails, PAM immediately stops and denies access.
3. **`pam_pwquality.so`**: This module checks password quality (length, complexity, etc.).
4. **`retry=3`**: Allows up to 3 attempts for password entry.
5. **`minlen=8`**: Requires a minimum password length of 8 characters.

---

## 4. Verification ✅

Test setting a password. If the password is less than 8 characters, the system will reject it.

---

🎉 By following these steps, you have now strengthened your system security by configuring password policies using PAM!
