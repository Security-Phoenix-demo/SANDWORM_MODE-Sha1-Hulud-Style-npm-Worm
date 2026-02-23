# Structured Security Campaign Report

## SANDWORM_MODE

Audience: Threat Intelligence / Senior Security Engineering

---

## 1. Campaign Overview

SANDWORM_MODE is a typosquatting-driven npm supply chain worm that executes on import, harvests secrets from developer and CI environments, and propagates by injecting dependencies and CI workflows into accessible GitHub repositories. It escalates beyond typical npm credential stealers by introducing AI toolchain poisoning through MCP server injection, designed to coerce secret collection via assistant integrations. The malware employs multi-channel exfiltration over HTTPS with DNS tunneling fallback and establishes persistence through git hook template manipulation, enabling reinfection of future repositories. Public reporting identified at least 19 malicious npm packages under two publisher aliases prior to takedown.

---

## 2. Attack Vector Breakdown

### Initial Access

- Typosquatted npm packages impersonating trusted developer utilities and AI tooling.
- Payload executes during normal import without requiring postinstall hooks.

### Privilege Escalation / Account Takeover

- Harvests credentials from:
  - `.npmrc`
  - Environment variables
  - CI secret contexts
- Uses stolen npm and GitHub tokens to publish packages and write to repositories.

### Payload Delivery / Injection

- Obfuscated JavaScript loaders:
  - Base64-encoded blobs
  - zlib inflate
  - XOR decryption
  - Indirect `eval`
  - Chunked loader reconstruction
  - `Module._compile` execution
- Stage 1 loads in-memory.
- Stage 2 decrypted via AES-256-GCM after time gate.
- CI environments bypass delay and execute immediately.

### Self-Propagation / Lateral Movement

- Enumerates repositories using stolen GitHub tokens.
- Injects a malicious "carrier" dependency.
- Modifies lockfiles.
- Injects CI workflows to harvest additional secrets.
- Implements SSH-assisted fallback propagation.
- Persists through git hook template abuse.

---

## 3. Payload Characteristics

### Artifact Profile

- JavaScript malware embedded in npm packages.
- Obfuscated loaders with runtime decoding.
- At least one variant embeds a large base64 blob inflated and executed via `eval`.

### Execution Trigger

- Executes automatically on module import.
- Preserves expected package functionality to reduce detection.

### Reconnaissance

- Profiles environment and system signals.
- Detects CI environments and triggers immediate execution path.

### Credential Harvesting Targets

- npm publish tokens
- GitHub tokens
- `.npmrc` credentials
- CI environment secrets
- Cloud provider credentials
- SSH keys
- LLM provider API keys
- Other environment variables containing KEY, SECRET, TOKEN, PASSWORD

### Persistence Mechanisms

- Git hook template abuse:
  - `git config --global init.templateDir`
  - Malicious hooks placed in `~/.git-templates/hooks/`
- Injected CI workflows into `.github/workflows/`
- Potential release pipeline modification

### Exfiltration Methods

- HTTPS POST to Cloudflare Worker endpoints
- GitHub API uploads to attacker-controlled repositories
- DNS tunneling to:
  - `freefan.net`
  - `fanfree.net`

### Crypto / Loader Artifacts

- AES-256-GCM decryption of Stage 2 payload
- Published IV and authentication tag values
- Known Stage 2 plaintext SHA-256 hash
- Embedded bearer token for early "drain" exfil endpoint

---

## 4. Indicators of Compromise (IOCs)

