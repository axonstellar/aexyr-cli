<div align="center">

# Æxyr CLI

**Terminal-native interface for your AEXYR infrastructure**

*Manage services, deployments, SSL certificates, and agent conversations — all from the command line.*

![Status](https://img.shields.io/badge/Status-Coming_Soon-yellow)
![Platform](https://img.shields.io/badge/Platform-macOS_%7C_Linux_%7C_Windows-4A154B)
![License](https://img.shields.io/badge/License-MIT-green)
![Node](https://img.shields.io/badge/Node.js-20%2B-339933?logo=node.js&logoColor=white)

---

**[AEXYR](https://github.com/axonstellar/AEXYR)** · **[AEXYR Desktop](https://github.com/axonstellar/aexyr-desktop)** · **[AxonStellar](https://axonstellar.com)**

</div>

---

## What is AEXYR CLI?

AEXYR CLI (`axr`) is the command-line interface for managing [AEXYR](https://github.com/axonstellar/AEXYR) instances remotely. It brings the full power of the AEXYR platform to your terminal — no browser required.

Connect to your AEXYR server securely, manage your service constellation, interact with the agent, and orchestrate infrastructure from any terminal on any machine.

```bash
# Connect to your AEXYR instance
axr connect my-server

# Check what's running
axr services list

# Talk to the agent
axr chat "Deploy a Node.js API on port 9555 with SSL"

# Manage SSL certificates
axr ssl provision api.example.com
axr ssl assign api.example.com --port 9555

# Monitor your constellation
axr vitals
axr topology
```

---

## Planned Features

### 🔌 Secure Remote Connection
Connect to any AEXYR instance over an encrypted tunnel. Zero open ports required — leverages Cloudflare Tunnel for secure access without firewall configuration or port forwarding.

### 💬 Agent Interaction
Converse with your AEXYR agent directly from the terminal. Send tasks, receive responses, and watch execution in real time. Supports both interactive chat and single-command execution.

### 🖥️ Service Management
List, start, stop, and restart services in your constellation. View health status, inspect logs, and manage the full service lifecycle without opening a browser.

### 🔒 SSL & Domain Management
Provision SSL certificates, assign domains to services, and manage your certificate inventory — all from the command line.

### 📊 Monitoring
Stream system vitals, view network topology, and tail service logs. Get real-time visibility into your infrastructure from any terminal.

### ⚡ Workflow Automation
Execute Engram playbooks, trigger scheduled tasks, and compose multi-step operations into scriptable workflows. Integrate AEXYR into your existing CI/CD pipelines and shell scripts.

### 🌐 Multi-Instance Management
Manage multiple AEXYR servers from a single CLI installation. Switch between instances with named profiles.

---

## Installation

> **Coming soon.** The CLI is currently in development.

```bash
# npm (planned)
npm install -g @axonstellar/axr-cli

# Verify installation
axr --version
```

---

## Usage

> **Coming soon.** The following shows planned command structure.

```bash
# First-time setup
axr init
axr connect add my-server --host aexyr.example.com

# Service operations
axr services list                    # List all services
axr services logs my-app --follow    # Tail logs
axr services restart --port 9555     # Restart by port

# Agent interaction
axr chat "What services are running?"           # One-shot command
axr chat --interactive                          # Interactive session
axr chat "Fix the failing health check on 9553" # Task delegation

# SSL management
axr ssl list                                    # View certificates
axr ssl provision app.example.com               # Provision new cert
axr ssl assign app.example.com --port 9555      # Bind to service

# System monitoring
axr vitals                     # System resources
axr topology                   # Service constellation
axr ops                        # Operations overview

# Workflow execution
axr engram run health-scan     # Execute a playbook
axr tasks list                 # View scheduled tasks
```

---

## The AEXYR Ecosystem

AEXYR CLI is part of the AEXYR product family — three interfaces to the same powerful infrastructure:

| Product | Interface | Status |
|---|---|---|
| **[AEXYR](https://github.com/axonstellar/AEXYR)** | Web UI — full-featured browser dashboard | ✅ Available |
| **[AEXYR CLI](https://github.com/axonstellar/aexyr-cli)** | Terminal — scriptable command-line interface | 🔜 Coming Soon |
| **[AEXYR Desktop](https://github.com/axonstellar/aexyr-desktop)** | Desktop — native multi-instance mission control | 🔜 Coming Soon |

All three share the same APIs and connect to the same AEXYR server. Use whichever interface fits your workflow — or use all three.

---

## Requirements

- **Node.js** 20 or later
- **An AEXYR instance** to connect to ([get started here](https://github.com/axonstellar/AEXYR))

---

## License

AEXYR CLI is open-source software released under the [MIT License](LICENSE).

The AEXYR platform itself is proprietary software by **AxonStellar LLC**. See the [AEXYR repository](https://github.com/axonstellar/AEXYR) for platform licensing details.

---

<div align="center">

*Your infrastructure, your terminal, your control.* ⚡

**Built by [AxonStellar](https://axonstellar.com)**

</div>
