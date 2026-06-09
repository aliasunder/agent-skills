# Security Policy

## Scope

This repository contains agent skills — markdown skill files and shell-based
CI workflows. There is no runtime server or
compiled application code.

Security concerns here typically involve:

- CI workflow vulnerabilities (GitHub Actions)
- Secrets or credentials accidentally committed
- Malicious content injected into skill/command markdown

## Reporting a Vulnerability

If you discover a security issue, please report it through
[GitHub's private vulnerability reporting](https://github.com/aliasunder/agent-skills/security/advisories/new)
rather than opening a public issue.

Please include:

- A description of the vulnerability
- Steps to reproduce or a proof of concept
- The potential impact

You should receive an acknowledgment within **48 hours**. I'll work with you to
understand the issue and coordinate a fix before any public disclosure.

## Supported Versions

Only the latest release is actively maintained. If you're using an older version,
please upgrade before reporting.

| Version | Supported |
|---------|-----------|
| Latest  | Yes       |
| Older   | No        |
