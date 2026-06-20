# Ubuntu Server Hardening Guide

**Enterprise Linux Infrastructure Preparation and Hardening**

*Web Server | Reverse Proxy Server | Load Balancer | Container Image Repository | Jump Server*

---

## Table of Contents

1. [Objective](#objective)
2. [Problem Statement](#problem-statement)
3. [Scope of the Project](#scope-of-the-project)
4. [Server Role Definition](#server-role-definition)
5. [Installation Recommendations](#installation-recommendations)
6. [Password Policies](#password-policies)
7. [Initial System Preparation](#initial-system-preparation)
8. [Package Integrity Verification](#package-integrity-verification)
9. [User and Access Management](#user-and-access-management)
10. [SSH Hardening](#ssh-hardening)
11. [Brute Force Protection](#brute-force-protection)
12. [Firewall and Network Security](#firewall-and-network-security)
13. [Filesystem Security](#filesystem-security)
14. [Logging and Monitoring](#logging-and-monitoring)
15. [Persistent Journal Logging](#persistent-journal-logging)
16. [Service and Daemon Management](#service-and-daemon-management)
17. [Kernel and System Hardening](#kernel-and-system-hardening)
18. [AppArmor Security](#apparmor-security)
19. [Time Synchronization](#time-synchronization)
20. [Compliance Validation](#compliance-validation)
21. [Monitoring and Alerting](#monitoring-and-alerting)
22. [CIS Benchmark Validation](#cis-benchmark-validation)
23. [NIST 800-53 Mapping](#nist-800-53-mapping)
24. [ISO 27001 Alignment](#iso-27001-alignment)
25. [Expected Project Outcome](#expected-project-outcome)
26. [Conclusion](#conclusion)

---

## Objective

This document provides a complete step-by-step guide for preparing and hardening Ubuntu Server systems for the following enterprise use cases:

- Web Server
- Reverse Proxy Server
- Load Balancer
- Container Image Repository
- Bastion Host / Jump Server

The goal is to build a secure, stable, maintainable, and production-ready Linux infrastructure using industry best practices.

---

## Problem Statement

Organizations often deploy Ubuntu servers without standardized security configurations, leading to:

- Weak access control
- Unnecessary services exposure
- Poor logging and auditing
- Misconfigured SSH access
- Unpatched vulnerabilities
- Lack of compliance readiness

This project addresses these issues by implementing a secure Ubuntu Server hardening baseline using CIS Benchmarks, NIST 800-53, and ISO 27001 recommendations.

---

## Scope of the Project

The scope of this project includes preparing and hardening Ubuntu Server systems for the following infrastructure roles:

- Web Server
- Reverse Proxy Server
- Load Balancer
- Container Image Repository
- Jump Server / Bastion Host

### The project covers:
- Minimal OS installation
- User and access management
- SSH hardening
- Firewall and network security
- Filesystem security
- Logging and auditing
- Kernel hardening
- Service minimization
- Compliance validation
- Security monitoring

### The project does not include:
- Application-level hardening
- Cloud-native security controls
- Kubernetes hardening
- Advanced SIEM integration
- Enterprise IAM integration

---

## Server Role Definition

Before installation, define the exact role of the server.

| Server Type | Purpose |
|-------------|---------|
| Web Server | Host applications/websites |
| Proxy Server | Reverse proxy and traffic routing |
| Load Balancer | Distribute application traffic |
| Container Registry | Store container images |
| Jump Server | Secure administrative access |

---

## Installation Recommendations

**Use:**
- Ubuntu Server ISO
- Minimal Installation
- Latest LTS Version

**Avoid:**
- Desktop Environment
- GUI packages
- Unnecessary services
- Unused software packages

---

## Password Policies

### Install password quality tools
```bash
sudo apt install libpam-pwquality -y
```

### Configure password policies
```bash
sudo nano /etc/pam.d/common-password
```

Ensure these settings (add if missing):
```
password requisite pam_pwquality.so retry=3 minlen=12 difok=3 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
```

### Password aging policies
```bash
sudo nano /etc/login.defs
```

Set these values:
```
PASS_MAX_DAYS 90
PASS_MIN_DAYS 7
PASS_WARN_AGE 14
```

### Enforce for existing users
```bash
sudo chage --maxdays 90 adminuser
sudo chage --mindays 7 adminuser
sudo chage --warndays 14 adminuser
```

### Check password aging
```bash
sudo chage -l adminuser
```

---

## Initial System Preparation

### Update Package Repository
```bash
sudo apt update
```

### Upgrade Installed Packages
```bash
sudo apt upgrade -y
sudo apt dist-upgrade -y
```

### Remove Unnecessary Packages
```bash
sudo apt autoremove -y
sudo apt autoclean
```

### Regular System Update
```bash
sudo apt update && sudo apt upgrade -y
```

---

## Package Integrity Verification

Install package integrity verification tools:
```bash
sudo apt install debsums -y
sudo debsums -s
```

**Purpose:**
- Detect modified packages
- Verify package integrity
- Improve system trust validation

---

## User and Access Management

### Create dedicated admin user
```bash
sudo adduser adminuser
sudo usermod -aG sudo adminuser
```

### Create a non-privileged user for application
```bash
sudo adduser appuser
```

### Set umask globally
```bash
sudo nano /etc/profile
```
Add:
```
umask 027
```

```bash
sudo nano /etc/login.defs
```
Ensure:
```
UMASK 027
```

### Configure sudo with minimal privileges
```bash
sudo nano /etc/sudoers.d/adminuser
```
Add:
```
adminuser ALL=(ALL) ALL
```

For even better security, limit sudo commands:
```
adminuser ALL=(ALL) /usr/bin/apt, /usr/bin/systemctl, /usr/sbin/reboot, /usr/sbin/shutdown
```

### Set default shell to bash if not already
```bash
sudo chsh -s /bin/bash adminuser
```

---

## SSH Hardening

Edit SSH configuration file:
```bash
sudo nano /etc/ssh/sshd_config
```

### Disable Password Authentication
```
PasswordAuthentication no
PubkeyAuthentication yes
```

### Disable Empty Passwords
```
PermitEmptyPasswords no
```

### Disable Root Login
```
PermitRootLogin no
```

### Change Default SSH Port (Optional)
```
Port 2222
```
```bash
sudo ufw allow 2222/tcp
sudo systemctl restart sshd
sudo ufw delete allow 22
```

### Limit SSH Users
```
AllowUsers adminuser devopsuser
```

### Configure Idle Timeout
```
ClientAliveInterval 300
ClientAliveCountMax 0
```

### Disable X11 Forwarding
```
X11Forwarding no
```

### Restart SSH Service
```bash
sudo systemctl restart sshd
```

---

## Brute Force Protection

Install Fail2Ban:
```bash
sudo apt install fail2ban -y
```

Create a local configuration file:
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Edit the configuration:
```bash
sudo nano /etc/fail2ban/jail.local
```

Add/Modify these settings:
```
[sshd]
enabled = true
port = 2222
logpath = %(sshd_log)s
backend = systemd
maxretry = 3
bantime = 3600
findtime = 600
```

Restart Fail2Ban:
```bash
sudo systemctl restart fail2ban
sudo systemctl enable fail2ban
```

Check status:
```bash
sudo fail2ban-client status sshd
```

**Purpose:**
- Prevent brute-force attacks
- Automatically block malicious IP addresses
- Protect SSH services

---

## Firewall and Network Security

### Enable UFW Firewall

Enable UFW:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

**Common ports based on server role:**

**Web Server**
```bash
sudo ufw allow 80/tcp    # HTTP
sudo ufw allow 443/tcp   # HTTPS
```

**Proxy Server (Nginx/HAProxy)**
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 8080/tcp  # If using as cache/proxy port
```

**Load Balancer (HAProxy/Nginx)**
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 8080/tcp  # Management/Stats port
```

**Container Registry (Harbor/Docker Registry)**
```bash
sudo ufw allow 443/tcp   # HTTPS
sudo ufw allow 5000/tcp  # Docker Registry
sudo ufw allow 8080/tcp  # Harbor UI (if applicable)
```

**Jump Server**
```bash
sudo ufw allow 2222/tcp  # SSH (custom port)
```

Enable firewall:
```bash
sudo ufw enable
```

Verify rules:
```bash
sudo ufw status numbered
```

### Security Recommendations
- Open only required ports
- Restrict unnecessary inbound traffic
- Disable unused protocols
- Disable IPv6 if not required

---

## Filesystem Security

### Secure Critical File Permissions
```bash
sudo chmod 700 /root
sudo chmod 644 /etc/passwd
sudo chmod 600 /etc/shadow
```

### Secure Temporary Directories

Check current /tmp mount:
```bash
mount | grep /tmp
```

If mounted as tmpfs, update the mount options:
```bash
sudo nano /etc/fstab
```

Add or modify:
```
tmpfs /tmp tmpfs rw,noexec,nosuid,nodev,size=2G 0 0
```

Also secure /var/tmp:
```
tmpfs /var/tmp tmpfs rw,noexec,nosuid,nodev,size=1G 0 0
```

Remount without reboot:
```bash
sudo mount -o remount /tmp
sudo mount -o remount /var/tmp
```

Verify:
```bash
mount | grep -E '/tmp|/var/tmp'
```

**Recommended options:**
- nodev
- nosuid
- noexec

**Purpose:**
- Prevent privilege escalation
- Reduce malware execution risk

---

## Logging and Monitoring

### Install Auditd
```bash
sudo apt install auditd audispd-plugins -y
```

### Enable Auditd
```bash
sudo systemctl enable auditd
```

### Configure Audit Rules
```bash
sudo nano /etc/audit/audit.rules
```

Add these comprehensive rules:
```
# File system changes
-w /etc/passwd -p wa -k identity
-w /etc/shadow -p wa -k identity
-w /etc/group -p wa -k identity
-w /etc/gshadow -p wa -k identity
-w /etc/sudoers -p wa -k sudoers
-w /etc/sudoers.d/ -p wa -k sudoers

# System configuration
-w /etc/ssh/sshd_config -p wa -k sshd
-w /etc/ssh/ssh_config -p wa -k sshd
-w /etc/audit/ -p wa -k audit
-w /etc/apparmor/ -p wa -k apparmor
-w /etc/apparmor.d/ -p wa -k apparmor

# Critical system directories
-w /bin/ -p wa -k system
-w /usr/bin/ -p wa -k system
-w /sbin/ -p wa -k system
-w /usr/sbin/ -p wa -k system
-w /usr/local/bin/ -p wa -k system

# Kernel modifications
-w /etc/sysctl.conf -p wa -k sysctl
-w /etc/sysctl.d/ -p wa -k sysctl

# Network configuration
-w /etc/hosts -p wa -k network
-w /etc/hosts.allow -p wa -k network
-w /etc/hosts.deny -p wa -k network
-w /etc/network/ -p wa -k network

# Log monitoring
-w /var/log/auth.log -p wa -k auth
-w /var/log/syslog -p wa -k syslog

# Monitor user home directories (excluding root)
-w /home/ -p wa -k user_files

# Monitor admin user's home
-w /root/.ssh/ -p wa -k root_ssh
```

Restart auditd:
```bash
sudo systemctl restart auditd
```

Check rules loaded:
```bash
sudo auditctl -l
```

---

## Persistent Journal Logging

Enable persistent logging:
```bash
sudo mkdir -p /var/log/journal
sudo systemctl restart systemd-journald
```

**Purpose:**
- Preserve logs after reboot
- Improve forensic analysis
- Improve troubleshooting capability

---

## Service and Daemon Management

### List Services
```bash
sudo systemctl list-unit-files --type=service
```

### Disable Unnecessary Services
```bash
sudo systemctl disable <service_name>
```

### Remove Unused Network Services

**Examples:**
- cups
- avahi-daemon
- telnet
- ftp

**Purpose:**
- Reduce attack surface
- Improve system performance
- Minimize exposed services

---

## Kernel and System Hardening

Additional security parameters:
```bash
sudo nano /etc/sysctl.conf
```

Add:
```
# Disable IPv6 if not needed
# net.ipv6.conf.all.disable_ipv6 = 1
# net.ipv6.conf.default.disable_ipv6 = 1

# Prevent SYN flood attacks
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_syn_retries = 2
net.ipv4.tcp_synack_retries = 2

# Disable IP forwarding
net.ipv4.ip_forward = 0

# Disable source packet routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0

# Disable ICMP redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0

# Disable ICMP broadcasts
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Ignore bogus ICMP errors
net.ipv4.icmp_ignore_bogus_error_responses = 1

# Enable ASLR
kernel.randomize_va_space = 2

# Disable core dumps
fs.suid_dumpable = 0
kernel.core_uses_pid = 0

# Magic SysRq key (disable for security)
kernel.sysrq = 0
```

Apply changes:
```bash
sudo sysctl -p
```

Verify settings:
```bash
sudo sysctl -a | grep -E 'ip_forward|accept_redirects|tcp_syncookies'
```

**Purpose:**
- Prevent spoofing attacks
- Improve kernel security
- Reduce network attack vectors

---

## AppArmor Security

### Install AppArmor
```bash
sudo apt install apparmor apparmor-utils -y
```

### Enable Enforcement Mode
```bash
sudo aa-enforce /etc/apparmor.d/*
```

**Purpose:**
- Mandatory Access Control (MAC)
- Process isolation
- Application restriction

---

## Time Synchronization

### Install and configure time synchronization
```bash
sudo apt install chrony -y
```

### Configure NTP
```bash
sudo nano /etc/chrony/chrony.conf
```

Add time servers:
```
server 0.ubuntu.pool.ntp.org iburst
server 1.ubuntu.pool.ntp.org iburst
server 2.ubuntu.pool.ntp.org iburst
```

### Enable and start chrony
```bash
sudo systemctl enable chrony
sudo systemctl start chrony
```

### Verify time sync
```bash
chronyc sources -v
chronyc tracking
```

---

## Compliance Validation

### Install Lynis
```bash
sudo apt install lynis -y
```

### Run Security Audit
```bash
sudo lynis audit system
```

### Audit Report Location
```
/var/log/lynis.log
```

**Purpose:**
- Identify security gaps
- Validate hardening status
- Generate compliance insights

---

## Monitoring and Alerting

### Install system monitoring tools
```bash
sudo apt install htop iotop iftop nethogs -y
```

### Install and configure logwatch
```bash
sudo apt install logwatch -y
```

### Configure logwatch daily report
```bash
sudo nano /etc/cron.daily/00logwatch
```
Ensure it's executable.

### Set up mail if needed
```bash
sudo apt install mailutils -y
sudo nano /etc/logwatch/conf/logwatch.conf
```
Set:
```
MailTo = admin@example.com
```

---

## CIS Benchmark Validation

### Recommended Activities
- Verify mount options
- Verify SSH hardening
- Verify audit logging
- Verify password policies
- Verify firewall rules
- Verify service minimization

---

## NIST 800-53 Mapping

| Security Area | NIST Control |
|---------------|--------------|
| Access Control | AC |
| Audit Logging | AU |
| Configuration Management | CM |
| Incident Response | IR |
| System Integrity | SI |

---

## ISO 27001 Alignment

| ISO Control | Description |
|-------------|-------------|
| A.5 | Information Security Policies |
| A.8 | Asset Management |
| A.9 | Access Control |
| A.12 | Operations Security |
| A.13 | Network Security |
| A.16 | Incident Management |

---

## Expected Project Outcome

After completing this project, the Ubuntu server environment will:

- Follow secure baseline configurations
- Reduce attack surface
- Improve compliance readiness
- Support enterprise-grade infrastructure
- Improve audit and monitoring capability
- Provide stable and secure production deployment

---

## Conclusion

This project establishes a standardized Ubuntu Server hardening methodology aligned with enterprise security best practices. By implementing CIS Benchmark recommendations, NIST 800-53 controls, and ISO 27001 security principles, organizations can significantly improve server security, operational reliability, and compliance readiness across multiple infrastructure roles.
