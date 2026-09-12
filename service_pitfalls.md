# AEXYR CLI — Pitfalls & Lessons Learned

Known issues, design considerations, and things to watch out for.

---

## Architecture & Design

### 1. Æ Character Rendering
**Issue:** The Æ (ash) character renders correctly in GitHub markdown headings and links but disappears in regular paragraph body text on some browsers/fonts.  
**Fix:** Use HTML entity `&#198;` in body text, or use plain `AEXYR` in body copy and reserve Æ for titles.  
**Date:** 2026-09-11

### 2. Empty GitHub Repo Initialization
**Issue:** GitHub's Contents API and Git Data API both reject operations on completely empty repos (no branches, no commits). Contents API returns 403 "Repository has been locked", Git Data API returns 409 "Git Repository is empty".  
**Fix:** Initialize empty repos by cloning locally, committing, and pushing via git CLI. Cannot use API-only approach on empty repos.  
**Date:** 2026-09-11

### 3. Port 22 Docker Mapping
**Issue:** Port 22 is intentionally NOT mapped to the Docker host in the compose file. SSH access is routed through the Cloudflare Tunnel internally.  
**Consideration:** For Architecture B (local development phase), the CLI runs inside the SSH session on the server, talking to localhost:80. No port 22 exposure needed for either architecture.  
**Date:** 2026-09-11

### 4. npm Scope Availability
**Issue:** The `@axonstellar` scope on npmjs.com needs to be claimed before publishing.  
**Action:** Register the scope before v1 release. Scoped names can be taken by others.  
**Date:** 2026-09-11

## Security

### 5. Token Exposure in CLI
**Consideration:** The CLI will handle API tokens and SSH credentials. Never log, display, or transmit tokens in plain text. Store in `~/.axr/config.json` with restricted file permissions (0600). Never include tokens in error messages or debug output.  
**Date:** 2026-09-11

### 6. cloudflared Dependency
**Consideration:** CF Tunnel SSH requires `cloudflared` installed on the user's local machine. The CLI should detect if cloudflared is present and provide clear installation instructions if missing. Consider bundling or auto-installing cloudflared.  
**Date:** 2026-09-11
