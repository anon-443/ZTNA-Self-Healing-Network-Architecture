# ZTNA-Self-Healing-Network-Architecture
NIST SP 800-207 compliant Zero Trust Network Architecture with self-healing, AI threat analysis (Ollama), dynamic trust scoring, and real-time SOC dashboard. Detects → decides → enforces in < 500ms.

**NIST SP 800-207 Compliant · Zero Trust · Self-Healing · <500ms Response**
A production-ready Zero Trust Network Access (ZTNA) implementation that automatically detects attacks, dynamically scores trust, enforces iptables blocks, and self-heals — all with AI-powered threat analysis and a real-time SOC dashboard.

---

## Features

| Category | Capabilities |
|----------|---------------|
| **Detection** | ICMP/SYN/UDP flood, port scan, DDoS correlation (3+ IPs → 60s window), adaptive rate thresholds |
| **Trust Engine** | Per-IP score (100 → 0), −20 per attack, +5/hr recovery, permanent block at 0 |
| **Enforcement** | iptables DROP rules, exponential backoff `[60,120,300,600,3600]s`, watchdog re-verification (60s) |
| **Intelligence** | AI analysis (Ollama qwen3:4b), GeoIP enrichment (ip-api.com), HMAC-SHA256 audit logs |
| **Dashboard** | 7-tab HTTPS SOC console: Overview, Attacks, AI, Geo, DDoS, Analytics, PDF Report |
| **NIST Compliance** | All 7 tenets of SP 800-207 fully implemented |

**Pipeline:** Detect → Decide → Enforce in **<500ms**

---

## Tech Stack

- **IDS/PEP:** Python, Scapy, Flask, iptables
- **PDP:** Python, Flask, SQLite
- **AI:** Ollama (qwen3:4b) — local LLM, no cloud
- **Dashboard:** HTML/JS + Flask HTTPS
- **Security:** HMAC-SHA256, TLS 1.2/1.3, API key auth

---

## Quick Start

```bash
# 1. Clone
git clone https://github.com/yourusername/ztna-self-healing.git
cd ztna-self-healing

# 2. Install dependencies (each agent)
pip install -r requirements.txt

# 3. Configure
cp config.example.json config.json
# Edit: IPs, thresholds, API keys

# 4. Run agents
python ids_agent.py      # Ubuntu — detection
python pdp_server.py     # Windows — policy engine
python pep_agent.py      # Ubuntu — enforcement

# 5. Access dashboard
