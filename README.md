# IoT Honeypot Network - Healthcare Security

A comprehensive deception honeypot system designed to detect, trap, and analyze attackers targeting vulnerable IoT devices in healthcare environments.

## Project Overview

This project implements a low-to-medium interaction honeypot network simulating vulnerable medical IoT devices to:
- Proactively capture attacker behavioral patterns
- Log exploitation attempts and malware payloads
- Extract key indicators of compromise (IoCs)
- Visualize threat intelligence on a centralized dashboard

## Project Structure

```
├── honeypot/                      # Honeypot deployment & configuration
│   ├── docker-compose.yml         # Multi-container orchestration
│   ├── Dockerfile                 # Honeypot container image
│   ├── cowrie/                    # Cowrie SSH/Telnet honeypot config
│   └── honeyd/                    # Honeyd low-interaction config
├── scripts/                       # Automation & data processing
│   ├── log_parser.py              # Parse honeypot logs to JSON
│   ├── ioc_extractor.py           # Extract IoCs from captures
│   ├── geolocation.py             # Enrich IPs with geo data
│   └── setup.sh                   # Environment initialization
├── dashboard/                     # Threat intelligence visualization
│   ├── app.py                     # Flask dashboard backend
│   ├── templates/                 # HTML templates
│   ├── static/                    # CSS, JS, visualization libraries
│   └── requirements.txt           # Python dependencies
├── tests/                         # Testing & validation
│   ├── test_parser.py             # Unit tests for log parsing
│   └── integration_tests.py       # End-to-end tests
├── docs/                          # Documentation
│   ├── ARCHITECTURE.md            # System design overview
│   ├── DEPLOYMENT.md              # Deployment guide
│   ├── ANALYSIS_REPORT.md         # Final threat analysis
│   └── NETWORK_DIAGRAM.md         # Network architecture
├── .github/
│   └── workflows/                 # CI/CD pipelines
│       ├── test.yml               # Automated testing
│       └── security-scan.yml      # Security scanning
└── .gitignore

## Key Features

- **Virtual Honeypot Deployment**: Docker-based isolated environment simulating medical IoT devices
- **Logging & Analysis Engine**: Comprehensive capture of connection attempts, keystrokes, and payloads
- **Threat Intelligence Dashboard**: Real-time visualization of attacks with geolocation mapping
- **Automated IoC Extraction**: Identification of attacker IPs, malware hashes, and executed commands

## Technology Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| Honeypot | Cowrie/Honeyd | SSH/Telnet deception with logging |
| Infrastructure | Docker | Containerized isolation |
| Data Processing | Python | Log parsing and analysis |
| Visualization | Flask + D3.js | Dashboard and mapping |
| Database | JSON/SQLite | Log storage and querying |

## 4-Week Roadmap

### Week 1: Environment Setup & Device Simulation
- [ ] Configure isolated Docker environment
- [ ] Deploy Cowrie with medical IoT device simulation
- [ ] Configure realistic banners and filesystem
- [ ] Set up network isolation

### Week 2: Exposure & Data Capture
- [ ] Configure controlled exposure (test network)
- [ ] Validate logging functionality
- [ ] Test brute-force capture
- [ ] Verify payload recording

### Week 3: Log Parsing & Threat Intelligence
- [ ] Develop log parsing pipeline
- [ ] Extract indicators of compromise
- [ ] Create IoC database
- [ ] Implement threat correlation

### Week 4: Dashboarding & Geolocation Analysis
- [ ] Build web dashboard
- [ ] Implement IP geolocation
- [ ] Create attack visualization
- [ ] Generate final analysis report

## Quick Start

```bash
# Clone repository
git clone https://github.com/kakarot2003/IoT-Honeypot-Network.git
cd IoT-Honeypot-Network

# Setup environment
./scripts/setup.sh

# Deploy honeypot
docker-compose up -d

# Access dashboard
open http://localhost:5000
```

## Security Considerations

⚠️ **CRITICAL**: This honeypot is designed for controlled environments only:
- Deploy within isolated networks (VLAN/subnet isolation)
- Implement strict egress filtering
- Monitor for breakthrough attempts
- Log all administrative access
- Comply with organizational security policies

## Success Metrics

✓ **Honeypot Fidelity**: Successfully deceives attackers into believing it's a real medical device
✓ **Logging Coverage**: Captures 100% of connection attempts and payloads
✓ **Dashboard Accuracy**: Correctly geolocates and categorizes attacks
✓ **HIPAA Compliance**: Demonstrates advanced proactive security measures

## User Personas

| Role | Needs | Interaction |
|------|-------|-------------|
| Threat Researcher (Intern) | Safe malware analysis environment | Deploy, monitor, extract payloads |
| Network Administrator | Early detection of lateral movement | Alert on internal IP connections |
| Compliance Auditor | Proof of advanced security | Threat intelligence reports |

## Contributing

This is an internship project. For questions or improvements, open an issue in the GitHub repository.

## License

MIT License - See LICENSE file for details

## Contact

Project Owner: kakarot2003