================================================================
NS PROJECT — SELF-HEALING NETWORK ARCHITECTURE
NIST SP 800-207 Zero Trust Architecture Implementation
================================================================

PROJECT OVERVIEW
----------------
A working multi-machine Zero Trust security system that automatically
detects, blocks, and self-heals from network attacks using the
Policy Decision Point (PDP) / Policy Enforcement Point (PEP) model
defined in NIST SP 800-207.

MACHINES & ROLES
----------------
  Windows (192.168.50.1)   : PDP Agent — decision engine + AI analysis
  Ubuntu  (192.168.50.135) : PEP Agent — iptables enforcement
                             IDS Agent — packet capture (Scapy)
                             Dashboard — SOC monitoring (port 8080)
  Kali    (192.168.x.x)    : Attack Simulator (attacker machine)

STARTUP ORDER (IMPORTANT)
--------------------------
  1. Windows  : ollama serve               (start AI engine)
  2. Windows  : python pdp_agent.py        (start PDP)
  3. Ubuntu   : sudo python3 pep_agent.py  (start PEP)
  4. Ubuntu   : sudo python3 ids_agent.py  (start IDS)
  5. Ubuntu   : open dashboard.html in browser
  6. Kali     : sudo python3 attack_simulator.py

  FIRST TIME ONLY: del security.db  (fresh database)

ATTACK PIPELINE (6 STEPS)
--------------------------
  1. Kali sends flood/scan packets to Ubuntu
  2. IDS (Scapy) detects threshold breach → classifies attack type
  3. IDS sends JSON alert to PDP via HTTP POST
  4. PDP checks trust score, zone policy → decides action
  5. PDP sends BLOCK command to PEP
  6. PEP adds iptables DROP rule → auto-removes after block duration

ATTACK TYPES DETECTED
---------------------
  - ICMP_FLOOD   : ping flood (hping3 -1)
  - SYN_FLOOD    : TCP SYN flood (hping3 -S)
  - UDP_FLOOD    : UDP flood (hping3 -2)
  - PORT_SCAN    : nmap-style reconnaissance (15+ unique ports in 10s)
  - DDOS         : 3+ IPs attacking same victim within 60s

NIST SP 800-207 COMPLIANCE MAPPING
-------------------------------------
  Tenet 1 : All sources treated as threats — every packet inspected
            → IDS Agent inspects every packet with Scapy (24/7)

  Tenet 3 : Per-session access, no permanent implicit trust
            → Auto-unblock after block duration (no permanent access)

  Tenet 5 : Continuous monitoring of asset integrity
            → IDS 24/7 + 60s watchdog re-verification after unblock

  Tenet 6 : Authentication and authorization strictly enforced
            → PEP: source IP + API key dual authentication
            → PDP: API key on every IDS alert

  Tenet 7 : Collect telemetry to improve security posture
            → HMAC-SHA256 tamper-proof log on every event
            → Adaptive threshold: mean + 3×std_dev of traffic baseline
            → AI threat analysis stored per event

KEY FEATURES
------------
  [1] Dynamic Trust Score  : 0-100, decreases on attack, recovers over time
  [2] Exponential Backoff  : Block durations: 60s→120s→300s→600s→1hr
  [3] Zone-Based Policy    : RED/YELLOW/GREEN zones with different thresholds
  [4] Watchdog Window      : 60s re-verification after every auto-unblock
  [5] HMAC-SHA256 Logging  : Every log entry signed, dashboard shows ✓/✗
  [6] Adaptive Threshold   : IDS learns normal traffic, adapts detection
  [7] AI Threat Analysis   : Ollama qwen3:4b analyses every event
  [8] GeoIP Intelligence   : Country, city, ISP, proxy/datacenter flags
  [9] DDoS Correlation     : Multi-IP attack detection (3+ IPs, 60s window)
  [10] Port Scan Detection : Reconnaissance detection (15 ports in 10s)
  [11] PDF Report          : One-click incident report from dashboard
  [12] Permanent Block     : Trust=0 → permanent iptables DROP rule

REQUIREMENTS
------------
  Windows : pip install flask flask-cors requests cryptography
  Ubuntu  : pip3 install flask flask-cors requests scapy cryptography
  Ollama  : https://ollama.com → ollama pull qwen3:4b

HTTPS SETUP (TENET 2)
---------------------
  1. Open terminal on Windows PDP machine
  2. Run: python generate_certs.py (creates cert.pem and key.pem)
  3. Copy cert.pem and key.pem to the Ubuntu PEP machine
  4. Ensure both are in the same folder as pdp_agent.py and pep_agent.py
  5. The browser will show "Not Secure" because it is a self-signed certificate.
     Click "Advanced" -> "Proceed to 192.168.50.1 (unsafe)" to view dashboard.

================================================================
