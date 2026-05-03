# Security policy

## Reporting a vulnerability

**Please do not open a public issue for security vulnerabilities.**

Use one of these private channels:

- **Preferred:** GitHub's private vulnerability reporting —
  <https://github.com/goabonga/multicz-action/security/advisories/new>
- Email: <goabonga@pm.me>

Include:

- A description of the vulnerability and its impact
- Steps to reproduce or a proof-of-concept
- The affected version (`v1.x.x` tag or commit SHA)

You should receive an acknowledgment within 72 hours. The aim is a fix
within 14 days for high-severity issues, coordinated with you before any
public disclosure.

## Supported versions

Only the latest major version receives security fixes. Pin to the
floating major tag (`@v1`) to receive them automatically, or pin to a
SHA for byte-stable supply chain.

## Threat model

This action is a thin wrapper that invokes
[`uv tool install multicz`](https://github.com/goabonga/multicz) — it
runs entirely inside your GitHub Actions runner and writes the multicz
binary to `$PATH`. The shell script in `action.yml` does not download
arbitrary content beyond what `uv` and `pip` resolve from PyPI.

For multicz's own security guarantees and CI hardening recommendations,
see <https://goabonga.github.io/multicz/security/>.
