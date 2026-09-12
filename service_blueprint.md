# AEXYR CLI — Service Blueprint

**Project:** AEXYR CLI (`axr`)  
**Repo:** [axonstellar/aexyr-cli](https://github.com/axonstellar/aexyr-cli)  
**Version:** 0.1.0 (planning phase)  
**Status:** Pre-development — architecture and specification  
**Last Updated:** 2026-09-11  

---

## 1. Overview

AEXYR CLI (`axr`) is the command-line interface for the AEXYR autonomous agent platform. It enables users to interact with their AEXYR instance directly from the terminal — build services, manage infrastructure, and deploy to the internet through natural language conversation.

Think `kubectl` for AEXYR — a thin client that gives terminal-native access to the full AxonStellar orchestration layer.

## 2. Product Context

AEXYR CLI is part of the **Product Trilogy**:

| Interface | User | Use Case |
|---|---|---|
| **Web UI** | Anyone with a browser | Quick access, mobile, sharing dashboards |
| **AEXYR CLI** | Power users, DevOps, CI/CD | Automation, scripting, terminal-native workflows |
| **AEXYR Desktop** | Daily operators, teams | Mission control, multi-instance management |

All three share the same APIs, same agent, same infrastructure — three interfaces for different contexts.

## 3. Architecture

### 3.1 Connection Model

**Primary:** Cloudflare Tunnel SSH (chosen architecture)

```
      Cloudflare       ┌──────────────────────┐
  User's      │      Tunnel           │  AEXYR Container     │
  Terminal    │ ◄──────────────────►   │  cloudflared → sshd  │
  axr commands│   Zero open ports     │  sshd → localhost:80 │
              │                        │  Flask API           │
                       └──────────────────────┘
```

**How it works:**
- `cloudflared` inside the AEXYR container routes `ssh.domain.com → localhost:22`
- User connects via `cloudflared access ssh` on their local machine
- Port 22 is NOT mapped to the Docker host — all SSH flows through the CF tunnel
- The CF tunnel is already created upon first SSL certificate provisioning in AEXYR

**Development phase (Architecture B):** For v0.x, the CLI runs locally on the server via SSH session, talking to `localhost:80` directly. CF Tunnel SSH is the target for v1.

### 3.2 Authentication — Three Layers

| Layer | Mechanism | Purpose |
|---|---|---|
| **1. Cloudflare Access** | SSO, email OTP, mTLS, IP allowlists | Identity verification at the edge |
| **2. SSH Key Auth** | Ed25519/RSA keypair | Cryptographic session authentication |
| **3. API Token** | Internal API token (`/aexyr/tmp/.internal_api_key`) | Flask API authorization |

### 3.3 Security Advantages

- Zero open ports — no port to scan, no IP exposed
- Double encryption — CF tunnel + SSH
- Cloudflare absorbs DDoS
- Cloudflare Access provides audit logging, geo/IP restrictions
- Private keys never leave the user's machine
- Zero Trust architecture

## 4. Command Surface

### 4.1 Agent Interaction
```bash
axr chat "build me a REST API with PostgreSQL"    # Conversational building
axr ask "what services are running?"               # Quick queries
axr task create --schedule "0 3 * * *" "run scan"  # Scheduled tasks
axr task list                                       # List tasks
```

### 4.2 Service Management
```bash
axr services list              # List all services (Server Rack view)
axr services restart 9551      # Restart by port
axr logs <service> --tail 50   # Stream service logs
axr health                     # Full constellation health scan
axr status                     # Quick status overview
```

### 4.3 SSL & Domains
```bash
axr ssl provision api.example.com           # Provision SSL certificate
axr ssl assign api.example.com 9553         # Bind domain to service
axr ssl status                              # Certificate status overview
```

### 4.4 Infrastructure
```bash
axr topology              # Print network topology (ASCII/JSON)
axr topology --json       # Machine-readable output
```

### 4.5 Knowledge & Memory
```bash
axr cortex search "nginx configuration"     # Search knowledge base
axr cortex export > knowledge.json          # Export knowledge
axr memory list                             # List memories
axr memory save "production DB rotated"     # Save a memory
```

### 4.6 Development Workflow
```bash
axr build                 # Build Docker image locally
axr build --push          # Build + push to GHCR
axr release 1.4.2         # Full release pipeline
axr dev                   # Start local dev instance
```

### 4.7 Multi-Instance Fleet (Future — v2+)
```bash
axr connect prod@server1.example.com        # Connect to instance
axr fleet status                            # Health across fleet
axr fleet deploy 1.4.2                      # Rolling update
```

## 5. Technology Stack

| Component | Technology | Rationale |
|---|---|---|
| **Language** | Node.js (TypeScript) | Aligns with AEXYR ecosystem, npm distribution |
| **CLI Framework** | TBD (Commander.js, oclif, or yargs) | Structured command parsing |
| **SSH Integration** | Wraps `cloudflared access ssh` | Transparent tunnel management |
| **Output** | TTY-aware (colors, tables, spinners) | Rich terminal experience |
| **Config** | `~/.axr/config.json` | Instance profiles, auth tokens |
| **Package** | `@axonstellar/axr-cli` | npm scoped package |

## 6. Distribution

### Phase 1 — npm (v1)
```bash
npm install -g @axonstellar/axr-cli
```
- Published to npmjs.com under `@axonstellar` scope
- Requires Node.js 18+ on user's machine
- Action item: Claim `@axonstellar` scope on npmjs.com

### Phase 2 — Binary releases (v2)
- Pre-compiled standalone binaries via `pkg` or `nexe` (Node.js → binary)
- Attached to GitHub Releases
- No Node.js required on user's machine
- Platforms: macOS (aarch64 + x64), Windows (x64), Linux (x64)

### Potential Phase 3 — Bundled in AEXYR container
- `axr` command available inside the container out-of-the-box
- Users SSH in and `axr` is already there

## 7. Configuration

```json
// ~/.axr/config.json
{
  "default_instance": "prod",
  "instances": {
    "prod": {
      "host": "ssh.example.com",
      "auth": "cloudflare",
      "user": "aexyr-user"
    },
    "dev": {
      "host": "ssh.dev.example.com",
      "auth": "cloudflare",
      "user": "aexyr-user"
    }
  }
}
```

## 8. End-User Experience Flow

1. Install: `npm install -g @axonstellar/axr-cli`
2. Connect: `axr connect ssh.myserver.example.com` → browser opens for CF Access auth
3. Build: `axr chat "build me a REST API for task management with PostgreSQL"`
4. Agent scaffolds project, provisions DB, deploys, provisions SSL
5. Result: `https://api.myserver.example.com` is live
6. Iterate: `axr chat "add rate limiting"` / `axr logs task-api --tail 50`

## 9. File Structure (Planned)

```
aexyr-cli/
 src/
   ├── index.ts                 # Entry point
   ├── commands/                # Command implementations
   │   ├── chat.ts
   │   ├── services.ts
   │   ├── ssl.ts
   │   ├── topology.ts
   │   ├── logs.ts
   │   ├── health.ts
   │   ├── task.ts
   │   ├── cortex.ts
   │   ├── memory.ts
   │   ├── connect.ts
   │   └── fleet.ts
   ├── lib/
   │   ├── api-client.ts        # HTTP client for AEXYR API
   │   ├── ssh-tunnel.ts        # cloudflared SSH wrapper
   │   ├── config.ts            # Config file management
   │   ├── auth.ts              # Authentication flow
   │   └── output.ts            # TTY-aware formatting
   └── types/
       └── index.ts             # TypeScript type definitions
 bin/
   └── axr                      # CLI entry script
 tests/
 package.json
 tsconfig.json
 README.md
 LICENSE
 service_blueprint.md
 service_changelog.md
 service_pitfalls.md
```

## 10. Roadmap

| Phase | Version | Milestone |
|---|---|---|
| **Planning** | 0.1.0 | Architecture, spec, command design (current) |
| **Scaffold** | 0.2.0 | Project setup, TypeScript, CLI framework, basic structure |
| **Local MVP** | 0.3.0 | Architecture B — CLI on server, core commands (chat, services, logs, health) |
| **API Integration** | 0.4.0 | Full command surface against AEXYR Flask API |
| **CF Tunnel SSH** | 0.5.0 | cloudflared integration, remote connection |
| **Auth** | 0.6.0 | CF Access flow, SSH key management, config profiles |
| **Polish** | 0.9.0 | Output formatting, error handling, help text, tests |
| **v1 Release** | 1.0.0 | npm publish, README, documentation |
| **Binary** | 1.1.0 | Standalone binary distribution via GitHub Releases |
| **Fleet** | 2.0.0 | Multi-instance management |

## 11. Dependencies on AEXYR Platform

The CLI consumes existing AEXYR APIs — no new backend needed for v1:

| CLI Command | AEXYR API Endpoint |
|---|---|
| `axr chat` | Agent chat WebSocket/HTTP |
| `axr services` | `GET /manifests_list`, `POST /visualizer_service_control` |
| `axr logs` | `GET /api_node_logs` |
| `axr health` | `GET /visualizer_vitals` |
| `axr topology` | `GET /api_topology` |
| `axr ssl` | `POST /api_certificates`, `POST /api_assignments` |
| `axr task` | `POST /scheduler_task_create`, `POST /scheduler_tasks_list` |
| `axr status` | `GET /api_ops_system`, `GET /api_ops_network` |
