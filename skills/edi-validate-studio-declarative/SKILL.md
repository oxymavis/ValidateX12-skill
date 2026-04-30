---
name: edi-validate-studio-runtime
description: >-
  Runnable EDI Validate Studio skill package with executable CLI
  (scripts/validate_cli.py), plus YAML/JSON/OpenAPI contracts. Use when an
  agent must run real validation from spec files + EDI text in shell mode,
  while still exposing documented API/schema references.
---

# EDI Validate Studio — Runnable Skill Kit

This directory is a **standalone runnable skill package**. It contains:

- executable CLI: `scripts/validate_cli.py`
- contracts: YAML / JSON / OpenAPI / JSON Schema

The CLI runs real validation logic by loading a local **runtime repo** (Validation EDI) passed via `--runtime-repo` or `VALIDATION_EDI_RUNTIME_REPO`.

## Where this skill lives (agent sync)

Your agent expects:

```text
{skills_dir}/
  └── edi-validate-studio-declarative/
      └── SKILL.md   ← this file (with manifest.json and formats/ as siblings)
```

**In this repository**, set **`{skills_dir}`** to the folder **`skills/`** at the repo root. This clone already contains `skills/edi-validate-studio-declarative/SKILL.md` — do not point `{skills_dir}` at the repository root unless every skill is a direct child of that root.

**Cursor Desktop** can mirror the same layout under `~/.cursor/skills/edi-validate-studio-declarative/` (copy the whole `edi-validate-studio-declarative` folder).


## Runnable CLI (real validation)

```bash
python skills/edi-validate-studio-declarative/scripts/validate_cli.py \
  --runtime-repo "/path/to/Validation EDI" \
  --spec ./customer-spec.pdf \
  --edi ./message.edi
```

- `--runtime-repo`: path containing `edi_validate_web_app/` and parent-level validator modules.
- `--spec`: repeatable; supports PDF/DOCX/XLS/XLSX/TXT/MD based on runtime parser support.
- `--edi`: file path, or `-` for stdin.
- Output: JSON (`summary`, `findings`, `validationMode`, `fallback`, etc.).

This gives executable behavior without hosting a public web app.

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

The `edi_validate_web_app` tree is the **runtime dependency** for the CLI. This skill repo contains the runnable entrypoint, while the runtime repo provides parser/validator implementation modules.
