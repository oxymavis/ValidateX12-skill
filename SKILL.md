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

## Where to install this skill

Many agents expect skills under a top-level **`skills/`** directory (each skill is its own subfolder with `SKILL.md` at the root of that subfolder).

**Recommended layout**

```text
skills/
  edi-validate-studio-declarative/   # or any folder name your agent lists
    SKILL.md
    manifest.json
    formats/
    ...
```

Copy the **contents** of this repository into `skills/edi-validate-studio-declarative/` (or rename the folder; keep `SKILL.md` next to `manifest.json` and `formats/`).

**Cursor Desktop** uses the same idea under `~/.cursor/skills/<folder-name>/`. If you use both, you can keep two copies or symlink one to the other.

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
