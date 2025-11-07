---
title: "Shell Script Style Guide"
status: "Draft"
owner: "Platform Engineering"
last_updated: 2025-11-06
version: "1.0"
---

# Shell Script Style Guide

## Purpose
Establish safe and consistent shell scripting practices for use in automation, provisioning, and CI environments.

## Core Rules
| Area | Recommendation | Example |
|-------|----------------|----------|
| Interpreter | Always define at top | `#!/usr/bin/env bash` |
| Strict mode | Enforce error handling | `set -euo pipefail` |
| Quoting | Always quote variables | `"${VAR}"` |
| Tests | Use `[[ ]]` instead of `[` | `if [[ -f "$file" ]]; then` |
| Functions | Use lowercase with underscores | `get_system_info()` |
| Comments | One space after `#` | `# This is a comment` |
| Indentation | 2 spaces | consistent with YAML |

## Safety & Portability
- Use `#!/usr/bin/env bash` instead of `/bin/bash` for portability.
- Avoid external commands if built-in alternatives exist.
- Check command return codes explicitly where critical.

## Linting
Use [ShellCheck](https://www.shellcheck.net/) for static analysis.

**Run locally or in CI:**
```bash
shellcheck scripts/*.sh
```


Common ShellCheck Rules:

| ID | Description |
|----|-------------|
| SC2086 | Quote variables to prevent globbing |
| SC2166 | Use `[[` instead of `[` |
| SC2046 | Quote command substitutions |

## References

- https://google.github.io/styleguide/shellguide.html
- https://github.com/koalaman/shellcheck