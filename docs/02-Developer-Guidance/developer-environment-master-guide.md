---
title: "Developer Environment Master Guide"
status: "Draft"
owner: "Server Engineering and Operations"
last_updated: 2025-11-07
version: "1.0"
---

# Developer Environment Master Guide

< Note: This setup would utilize one devspace to rule them all. Working on a POC to demo.

This guide consolidates **all setup instructions and standards** for the SEO Automation Framework developer lab — including **OpenShift Dev Spaces**, **VS Code**, **pre-commit**, and the shared configuration files (`pyproject.toml`, `.pre-commit-config.yml`, `.ansible-lint`, `.markdownlint.yaml`, etc.).  

It ensures everyone’s environment behaves the same way whether you code locally, inside Dev Spaces, or in CI.

---

## Overview

| Component | Purpose |
|------------|----------|
| **Dev Spaces / VS Code** | Primary development IDEs |
| **Virtual env (.venv)** | Isolated Python environment |
| **Pre-commit hooks** | Enforce linting before commits |
| **Tool configs** | Shared across IDE and CI |
| **Containerfile or devfile** | Reproduce lab workspace image |

---

## Workspace Initialization (Python + Lint Stack)

1. **Clone repo and open in Dev Spaces or VS Code.**  
2. **Create and activate venv**
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
3. Install dev dependencies
   > WIP
4. Install and enable pre-commit
   > WIP
5. Run manual QA if needed
   > WIP

## Standard Config Files

> WIP

## Dev Spaces Integration

> WIP

### Option B - Containerfile baked image

## VS Code Configuration

> WIP

## Recommended Extensions

> WIP

## Vim / Neovim Alternative

## QA pipleline Flow

> WIP