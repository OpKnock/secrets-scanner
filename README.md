# Secrets Scanner

Secrets scanner for codebases and git repositories, written in Go.

## Overview

The Secrets Scanner is an educational security tool designed to demonstrate secrets detection techniques for codebases and git repositories. This tool helps developers and security teams identify hardcoded secrets, API keys, and other sensitive information in a controlled, educational environment.

**Important:** This tool is intended solely for educational and authorized security testing purposes. Only scan code repositories on systems you own or have explicit written permission to test. Unauthorized scanning of code may violate applicable policies and regulations.

## Features

### 150 Detection Rules

Comprehensive secrets detection covering:

- **Cloud provider credentials**: AWS, GitHub, GitLab, GCP, Azure API keys and secrets
- **Messaging and communication**: Slack, Stripe, Twilio, SendGrid webhook and API credentials
- **Cryptographic keys**: SSH keys, PGP keys, certificate files
- **Authentication tokens**: JWT tokens, OAuth secrets, session secrets
- **Passwords and connection strings**: Database URLs, API endpoints with embedded credentials
- **High-entropy string detection**: Shannon entropy analysis for detecting random secrets (>= 4.0 bits/character)

### Shannon Entropy Analysis

- **Automatic high-randomness string detection**: Identifies strings with entropy >= 4.0 bits/character
- **False positive defense**: Multi-layer filtering to reduce false positives
- **Configurable thresholds**: Adjust entropy threshold per project needs

### Git History Scanning

- **Branch scanning**: Scan specific branches or all branches
- **Depth-limited history**: Control how many commits to scan
- **Date range filtering**: Scan commits within specific time ranges
- **Staged changes detection**: Detect secrets in git staging area

### Output Formats

- **Colored terminal tables**: Interactive review with Rich table display
- **JSON**: Structured output for archival and CI/CD integration
- **SARIF v2.1.0**: GitHub code scanning format for CI/CD pipelines

### 5-Layer False Positive Defense

1. **Keyword pre-filter**: Rapid initial scan to exclude obvious non-secrets
2. **Structural validation**: Validate detected patterns against expected formats
3. **Stopwords**: Filter common words that match patterns but aren't secrets
4. **Allowlists**: Project-specific exclusions for known false positives
5. **Entropy verification**: Shannon entropy check as final validation layer

### Concurrent Pipeline

- **Bounded worker pools**: Controlled parallelism to avoid resource exhaustion
- **Progressive scanning**: Staged scanning from quick checks to deep analysis
- **Performance optimized**: Designed for large codebases with thousands of files

## Installation

### Requirements

- **Go 1.21+**: Go programming language runtime
- **Optional**: `just` command runner (for command execution)

### Build from Source

```bash
# Clone the repository
git clone https://github.com/OpKnock/secrets-scanner.git

# Build the portia command
go install github.com/OpKnock/portia/cmd/portia@latest
```

### Install with just command runner

```bash
# Using curl (recommended)
curl -sSf https://just.systems/install.sh | bash -s -- --to ~/.local/bin

# Or via package manager
# Debian/Ubuntu: apt install just
# Fedora: dnf install just
# macOS: brew install just
```

### Verify Installation

```bash
portia scan --help
just --list
```

## Quick Start

```bash
# Scan current directory
portia scan .
```

### Using just as Command Runner

Type `just` to see all available commands:

| Command | Description |
|---------|-------------|
| `portia scan [path]` | Scan a directory for secrets |
| `portia git [repo]` | Scan git history for secrets |
| `portia init` | Initialize `.portia.toml` configuration |
| `portia pyproject` | Create `pyproject.toml` with `[tool.portia]` config |
| `portia config rules` | List all 150 detection rules |
| `portia config show` | Show active configuration |

## Commands Reference

### `portia scan [path]`

Scan a directory for secrets.

```bash
portia scan .
portia scan ./my-project --format json -o results.json
```

**Flags:**
- `--format` (terminal/json/sarif): Output format
- `--verbose`: Enable debug logging
- `--no-color`: Disable colored output
- `--exclude`: Exclude patterns (glob)
- `--max-size`: Maximum file size to scan
- `--hibp`: Enable HIBP breach verification (offline, your secrets never leave your machine)
- `--config`: Path to configuration file

### `portia git [repo]`

Scan git history for secrets.

```bash
portia git https://github.com/example/repo.git
portia git --branch main --since 2024-01-01
```

**Git flags:**
- `--branch`: Specific branch to scan (default: all branches)
- `--since`: Scan commits since this date (YYYY-MM-DD)
- `--depth`: Maximum commit depth to scan
- `--staged`: Include staged changes in scan

### `portia init`

Initialize `.portia.toml` configuration file in current directory.

```bash
portia init
```

Generates a default configuration with all 150 detection rules enabled.

### `portia pyproject`

Create `pyproject.toml` with `[tool.portia]` configuration for Python projects.

```bash
portia pyproject
```

### `portia config rules`

List all 150 detection rules with descriptions and patterns.

```bash
portia config rules
```

### `portia config show`

Show active configuration settings.

```bash
portia config show
```

## Legal and Ethical Notes

### Authorized Scanning Only

This tool is designed for authorized codebase security scanning. Key principles:

- **Only scan code repositories on systems you own or administer**
- **Obtain explicit written permission** before scanning any production codebase
- **Report any discovered secrets** to the appropriate system owners
- **Never scan code repositories** on systems you do not have explicit authorization for

### Educational Value

Understanding secrets detection helps development teams:

- Identify and remove hardcoded credentials from source code
- Implement secret management best practices (Vault, environment variables, etc.)
- Prevent credential leakage in CI/CD pipelines
- Build more secure code review processes

### Legal Compliance

- Unauthorized codebase scanning may violate Computer Fraud and Abuse Act (CFAA)
- Employee monitoring laws may apply depending on jurisdiction
- Always obtain explicit written permission before testing any codebase

### Secret Handling Best Practices

- **Never commit detected secrets** to any repository
- **Use environment variables or secret management tools** for credential storage
- **Rotate any exposed credentials** immediately after discovery
- **Follow responsible disclosure** for third-party packages or services

## License

AGPL 3.0 - See the LICENSE file for full terms and conditions. This project is provided "as is" without warranty of any kind, either express or implied. AGPL requires that source code be made available to users over a network.