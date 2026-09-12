# AEXYR CLI — Changelog

All notable changes to this project will be documented in this file.

---

## [v0.1.0] — 2026-09-11 — Project inception and architecture planning
- Created: Initial project documentation (blueprint, changelog, pitfalls)
- Defined: CLI command surface (agent, services, SSL, infrastructure, knowledge, fleet)
- Decided: Cloudflare Tunnel SSH as primary connection architecture
- Decided: Three-layer authentication model (CF Access → SSH keys → API token)
- Decided: Node.js/TypeScript stack with npm distribution
- Decided: Tauri for desktop counterpart (aexyr-desktop)
- Created: GitHub repo axonstellar/aexyr-cli (public, Coming Soon README)
- Status: Pre-development — architecture and specification phase
- Files: service_blueprint.md, service_changelog.md, service_pitfalls.md
