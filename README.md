# Ubuntu Server Hardening Guide

<div align="center">

![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Security](https://img.shields.io/badge/Security-Audit-blue?style=for-the-badge)
![CIS](https://img.shields.io/badge/CIS-Benchmark-green?style=for-the-badge)
![NIST](https://img.shields.io/badge/NIST-800--53-red?style=for-the-badge)
![ISO](https://img.shields.io/badge/ISO-27001-orange?style=for-the-badge)

**Enterprise Linux Infrastructure Preparation and Hardening**

*A comprehensive guide for securing Ubuntu Server in production environments*

[![Documentation](https://img.shields.io/badge/docs-markdown-blue?style=for-the-badge)](./HARDENING-GUIDE.md)
[![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)](./LICENSE)

</div>

---

## 📋 Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Supported Server Roles](#supported-server-roles)
- [Compliance Standards](#compliance-standards)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Hardening Checklist](#hardening-checklist)
- [Server Role Specific Configuration](#server-role-specific-configuration)
- [Security Controls](#security-controls)
- [Monitoring and Maintenance](#monitoring-and-maintenance)
- [Compliance Validation](#compliance-validation)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

---

## Overview

This repository provides a complete, production-ready Ubuntu Server hardening guide for enterprise Linux infrastructure. It implements security best practices based on **CIS Benchmarks**, **NIST 800-53**, and **ISO 27001** recommendations across multiple server roles including Web Servers, Reverse Proxies, Load Balancers, Container Registries, and Jump Servers.

### Why This Guide?

Organizations often deploy Ubuntu servers without standardized security configurations, leading to:
- 🔓 Weak access control and authentication
- 📡 Unnecessary service exposure
- 📊 Poor logging and auditing
- 🚪 Misconfigured SSH access
- 🐛 Unpatched vulnerabilities
- 📋 Lack of compliance readiness

This guide addresses all these issues by implementing a secure, maintainable, and compliance-ready Ubuntu Server baseline.

---

## 🎯 Key Features

- **🔒 Comprehensive Security Hardening**
  - SSH hardening with key-based authentication
  - Firewall configuration with UFW
  - Filesystem security and mount options
  - Kernel parameter optimization
  - AppArmor mandatory access control

- **👥 User and Access Management**
  - Principle of least privilege
  - Strong password policies
  - Sudo access control
  - User role separation

- **📊 Advanced Monitoring & Auditing**
  - Auditd configuration for system monitoring
  - Fail2ban for brute force protection
  - System resource monitoring tools
  - Persistent journal logging

- **✅ Compliance Ready**
  - CIS Benchmark alignment
  - NIST 800-53 control mapping
  - ISO 27001 security controls
  - Lynis security auditing

- **🔄 Role-Based Configuration**
  - Web Server
  - Reverse Proxy Server
  - Load Balancer
  - Container Image Repository
  - Jump Server / Bastion Host

---

## 🖥️ Supported Server Roles

| Server Type | Purpose | Key Ports |
|-------------|---------|-----------|
| **Web Server** | Host applications/websites | 80, 443 |
| **Proxy Server** | Reverse proxy and traffic routing | 80, 443, 8080 |
| **Load Balancer** | Distribute application traffic | 80, 443, 8080 |
| **Container Registry** | Store container images | 443, 5000, 8080 |
| **Jump Server** | Secure administrative access | 2222 (SSH) |

---

## 📜 Compliance Standards

### CIS Benchmark
- Password policies and authentication
- Filesystem permissions and mount options
- Network and firewall configuration
- Logging and auditing
- Service minimization

### NIST 800-53 Controls
- **AC**: Access Control
- **AU**: Audit and Accountability
- **CM**: Configuration Management
- **IR**: Incident Response
- **SI**: System and Information Integrity

### ISO 27001 Controls
- **A.5**: Information Security Policies
- **A.8**: Asset Management
- **A.9**: Access Control
- **A.12**: Operations Security
- **A.13**: Network Security
- **A.16**: Incident Management

---

## 📋 Prerequisites

### System Requirements
- Ubuntu Server 20.04 LTS or 22.04 LTS
- Minimum 2GB RAM
- 20GB available storage
- Network connectivity
- Root/sudo access

### Knowledge Requirements
- Basic Linux command-line operations
- Understanding of system administration
- Familiarity with security concepts
- Basic networking knowledge

---

## 📁 Repository Structure

```
ubuntu-hardening-guide/
├── README.md                 # This file
├── HARDENING-GUIDE.md        # Complete hardening documentation
├── LICENSE                   # MIT License
├── scripts/
│   ├── ubuntu-hardening.sh   # Main hardening script
│   ├── audit-setup.sh        # Auditd configuration
│   ├── firewall-setup.sh     # UFW configuration
│   └── security-audit.sh     # Lynis audit script
├── configs/
│   ├── sshd_config           # Hardened SSH configuration
│   ├── audit.rules           # Auditd rules
│   ├── sysctl.conf           # Kernel parameters
│   └── jail.local            # Fail2ban configuration
├── docs/
│   ├── CIS-Benchmarks.md     # CIS benchmark details
│   ├── NIST-800-53.md        # NIST control mapping
│   └── ISO-27001.md          # ISO 27001 alignment
└── templates/
    ├── web-server.md         # Web server specific config
    ├── load-balancer.md      # Load balancer config
    └── jump-server.md        # Jump server config
```

---

## ✅ Hardening Checklist

### Access Control
- [ ] Create separate admin and application users
- [ ] Configure strong password policies
- [ ] Set up SSH key-based authentication
- [ ] Disable root login
- [ ] Implement sudo with minimal privileges

### Network Security
- [ ] Configure UFW firewall rules
- [ ] Disable IPv6 if not needed
- [ ] Change default SSH port
- [ ] Implement Fail2ban
- [ ] Close unnecessary ports

### System Hardening
- [ ] Update and patch system
- [ ] Remove unnecessary packages
- [ ] Secure temporary directories
- [ ] Configure kernel parameters
- [ ] Enable AppArmor

### Monitoring & Auditing
- [ ] Install and configure auditd
- [ ] Set up persistent journal logging
- [ ] Enable system monitoring tools
- [ ] Configure logwatch for reporting
- [ ] Implement regular security audits

### Compliance
- [ ] Run Lynis security audit
- [ ] Review CIS benchmarks
- [ ] Validate NIST controls
- [ ] Check ISO 27001 compliance

---

## 👥 Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Guidelines
- Follow the existing coding style
- Update documentation as needed
- Test your changes thoroughly
- Include examples where applicable

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **CIS** - Center for Internet Security for their benchmarks
- **NIST** - National Institute of Standards and Technology for 800-53 controls
- **ISO** - International Organization for Standardization for 27001
- **Ubuntu Community** - For maintaining an excellent server distribution
- **Lynis Team** - For the comprehensive security audit tool

---

## 📚 Additional Resources

### Official Documentation
- [Ubuntu Server Documentation](https://ubuntu.com/server/docs)
- [CIS Benchmarks](https://www.cisecurity.org/benchmark/ubuntu_linux/)
- [NIST 800-53](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final)
- [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)

### Security Tools
- [Lynis](https://cisofy.com/lynis/)
- [Auditd](https://linux.die.net/man/8/auditd)
- [Fail2Ban](https://www.fail2ban.org/)
- [AppArmor](https://apparmor.net/)

### Community Forums
- [Ubuntu Forums](https://ubuntuforums.org/)
- [Server Fault](https://serverfault.com/)
- [Reddit - r/sysadmin](https://www.reddit.com/r/sysadmin/)
- [Reddit - r/linuxadmin](https://www.reddit.com/r/linuxadmin/)

---

<div align="center">

**[⬆ Back to Top](#ubuntu-server-hardening-guide)**

*Built with ❤️ for the open source community*

[![Star](https://img.shields.io/github/stars/yourusername/ubuntu-hardening-guide?style=social)](https://github.com/yourusername/ubuntu-hardening-guide)
[![Fork](https://img.shields.io/github/forks/yourusername/ubuntu-hardening-guide?style=social)](https://github.com/yourusername/ubuntu-hardening-guide)

</div>
