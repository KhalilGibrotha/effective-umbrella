---
title: "Python Style Guide"
status: "Draft"
owner: "Server Engineering and Operations"
last_updated: 2025-11-06
version: "1.0"
---

# Python Style Guide

## Purpose
Provide consistent standards for Python scripts and automation utilities used in conjunction with Ansible and platform management.

## General Principles
- Follow **PEP 8** for syntax and structure.
- Code must be **readable, modular, and testable**.
- Prefer explicit imports over wildcard (`from x import *`).
- Use type hints for clarity and reliability.

## Formatting
| Rule | Tool |
|------|------|
| 4-space indentation | Black |
| Max line length 88 | Black |
| Double quotes for strings | Black |
| Group imports (stdlib, third-party, local) | isort |

**Example `pyproject.toml`:**
```toml
[tool.black]
line-length = 88
target-version = ['py39']

[tool.flake8]
max-line-length = 88
ignore = E203, W503
extend-ignore = E501

[tool.isort]
profile = "black"
```

## Linting & Testing

- Use flake8 and black for style and correctness.

- Run pytest for all modules containing functions.

- Use docstrings (Google format preferred).

- Add logging, not print statements, for output.

## Documentation

Include module docstrings:
```python
"""
Module for RHEL patch compliance verification.
Author: {{ author_name }}
Last Updated: YYYY-MM-DD
"""
```

## References

- https://peps.python.org/pep-0008/
- https://black.readthedocs.io/en/stable/
- https://flake8.pycqa.org/en/latest/
- https://pycqa.github.io/isort/