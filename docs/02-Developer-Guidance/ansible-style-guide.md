---
title: "Ansible Code Style Guide"
status: "Draft"
owner: "Platform Engineering"
last_updated: 2025-11-06
version: "1.0"
---

# Ansible Code Style Guide

## Purpose

Define consistent conventions for authoring, testing, and reviewing Ansible content within the SEO Automation Framework.

## Core Principles

- **Readable:** YAML and playbooks must be human-readable and self-documenting.
- **Idempotent:** Tasks can run repeatedly without altering state unexpectedly.
- **Secure:** Never hardcode secrets; use AAP credentials or CyberArk CCP.
  - [Storing secrets safely](https://docs.github.com/en/get-started/learning-to-code/storing-your-secrets-safely)
- **Reusable:** Prefer modular roles over monolithic playbooks.
- **Traceable:** Every change is version-controlled, linted, and reviewed.

## Naming & Structure

| Element | Convention | Example |
|----------|-------------|----------|
| Variables | `lower_snake_case` | `vm_network_vlan` |
| Roles | `role_<function>` | `role_rhel_patch` |
| Playbooks | `kebab-case.yml` | `windows-upgrade.yml` |
| Tags | Verb-noun | `patch_apply`, `cert_renew` |
| File Layout | Standard Ansible role tree | `tasks/`, `vars/`, `templates/` |

## YAML Standards

- 2-space indentation, UTF-8 encoding, LF line endings.
- Avoid tabs and trailing spaces.
- Always include `---` at top of YAML files.
- Limit line length to 120 characters.

## Required Playbook Header

```yaml
# Authors: {{ author_name }}
# Reviewed By: {{ reviewer_name }}
# Last Reviewed: YYYY-MM-DD
# Summary: Brief purpose and context
```

## Testing & Linting

- Run ansible-lint and yamllint before each PR.
- Use Molecule for reusable roles.
- Ensure success under ansible-navigator run locally before merge.

**Sample .ansible-lint:**
```yaml
skip_list:
  - yaml
warn_list:
  - experimental
parseable: true
verbosity: 1

```

### Linting

### Testing With Molecule

## Version Control
- Use feature branches named `feature/<jira>-short-desc`.
- Include CRQ IDs in merge commits to main.
- All merges require code review by an Architect or Senior Engineer.

## References
- https://docs.github.com/en
- https://ansible.readthedocs.io/projects/lint/
- https://ansible.readthedocs.io/projects/molecule/