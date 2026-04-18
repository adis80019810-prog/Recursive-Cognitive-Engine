# Contributing to Recursive Cognitive Engine

Thank you for your interest in improving RCE!

## How to Contribute

1. **Fork** the repository.
2. **Create a feature branch** (`git checkout -b feature/amazing-improvement`).
3. **Make your changes** and test them in your own n8n instance.
4. **Update documentation** if you change behaviour or add environment variables.
5. **Submit a Pull Request** with a clear description of the change.

## Code Style

- Keep JavaScript code in n8n Code nodes **self‑contained** (no external npm packages).
- Use `try/catch` for all file I/O operations.
- Follow the existing security patterns (path containment, input sanitisation).

## Reporting Issues

Use the GitHub Issues tracker. Please include:
- n8n version
- Node.js version
- Relevant error logs from `04_Meta/error_log.md`
- Steps to reproduce

## Security Disclosures

If you discover a security vulnerability, **do not open a public issue**. Email [adis80019810@gmail.com] with details.
