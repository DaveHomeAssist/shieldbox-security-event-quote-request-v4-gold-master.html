> **DEPRECATED** — This file is superseded by `CLAUDE.md`. Issues, session log, and project metadata now live in CLAUDE.md. This file is retained as a historical archive only.

# AGENTS.md

Inherits root rules from `/Users/daverobertson/Desktop/Code/AGENTS.md`.

## Project Overview

Static mockup of an event security quote intake portal for ShieldBox Security (Philadelphia). Users configure event details (type, size, billing, scope) and receive a quote estimate. The v4 gold master is the final polished version. The archive/ directory contains previous iterations: v0 original, v1 gemini, v2 codex, v3 claude.

## Stack

- Static HTML + inline CSS + inline JS (single file index.html)
- Three theme variants: default dark, editorial (warm light), slate
- Token based design system documented in SCHEMA.md
- GitHub Pages hosting
- No build step, no bundler, no framework

## Key Decisions

- Single file architecture; all form logic, pricing, and styles inline
- Multi theme support via body[data-theme] attribute and CSS custom property overrides
- Full design token contract: components reference tokens, no hardcoded colors
- Archive directory preserves the build evolution across different AI tools

## Issue Tracker

| ID | Severity | Status | Title | Notes |
|----|----------|--------|-------|-------|
| 001 | P1 | open | Checkbox inputs lack name attributes | Cannot serialize as form data; state is purely visual |
| 002 | P1 | open | No quote calculation engine | Billing rates display but no JS computes a total estimate |
| 003 | P1 | open | Event duration field accepts any free text with no validation | No pattern or type enforcement on critical scheduling field |
| 004 | P1 | open | Attendance input has no max bound | Users can enter 999999999 with no feedback |
| 005 | P2 | open | Color contrast fails WCAG AA on editorial, slate, obsidian, and concrete themes | ink-soft values too close to field-bg in dark variants |
| 006 | P2 | open | Validation only runs on explicit button click, not on submit | Users can bypass validation entirely |
| 007 | P2 | open | Risk assessment grid is static despite auto-assessed label | Attendance and scope changes do not update risk levels |
| 008 | P2 | open | Billing row selection not keyboard accessible | Click handler only; no keydown or focus delegation |

## Session Log

[2026-03-18] [ShieldBox] [docs] Add AGENTS baseline
