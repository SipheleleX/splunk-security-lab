#  Splunk Security Monitoring Lab

[![Docker](https://img.shields.io/badge/Docker-Compose-blue.svg)](https://www.docker.com/)
[![Splunk](https://img.shields.io/badge/Splunk-Enterprise-green.svg)](https://www.splunk.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A containerized Security Operations Center (SOC) environment demonstrating real-world threat detection and security monitoring using Splunk Enterprise - the industry-standard SIEM platform.

![Security Dashboard](<img width="1903" height="1040" alt="image" src="https://github.com/user-attachments/assets/b3dbe122-d722-4cc8-8353-740e8233ffe1" />)
*Main security monitoring dashboard showing real-time attack detection*

##  Project Overview

This project simulates a production SOC environment capable of detecting, analyzing, and visualizing cybersecurity threats in real-time. Built with industry-standard tools, it demonstrates practical skills applicable to SOC Analyst, Security Engineer, and SIEM Administrator roles.

### Key Features

-  **Real-time Threat Detection**: Monitors and alerts on security events as they occur
-  **Attack Simulation**: Automated generation of 8+ attack types including SQL Injection, XSS, Command Injection
-  **Custom Dashboards**: Executive, analyst, and real-time monitoring views
-  **Alert Management**: Configurable alerting for critical security events
-  **Containerized Deployment**: Fully portable using Docker Compose
-  **Industry Tools**: Splunk Enterprise (used by 70% of Fortune 500 companies)

##  Architecture
```
┌──────────────────────────────────────────────────────┐
│                   Splunk SIEM                        │
│  ┌────────────────────────────────────────────┐     │
│  │  Indexer & Search Head                     │     │
│  │  • Real-time Indexing                      │     │
│  │  • SPL Query Engine                        │     │
│  │  • Dashboards & Visualizations             │     │
│  └────────────────────────────────────────────┘     │
│           ↑                                          │
│           │ Logs & Events                            │
│           │                                          │
│  ┌────────┴─────────────────────────────────┐       │
│  │  Universal Forwarder                     │       │
│  │  • Log Collection                        │       │
│  │  • Data Forwarding                       │       │
│  └──────────────────────────────────────────┘       │
└──────────────────────────────────────────────────────┘
                        ↑
                        │ Network Traffic & Logs
                        │
┌──────────────────────────────────────────────────────┐
│            Application Layer                          │
│  ┌──────────────┐  ┌──────────────┐                 │
│  │    DVWA      │  │    Nginx     │                 │
│  │ (Vulnerable  │  │ (Web Server) │                 │
│  │   Web App)   │  │              │                 │
│  └──────────────┘  └──────────────┘                 │
└──────────────────────────────────────────────────────┘
```

##  Technologies Used

| Component | Technology | Purpose |
|-----------|------------|---------|
| **SIEM** | Splunk Enterprise 9.x | Security monitoring & analysis |
| **Log Shipping** | Splunk Universal Forwarder | Log collection & forwarding |
| **Vulnerable App** | DVWA | Attack simulation target |
| **Web Server** | Nginx | Additional monitoring target |
| **Container Platform** | Docker & Docker Compose | Orchestration & deployment |
| **Scripting** | Bash | Automation & attack simulation |

##  Quick Start

### Prerequisites

- Linux environment (Ubuntu 20.04+ recommended)
- Docker & Docker Compose installed
- 8GB RAM minimum
- 20GB free disk space

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/SipheleleX/splunk-security-lab.git
cd splunk-security-lab
```

2. **Set vm.max_map_count (required for Splunk)**
```bash
sudo sysctl -w vm.max_map_count=262144
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
```

3. **Start the lab**
```bash
docker-compose up -d
```

4. **Wait for Splunk to start (2-3 minutes)**
```bash
docker logs -f splunk
# Wait for "Ansible playbook complete" message
```

5. **Configure log monitoring**
```bash
docker exec -it splunk bash
/opt/splunk/bin/splunk add monitor /logs -index main -sourcetype _json -auth admin:Changeme123!
exit
```

6. **Access Splunk**
```
URL: http://localhost:8000
Username: admin
Password: Changeme123!
```

### Running Attack Simulations
```bash
# Make the script executable
chmod +x attack-simulator.sh

# Run attack simulation
./attack-simulator.sh
```

This generates various attack types:
- SQL Injection (UNION-based, Boolean-based)
- Cross-Site Scripting (XSS)
- Command Injection
- Directory Traversal
- Brute Force attacks
- Security scanner detection

##  Dashboards & Visualizations

### 1. Security Monitoring Dashboard
**Purpose**: Executive overview of security posture

**Key Metrics**:
- Total attacks detected
- Attack type distribution (Pie chart)
- Severity breakdown (Bar chart)
- Attack timeline (Line chart)

**SPL Query**:
```spl
index=main sourcetype=_json attack_type=* 
| stats count by attack_type, severity
```

### 2. Real-Time Attack Feed
**Purpose**: Live security event monitoring

**Features**:
- Streaming attack data
- 30-second refresh window
- Color-coded severity levels

**SPL Query**:
```spl
index=main sourcetype=_json attack_type=*
| eval time=strftime(_time, "%H:%M:%S")
| table time, attack_type, severity, target, payload
```

### 3. Critical Alerts Dashboard
**Purpose**: High-priority threat tracking

**SPL Query**:
```spl
index=main sourcetype=_json severity="critical"
| table _time, attack_type, target, payload
| sort -_time
```

##  Sample Splunk Searches (SPL)

### Basic Searches

**View all attacks**:
```spl
index=main sourcetype=_json attack_type=*
```

**SQL Injection attacks only**:
```spl
index=main sourcetype=_json attack_type="sql_injection"
```

**Critical severity attacks**:
```spl
index=main sourcetype=_json severity="critical"
```

### Advanced Analytics

**Attack statistics**:
```spl
index=main sourcetype=_json 
| stats count as "Total Attacks", 
        count(eval(severity="critical")) as "Critical", 
        count(eval(severity="high")) as "High",
        count(eval(severity="medium")) as "Medium",
        count(eval(severity="low")) as "Low"
```

**Attack timeline with predictions**:
```spl
index=main sourcetype=_json 
| timechart span=5m count by attack_type
| predict count future_timespan=10
```

**Top attack sources**:
```spl
index=main sourcetype=_json 
| stats count by target, attack_type 
| sort -count 
| head 10
```

##  Skills Demonstrated

### Technical Skills
-  SIEM Administration (Splunk)
-  Security Log Analysis
-  SPL (Splunk Processing Language)
-  Attack Pattern Recognition
-  Dashboard Development
-  Docker & Container Orchestration
-  Linux System Administration
-  Bash Scripting & Automation

### Security Knowledge
-  OWASP Top 10 Vulnerabilities
-  Attack Vectors & TTPs
-  Security Monitoring Best Practices
-  Incident Detection & Analysis
-  Threat Classification

##  Detection Capabilities

| Attack Type | Detection Method | Severity | Status |
|-------------|------------------|----------|--------|
| SQL Injection | Pattern matching (UNION, OR) | Critical |  Active |
| XSS | Script tag & event detection | Medium |   Active |
| Command Injection | Shell metacharacter detection | Critical |  Active |
| Directory Traversal | Path traversal patterns | High |  Active |
| Brute Force | Failed login threshold | Medium |  Active |
| Scanner Detection | User-agent fingerprinting | Low |  Active |
| CSRF | Request pattern analysis | Medium |  Active |
| File Upload | Extension validation bypass | Critical |  Active |

##  Configuration

### Changing the Admin Password

Edit `docker-compose.yml`:
```yaml
environment:
  - SPLUNK_PASSWORD=YourNewPassword123!
```

### Adding Custom Attack Types

Edit `attack-simulator.sh` and add your attack patterns:
```bash
log_attack "custom_attack" "$TARGET" "payload_here" "severity_level"
curl -s "$TARGET/your-endpoint?param=attack" > /dev/null
```

### Configuring Alerts

1. Go to Splunk → Search & Reporting
2. Run your search query
3. Click **Save As** → **Alert**
4. Configure trigger conditions and actions

##  Troubleshooting

### Splunk won't start
```bash
# Check logs
docker logs splunk

# Ensure vm.max_map_count is set
sudo sysctl -w vm.max_map_count=262144

# Restart containers
docker-compose restart
```

### No attacks showing in Splunk
```bash
# Verify log monitoring is configured
docker exec -it splunk /opt/splunk/bin/splunk list monitor -auth admin:Changeme123!

# Check if attack logs exist
ls -lh attack-logs/

# Re-run attack simulation
./attack-simulator.sh
```

### Port conflicts
```bash
# Check if ports are in use
sudo netstat -tulpn | grep -E ':(8000|8080|8081)'

# Modify docker-compose.yml to use different ports
```

##  Screenshots

![Dashboard Overview](screenshots/dashboard-overview.png)
*Security monitoring dashboard with real-time metrics*

![Attack Detection](screenshots/attack-detection.png)
*Live attack feed showing detected threats*

![SPL Query](screenshots/spl-query.png)
*Custom SPL query analyzing attack patterns*

##  Future Enhancements

- [ ] Integration with threat intelligence feeds (MISP, AlienVault OTX)
- [ ] Machine learning for anomaly detection
- [ ] Network traffic analysis with Suricata IDS
- [ ] Automated incident response playbooks (SOAR)
- [ ] User behavior analytics (UBA)
- [ ] Multi-node Splunk cluster deployment
- [ ] Cloud integration (AWS, Azure)

##  Learning Resources

- [Splunk Documentation](https://docs.splunk.com)
- [SPL Reference Guide](https://docs.splunk.com/Documentation/Splunk/latest/SearchReference)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

##  License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

##  Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

##  Author

**Siphelele**
- GitHub: [@SipheleleX](https://github.com/SipheleleX)
- LinkedIn: [[Siphelele Xaba](https://www.linkedin.com/in/siphelele-x-929a45267/)]

##  Acknowledgments

- Splunk for providing enterprise-grade SIEM software
- DVWA team for the vulnerable web application
- Docker community for containerization tools

---

 If you found this project useful, please consider giving it a star!

**Built with love and passion for the cybersecurity community**
