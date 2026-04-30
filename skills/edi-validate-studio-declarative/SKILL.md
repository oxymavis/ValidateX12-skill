---
name: edi-validate-studio-declarative
description: >-
  Declarative specification of EDI Validate Studio (YAML, JSON, OpenAPI, JSON
  Schema) — full product capabilities, HTTP contract, profile detection rules,
  workflows, and data shapes without requiring the Python web app source tree
  to be loaded. Use when importing a portable skill kit, generating clients,
  explaining validation architecture, or reimplementing the product in another
  language from specs alone.
---

# EDI Validate Studio — Declarative Skill Kit

This directory is a **standalone knowledge package**. It encodes the same capabilities as the reference Python implementation using **YAML, JSON, OpenAPI 3, and JSON Schema** only. No `.py` files are required for an AI to reason about behavior, generate documentation, or scaffold another stack.

## Where this skill lives (agent sync)

Your agent expects:

```text
{skills_dir}/
  └── edi-validate-studio-declarative/
      └── SKILL.md   ← this file (with manifest.json and formats/ as siblings)
```

**In this repository**, set **`{skills_dir}`** to the folder **`skills/`** at the repo root. This clone already contains `skills/edi-validate-studio-declarative/SKILL.md` — do not point `{skills_dir}` at the repository root unless every skill is a direct child of that root.

**Cursor Desktop** can mirror the same layout under `~/.cursor/skills/edi-validate-studio-declarative/` (copy the whole `edi-validate-studio-declarative` folder).

## Source of truth map

| Concern | File |
|--------|------|
| Package index | `manifest.json` |
| HTTP API | `formats/openapi.yaml` |
| Capability list | `formats/capabilities.yaml` |
| User/API workflow | `formats/workflow.yaml` |
| Built-in profile keywords & IDs | `formats/profiles.yaml` |
| Profile scoring rules | `formats/profile-scoring.yaml` |
| Plugin registry (logical) | `formats/built-in-plugins.json` |
| Upload allow-list & hints | `formats/supported-uploads.json` |
| `ValidationPoint` shape | `formats/schemas/validation-point.schema.json` |
| `ValidationFinding` shape | `formats/schemas/validation-finding.schema.json` |
| `DetectedProfile` shape | `formats/schemas/detected-profile.schema.json` |

## Agent instructions

1. **Explain or implement HTTP integration** — start from `formats/openapi.yaml`; validate request/response bodies with the JSON Schemas in `formats/schemas/`.
2. **Explain profile matching** — combine `formats/profiles.yaml` with `formats/profile-scoring.yaml` (keyword +3, id pattern +5, best score wins, confidence threshold at score ≥ 8 → high).
3. **Explain validation modes** — `generated_spec` (generic over extracted points) vs `built_in_profile` (allow-listed plugin); on plugin exception, fall back per `formats/built-in-plugins.json`.
4. **Reimplement in another language** — treat YAML/JSON/OpenAPI as the contract; heuristic rule extraction is not fully formalized here (reference product uses NLP-style heuristics in code) — declare that gap when users need bit-exact parity.

## Relationship to the Python repo

The `edi_validate_web_app` tree is the **reference runtime**. This skill kit **does not import** it. When users need executable validation, they still deploy or call that implementation (or a reimplementation that conforms to these specs).
