# Nmap Network Reconnaissance

A structured network reconnaissance project performed against an intentionally vulnerable lab target using **Nmap**, covering host discovery, port scanning, service/version detection, OS fingerprinting, NSE vulnerability scripting, and firewall detection.

## 🎯 Objective

To demonstrate a complete, methodical network scanning workflow — the same approach used in real-world penetration testing and vulnerability assessment engagements — and document findings in a professional report format.

## 🧪 Lab Environment

| Component | Details |
|---|---|
| Attacker Machine | Windows 10/11, Nmap (native install) |
| Target Machine | Metasploitable2 (intentionally vulnerable Linux VM) |
| Virtualization | VirtualBox, Host-only network |
| Target IP | `192.168.203.128` |

> ⚠️ **Disclaimer:** All scans were performed in an isolated lab environment against a machine specifically designed to be vulnerable for educational purposes (Metasploitable2). No unauthorized systems were scanned.

## 🔍 Methodology

The scan followed a 7-stage structured approach:

### 1. Host Discovery
```
nmap -sn 192.168.203.0/24
```
Identified live hosts on the subnet before proceeding to targeted scanning.

### 2. Port Scanning
```
nmap -p- 192.168.203.128 -oN port_scan.txt
```
Full scan across all 65,535 TCP ports. **~20 open ports** identified, including FTP, SSH, Telnet, SMTP, DNS, HTTP, RPC, Samba, MySQL, PostgreSQL, VNC, IRC, and Tomcat.

### 3. Service & Version Detection
```
nmap -sV 192.168.203.128 -oN service_version.txt
```
Fingerprinted exact software and version running on each open port — critical for mapping services to known CVEs.

### 4. OS Detection
```
nmap -O 192.168.203.128 -oN os_detection.txt
```
Fingerprinted the target's operating system based on TCP/IP stack behavior.

### 5. NSE Script Scanning
```
nmap --script vuln 192.168.203.128 -oN nse_vuln_scan.txt
```
Ran the Nmap Scripting Engine's vulnerability detection scripts to actively flag exploitable misconfigurations and known CVEs.

### 6. Firewall Detection
```
nmap -sA 192.168.203.128 -oN firewall_detection.txt
```
ACK scan to distinguish filtered (firewalled) ports from unfiltered ones.

### 7. Consolidated Scan Report
```
nmap -A 192.168.203.128 -oA final_report
```
Combined OS detection, version detection, default scripts, and traceroute into one consolidated evidence file (`.nmap`, `.xml`, `.gnmap`).

## 🚨 Key Findings

| Vulnerability | Port/Service | Severity | Description |
|---|---|---|---|
| vsFTPd 2.3.4 Backdoor (CVE-2011-2523) | 21/ftp | **Critical** | Confirmed exploitable backdoor — Nmap's NSE script obtained a root shell (`uid=0(root)`) |
| UnrealIRCd Trojaned Backdoor | 6667/irc | **Critical** | Known backdoored build allowing remote code execution |
| RMI Registry RCE | 1099/java-rmi | **High** | Default config allows loading classes from remote URLs → RCE |
| SSL POODLE (CVE-2014-3566) | 25, 5432 | **High** | Padding-oracle MITM attack on SSLv3 |
| CCS Injection (CVE-2014-0224) | 5432 | **High** | MITM session hijack via malformed TLS handshake |
| Weak Diffie-Hellman Groups | 25, 5432 | **Medium** | 1024-bit DH groups vulnerable to passive eavesdropping |
| Slowloris DoS (CVE-2007-6750) | 80, 8180 | **Medium** | Resource exhaustion via slow partial HTTP requests |
| CSRF Vulnerabilities | 80, 8180 | **Medium** | Multiple unprotected forms discovered via spidering (TWiki, Mutillidae, Tomcat) |
| Missing HttpOnly Flag | 8180 | **Low-Medium** | Session cookies exposed to potential XSS-based theft |
| Exposed Admin/Manager Paths | 8180 | **Medium** | Tomcat Manager and dozens of guessable `/admin/` paths discoverable |

## ✅ Remediation Recommendations

- Immediately patch or remove vsFTPd 2.3.4 and UnrealIRCd — both contain confirmed backdoors
- Disable legacy/unencrypted services (Telnet, rexec, rlogin, rsh)
- Upgrade SSL/TLS configuration to disable SSLv3 and weak DH cipher suites
- Restrict RMI registry to trusted hosts only, or disable if unused
- Set `HttpOnly` and `Secure` flags on all session cookies
- Remove or password-protect exposed admin/manager interfaces
- Apply rate-limiting or timeout protections against Slowloris-style DoS

## 📂 Repository Structure

```
Nmap-Network-Reconnaissance/
├── README.md
├── outputs/              # Raw scan output files
├── screenshots/          # Terminal screenshots for each stage
└── report/               # Consolidated PDF report (optional)
```

## 🛠️ Tools Used

- **Nmap** — network scanning and reconnaissance
- **Metasploitable2** — intentionally vulnerable target VM
- **VirtualBox** — virtualization/lab environment

## 📌 Key Takeaway

This project demonstrates a full reconnaissance methodology from host discovery through vulnerability identification, reflecting the initial phases of a real-world penetration testing engagement.
