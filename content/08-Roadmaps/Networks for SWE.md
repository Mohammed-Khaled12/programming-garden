#  30-Days of Networking for SWEs

**Goal:** Mastering network concepts from a Software Engineer's perspective.

> **Note:** This is _not_ for Network Engineers. We are not dealing with Cisco switches, cables, or physical routers. We care about how our code travels through the internet.

---

##  Content Overview

1. **Application Layer:** HTTP/1.1 vs 2 vs 3, DNS, Cookies, WebSockets, Sockets Programming.
2. **Transport Layer:** TCP, 3-Way Handshake, Flow/Congestion Control, UDP, Head-of-line blocking.
3. **Network Layer:** IPv4, IPv6, DHCP, NAT, Subnetting (CIDR).
4. **Security & Architecture:** TLS/SSL Handshake, Proxies (Reverse & Forward), Load Balancers.

---

##  Resources
###  The Main Reference (Textbook & Labs)

- **Book:** _Computer Networking: A Top-Down Approach_ (Kurose & Ross).
- **Official Site:** [Kurose & Ross Official Website](https://gaia.cs.umass.edu/kurose_ross/index.php) (Videos & Labs).
- **Wireshark Labs:** [Download Labs](https://gaia.cs.umass.edu/kurose_ross/wireshark.php)

###  Miscellaneous (Video Explanations)

- **Hussein Nasser:** [YouTube Channel](https://www.youtube.com/@hnasr)
- **Network Direction:** [Network Fundamentals Playlist](https://youtube.com/playlist?list=PLDQaRcbiSnqF5U8ffMgZzS7fq1rHUI3Q8&si=TjD0_feHIOEqsLmc)
- **CloudKode:** [CCNA 200-301 First 10 Videos](https://youtube.com/playlist?list=PLZmPGUyBFvUrvoa-NYzcUWFpxoZR11id_&si=zB7czjsyI5lCbvD6)

---
# Linux System Monitor

A lightweight, automated system monitoring tool for Linux. It extracts core system metrics (CPU, RAM, Uptime, Load Average) directly from the `/proc` filesystem using a C++ backend, and dynamically updates an HTML dashboard and a Terminal UI using Bash scripting and Cron jobs.

## Features
* Extracts data natively via C++ (reads directly from /proc).
* Zero-configuration Web Dashboard (no web server required).
* Terminal UI for quick command-line monitoring.
* Fully automated via Cron (updates every minute in the background).

## Prerequisites
Make sure you have the following installed on your Linux machine:
* `g++` and `make` (for compiling the C++ backend)
* `jq` (for JSON parsing in Bash scripts)

## Quick Start

1. Clone the repository and navigate into it:
```bash
git clone [[https://github.com/yourusername/LinuxSystemMonitor.git](https://github.com/Mohammed-Khaled12/Linux-System-Monitor-App.git)]
cd LinuxSystemMonitor
```

2. Run the automated setup script:

```Bash
chmod +x setup.sh
./setup.sh
```

_Note: The setup script compiles the C++ code, sets file permissions, and automatically configures a Cron job to run the update script every minute._

## Viewing the Dashboard

**Web UI:** Open `web/index.html` in any web browser. The data displayed will be updated automatically every minute in the background.

**Terminal UI:** If you prefer the command line, you can view the formatted live terminal dashboard by running:

```Bash
watch -n 60 ./scripts/term_ui.sh
```

## Architecture Overview

1. **Backend (C++):** Reads raw kernel data from `/proc` files and generates a structured `data.json`.
    
2. **Processing (Bash):** Extracts values from the JSON using `jq` and injects them into an HTML template using `sed`.
    
3. **Automation (Cron):** Orchestrates the execution of the backend and processing scripts seamlessly every minute.