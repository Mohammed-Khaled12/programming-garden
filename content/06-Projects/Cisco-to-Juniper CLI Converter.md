
## Overview

A command-line tool written in C++ that translates Cisco IOS CLI commands into their Juniper JunOS equivalents. The project originated from a real pain point: engineers who know Cisco IOS well often struggle with JunOS syntax, which follows a fundamentally different philosophy.

**Goal:** Build a lightweight, extensible translator — not a full network automation platform. Start narrow, get something working end-to-end, then expand.

**Tech stack decision:** C++ (not Go). Reasoning: already know C++ well, no need to learn a new language for this project. Go's advantages (easier CLI tooling, goroutines for concurrency) aren't critical for Phase 1, since command translation isn't performance-bound. Go's concurrency model becomes genuinely useful in Phase 2 (see below), so that phase may be revisited in Go later if C++ threading proves painful.

---

## Why This Is a Real Problem (Not Just Syntax)

Cisco IOS and Juniper JunOS aren't just "different spellings of the same commands" — they follow different configuration philosophies:

| | Cisco IOS | Juniper JunOS |
|---|---|---|
| Config model | Commands apply immediately to running-config | Two-stage: edit a **candidate config**, then `commit` to apply |
| Command structure | Hierarchical modes — you "enter" a context (e.g. `configure terminal` → `interface gi0/1`) and issue commands inside it | Flat — every command is a full `set` statement with the entire path included, e.g. `set interfaces ge-0/0/1 unit 0 family inet address 10.0.0.1/24` |

This means translation isn't purely 1-to-1 string substitution in all cases — some Cisco commands map to multiple Juniper `set` lines, and some require format conversions (e.g. subnet mask → CIDR notation).

---

## How Network Engineers Actually Use These CLIs (context for anyone new to networking)

- Devices are accessed via **console cable** (direct physical connection, used for initial setup) or **SSH/Telnet** (once the device has an IP and is reachable on the network).
- Engineers typically connect using a terminal emulator (PuTTY, SecureCRT, etc.).
- What they see is **not a Linux shell** — it's the CLI of the device's own operating system (Cisco IOS or Juniper JunOS).
- In Phase 1, the tool doesn't connect to any device — it's a standalone translator. The user pastes the translated output into their own terminal session manually.

---

## Architecture (Phase 1 — Standalone Translator)

Four components, each feeding into the next:

### 1. Command Database
Not code — data. A JSON or CSV file mapping Cisco patterns to Juniper equivalents, kept separate from the codebase so new commands can be added without recompiling.

Example entries:
```
cisco_pattern: "interface {type}{num}"
junos_equivalent: "set interfaces {type}-0/0/{num}"

cisco_pattern: "ip address {ip} {mask}"
junos_equivalent: "set interfaces {iface} unit 0 family inet address {ip}/{cidr}"
```

### 2. Tokenizer
Splits an input line into words using `std::istringstream`. Example: `"interface GigabitEthernet0/1"` → `["interface", "GigabitEthernet0/1"]`. Everything downstream operates on tokens, not raw strings.

### 3. Pattern Matcher / Translation Engine
Converts each database pattern into a `std::regex` with capture groups. Uses `std::regex_match` and `std::smatch` to extract variables (interface numbers, IP addresses, etc.) from the input, then substitutes them into the Juniper output template. This is where format conversions (like subnet mask → CIDR) get handled.

### 4. CLI Driver (`main.cpp`)
Reads input line-by-line from stdin or a file, passes each line to the translation engine, prints the result.

```cpp
std::string line;
while (std::getline(std::cin, line)) {
    std::cout << translate(line) << "\n";
}
```

---

## Phase 1 Execution Plan

1. **Scope the commands.** Pick 15–20 common commands to support first (interfaces, VLANs, static routes, basic ACLs). Don't try to cover everything — that stalls the project before it starts. Write out Cisco → Juniper mappings by hand first.
2. **Build the command database.** JSON/CSV file with each pattern and its equivalent. Keeping this separate from code means new commands are just data entries, no recompilation.
3. **Build the tokenizer.** Simple function splitting a line into words via `std::istringstream`.
4. **Build the pattern matcher.** Convert each database pattern to a regex with capture groups; extract variables with `std::regex_match` / `std::smatch`.
5. **Build the translation engine.** Takes the matched pattern + extracted variables, builds the final Juniper output string. Handles conversions like subnet mask → CIDR.
6. **Wire it into a CLI.** `main.cpp` reads input line-by-line, calls the engine, prints output. This is the first working, testable version.
7. **Test against real commands.** Run against sample/real Cisco configs (searchable online) and see where pattern matching fails — each failure reveals a new pattern to add to the database.

**Priority note:** Steps 1–2 (scoping + database design) matter more than the code itself early on. A working 15-command MVP is a complete, CV-worthy deliverable. The common mistake is writing complex regex before the scope is defined, and burning time on edge cases that don't matter yet.

---

## Phase 2 — Live Auto-Translation (SSH Proxy)

**Status: parked for later — noted here for when the project resumes.**

The idea: instead of the user pasting translated output manually, they type Cisco-style commands directly into their normal SSH session, and translation happens live, transparently, against the real Juniper device.

### Why this is a fundamentally different project (not an extension of Phase 1)

Phase 1 is a standalone program: input → output, no network involvement. Phase 2 requires sitting **in the middle of an active SSH connection** between the user and the Juniper device — intercepting, translating, forwarding, and passing through responses — not just transforming one line at a time in isolation.

### Architecture: SSH Proxy

Instead of the user SSHing directly to the Juniper device, they SSH into this tool, which maintains its own real SSH connection to the actual device on their behalf:

```
User → (SSH) → Translator (proxy) → (real SSH) → Juniper device
```

Every line the user types arrives at the proxy first, gets translated, and is forwarded to the real device. Any response from the device is passed back to the user transparently (unmodified), so normal `show` command output, prompts, etc. still work.

### What this requires (beyond standard C++)

| Component             | Purpose                                                                                                                                                                                                                                                  |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `libssh` or `libssh2` | C++ library to act as both an SSH server (accepting the user's connection) and an SSH client (connecting to the real device)                                                                                                                             |
| PTY handling          | Managing a pseudo-terminal so the interactive session behaves normally (prompts, colors, etc.)                                                                                                                                                           |
| Async I/O             | Must handle two directions simultaneously (user input + device output). Go's goroutines would make this notably easier; in C++, this means threads or `select()`/`epoll`. This is the main reason Go might get reconsidered for this phase specifically. |
| Passthrough logic     | Any line that isn't a recognized command (e.g. `show interfaces`) must pass through unmodified — only recognized Cisco commands get translated                                                                                                           |

### Honest scope assessment

Phase 1 is a 2–3 week project, immediately CV-ready. Phase 2 is a significantly larger undertaking (a month+) requiring real systems-programming and networking-protocol depth — genuinely a different tier of project, and a strong addition on its own once Phase 1 is solid. Not to be started until Phase 1's command database and translation logic are stable.

---

## Status

- [ ] Phase 1: Scope initial command set
- [ ] Phase 1: Build command database (JSON/CSV)
- [ ] Phase 1: Build tokenizer
- [ ] Phase 1: Build pattern matcher (regex-based)
- [ ] Phase 1: Build translation engine
- [ ] Phase 1: Build CLI driver
- [ ] Phase 1: Test against real Cisco configs
- [ ] Phase 2: SSH proxy — parked for later