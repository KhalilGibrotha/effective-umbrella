---
title: "PowerShell Style Guide (Dev Spaces)"
status: "Draft"
owner: "Server Engineering and Operations"
last_updated: 2025-11-07
version: "1.0"
---

# PowerShell Style Guide (Dev Spaces)

## Purpose
Standardize PowerShell scripts and modules used across automation, CI, and AAP jobs. Aligns with Dev Spaces, GitHub Actions, and pre-commit hooks.

---

## Core Principles
- **Cross-platform first:** Target PowerShell 7+ (`pwsh`), avoid Windows-only APIs unless required.
- **Fail fast:** `Set-StrictMode -Version Latest`; `$ErrorActionPreference = 'Stop'`.
- **Composable:** Functions pipeline-friendly, return objects (not text).
- **Safe by default:** Support `-WhatIf`/`-Confirm` via `SupportsShouldProcess`.
- **Lintable & testable:** PSScriptAnalyzer + Pester are mandatory.

---

## Script & Function Conventions

> WIP