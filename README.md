# 🔐 SSH Access Project — Oracle Cloud VM

This project demonstrates a complete setup of secure SSH access to a cloud virtual machine using multiple SSH key pairs.  
All steps were performed manually as part of the [roadmap.sh DevOps Fundamentals](https://roadmap.sh/devops) track.

---

## 📌 Project Overview

The goal of this project was to:

- Generate and manage multiple SSH key pairs  
- Deploy a virtual machine in Oracle Cloud Infrastructure (OCI)  
- Configure networking components (VCN, subnet, routing, security lists)  
- Enable public SSH access (port 22)  
- Add multiple authorized keys to the server  
- Configure the local SSH client for simplified access  
- Verify connectivity using multiple SSH identities  

✅ **Result:** The VM is accessible using two independent SSH keys.

---

## ⚙️ Prerequisites

Before starting, the following were required:

- Oracle Cloud Infrastructure (OCI) Free Tier account  
- Local Linux machine with:
  - `ssh`
  - `ssh-keygen`
  - `nano` (or any text editor)  
- Basic terminal knowledge  

---

## 🔑 Generate SSH Keys

### 1. Create the first SSH key pair

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519
````

### 2. Create the second SSH key pair

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_second
```

> ⚠️ Both keys were generated without a passphrase for simplicity (not recommended for production).

---

## ☁️ Create and Configure Oracle Cloud VM

### VM Configuration

| Parameter      | Value                       |
| -------------- | --------------------------- |
| Cloud Provider | Oracle Cloud Infrastructure |
| Shape          | VM.Standard.E2.1.Micro      |
| OS             | Ubuntu 22.04 Minimal        |
| Region         | Sweden Central (Stockholm)  |
| Public IP      | Assigned automatically      |

### SSH Key Setup

During VM creation, the content of:

```
~/.ssh/id_ed25519.pub
```

was added to the instance.

---

## 🌐 Networking Configuration

### Virtual Cloud Network (VCN)

* CIDR: `10.0.0.0/16`

### Public Subnet

* Name: `ilgar_subnet`
* CIDR: `10.0.0.0/24`
* Public IP: Enabled

### Internet Gateway

Attached to allow external connectivity.

### Route Table

```
0.0.0.0/0 → Internet Gateway
```

### Security List (Firewall)

| Source    | Protocol | Port | Description |
| --------- | -------- | ---- | ----------- |
| 0.0.0.0/0 | TCP      | 22   | Allow SSH   |

---

## 🖥️ Add Second SSH Key to Server

### Connect using the first key

```bash
ssh -i ~/.ssh/id_ed25519 ubuntu@<PUBLIC_IP>
```

### Add the second key

```bash
nano ~/.ssh/authorized_keys
```

Append:

```
~/.ssh/id_ed25519_second.pub
```

---

## 🧩 Configure Local SSH Client

Edit SSH config:

```bash
nano ~/.ssh/config
```

Add:

```
Host ilgarvm
    HostName <PUBLIC_IP>
    User ubuntu
    IdentityFile ~/.ssh/id_ed25519

Host ilgarvm-second
    HostName <PUBLIC_IP>
    User ubuntu
    IdentityFile ~/.ssh/id_ed25519_second
```

### Connect easily:

```bash
ssh ilgarvm
ssh ilgarvm-second
```

---

## ✅ Test Connections

Both SSH identities were successfully tested:

```bash
ssh ilgarvm
```

```bash
ssh ilgarvm-second
```

✔️ Both commands connect to the same VM using different keys.

---

## 🎯 Conclusion

This project demonstrates the full lifecycle of secure SSH access setup:

* SSH key generation
* Cloud VM provisioning
* Network configuration
* Firewall rules setup
* Multi-key authentication
* SSH client configuration

💡 The setup follows fundamental DevOps practices for secure remote access.

---