| Category | Indicator | Notes |
| --- | --- | --- |
| Malicious Package | claud-code@0.2.1 | Typosquat |
| Malicious Package | cloude-code@0.2.1 | Typosquat |
| Malicious Package | cloude@0.3.0 | Typosquat |
| Malicious Package | crypto-locale@1.0.0 | Malicious |
| Malicious Package | crypto-reader-info@1.0.0 | Malicious |
| Malicious Package | detect-cache@1.0.0 | Malicious |
| Malicious Package | format-defaults@1.0.0 | Malicious |
| Malicious Package | hardhta@1.0.0 | Malicious |
| Malicious Package | locale-loader-pro@1.0.0 | Malicious |
| Malicious Package | naniod@1.0.0 | Malicious |
| Malicious Package | node-native-bridge@1.0.0 | Malicious |
| Malicious Package | opencraw@2026.2.17 | Malicious |
| Malicious Package | parse-compat@1.0.0 | Malicious |
| Malicious Package | rimarf@1.0.0 | Malicious |
| Malicious Package | scan-store@1.0.0 | Malicious |
| Malicious Package | secp256@1.0.0 | Malicious |
| Malicious Package | suport-color@1.0.1 | Representative example |
| Malicious Package | veim@2.46.2 | Malicious |
| Malicious Package | yarsg@18.0.1 | Malicious |
| Network | `https://pkg-metrics.official334.workers.dev/exfil` | HTTPS exfil endpoint |
| Network | `https://pkg-metrics.official334.workers.dev/drain` | Drain endpoint |
| Network | freefan.net | DNS exfil domain |
| Network | fanfree.net | DNS exfil domain |
| Host Artifact | .cache/manifest.cjs | Payload hiding path |
| Host Artifact | `/dev/shm/.node_<hex>.js` | Transient Stage 2 load path |
| Persistence | git config --global init.templateDir | Git hook template persistence |
| Crypto Hash | 5440e1a424631192dff1162eebc8af5dc2389e3d3b23bd26e9c012279ae116e4 | Stage 2 plaintext SHA-256 |

---

## 5. Blast Radius and Impact Assessment

### Confirmed Footprint

- At least 19 malicious npm packages.
- Two publisher aliases involved.

### Downstream Exposure

- High risk of intra-organization propagation after initial foothold.
- CI workflow poisoning can extend compromise to multiple repositories.

### Data at Risk

- npm publish tokens
- GitHub tokens and repo write permissions
- CI secret stores
- Cloud credentials
- SSH keys
- LLM provider API keys

### Persistence Risk Post-Remediation

- Removing the malicious package alone is insufficient.
- Git hooks and injected workflows must be removed.
- Developer endpoints and CI runners require validation and cleanup.

---

## 6. Differentiation From Prior Attacks

- AI toolchain poisoning via MCP injection represents escalation beyond standard npm stealers.
- Resilient exfiltration architecture combining HTTPS and DNS fallback.
- Two-stage execution model with time gating and CI fast-path.
- Git template persistence increases reinfection likelihood across future repositories.

---

## 7. Recommended Detections and Mitigations

### Detection Recommendations

#### Network

Alert on outbound connections or DNS queries to:

- pkg-metrics.official334.workers.dev
- freefan.net
- fanfree.net

#### Host

Detect:

- New or modified `git config --global init.templateDir`
- Executable scripts under:
  - `~/.git-templates/hooks/`
  - `.git/hooks/`
  - `.husky/`
- Creation or access of `.cache/manifest.cjs`

#### Repository

Monitor for:

- Unexpected changes in `.github/workflows/`
- New workflows using `pull_request_target`
- Suspicious automated dependency injection

### Mitigation Recommendations

#### Dependency Controls

- Blocklist the 19 malicious package names at registry proxy or firewall layer.
- Enforce dependency allowlists in CI environments.

#### Secret Hygiene

- Rotate all npm, GitHub, CI, cloud, SSH, and LLM provider keys if exposure suspected.
- Replace long-lived tokens with scoped, short-lived credentials.

#### CI Hardening

- Require review for workflow changes.
- Restrict which workflows can access secrets.
- Lock down default branch protection and disable auto-merge for dependency PRs.

#### Endpoint Hardening

- Monitor and lock global git configuration in managed developer environments.
- Audit git hook templates regularly.

#### AI Assistant Controls

- Baseline assistant configuration files.
- Alert on unauthorized MCP server additions.
- Treat unapproved MCP servers as high-risk software.

---

## Reference

Phoenix Security Research, original discovery: Socker Research team  
Date: February 22, 2026
