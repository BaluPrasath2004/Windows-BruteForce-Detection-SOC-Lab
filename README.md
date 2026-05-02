# Windows Brute-Force Detection SOC Lab

---

## Overview

This project demonstrates a simulated SMB brute-force attack against a Windows system and how it can be detected using Windows Security Event Logs.
The lab follows a complete workflow from connectivity validation to attack execution and detection.

---

## Lab Environment

```bash
Attacker Machine : Kali Linux
Target Machine   : Windows (10 / Server)
Network          : VirtualBox Host-Only Network
Target IP        : 192.168.56.101
Protocol         : SMB (Port 445)
```

---

## Tools Used

```bash
Nmap              -> Port Scanning
CrackMapExec      -> SMB Brute-force Simulation
Windows Event Log -> Detection & Analysis
rockyou.txt       -> Password Wordlist
```

---

## Attack & Detection Workflow

### Step 1: Network Connectivity Check

```bash
ping 192.168.56.101
```

Verified that attacker can reach the target.

![Step 1 - Ping](screenshots/step1-ping-connectivity.png)

---

### Step 2: IP Configuration Verification

```bash
ipconfig     # Windows
ip a         # Kali Linux
```

Ensured both machines are in the same network.

![Step 2 - IP Config](screenshots/step2-ip-configuration.png)

---

### Step 3: Port Scanning

```bash
nmap -p 445 192.168.56.101
```

Result:

```bash
445/tcp open microsoft-ds
```

![Step 3 - Nmap](screenshots/step3-port-scan-nmap.png)

---

### Step 4: SMB Brute-Force Attack

```bash
crackmapexec smb 192.168.56.101 -u vboxuser -p /usr/share/wordlists/rockyou.txt
```

Observed:

```bash
STATUS_LOGON_FAILURE
STATUS_ACCOUNT_LOCKED_OUT
```

![Step 4 - Attack](screenshots/step4-smb-bruteforce-attack.png)

---

### Step 5: Event Log Detection

Monitored Windows Security Logs during the attack.

```bash
Event ID 4625 -> Failed Logon
Event ID 4740 -> Account Lockout
```

![Step 5 - Logs](screenshots/step5-eventlog-detection-overview.png)

---

### Step 6: Event ID 4625 Analysis

```bash
Logon Type : 3 (Network Logon)
Reason     : Bad Password
Status     : 0xC000006D
```

Indicates repeated brute-force attempts.

![Step 6 - 4625](screenshots/step6-4625-failed-logon-details.png)

---

### Step 7: Event ID 4740 Analysis

```bash
Account Lockout Triggered
```

Confirms security policy enforcement.

![Step 7 - 4740](screenshots/step7-4740-account-lockout.png)

---

### Step 8: Impact Verification

```bash
Account locked - user unable to login
```

![Step 8 - Lockout](screenshots/step8-account-lockout-proof.png)

---

## Key Findings

```bash
- SMB (Port 445) is exposed and can be targeted
- Multiple failed logins generate Event ID 4625
- Repeated failures trigger Event ID 4740
- Logon Type 3 confirms network-based attack
- Windows logs provide strong detection capability
```

---

## Detection Logic (SOC Perspective)

```bash
IF multiple Event ID 4625 detected
AND Event ID 4740 occurs
THEN possible brute-force attack
```

---

## Conclusion

This lab demonstrates how brute-force attacks can be executed and detected using native Windows logging.
It highlights how SOC analysts identify attack patterns through event correlation and log analysis.

---

## Author

Balu Prasath R.
