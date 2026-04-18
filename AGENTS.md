# AGENTS.md — repo-specific agent instructions

Purpose
- Short, high-signal guidance so an automated agent (or a human using an agent) can work safely and efficiently in this repository.

Top-level rule
- Regardless of the language the user asks in, agents MUST reply in English. (This repo-level policy is intentional.)

Repository layout (what matters)
- Each subfolder under this repo is a "skill" package. Key examples present now:
  - plan-before-build/SKILL.md
  - skill-creator/SKILL.md
- Skill folders may include `scripts/`, `references/`, `evals/`, and `eval-viewer/`.

Primary source of truth
- Always read the SKILL.md file in a skill directory first. SKILL.md contains the canonical frontmatter (name, description) and operational commands the skill expects.

Exact commands and verification shortcuts (do not guess)
- Required tools: python (3.x) and git. Do NOT assume node/npm, cargo, or other runtimes are present unless a skill explicitly documents them.
- Run aggregate benchmark (from skill-creator):
  - python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>
- Generate review viewer:
  - nohup python <skill-path>/eval-viewer/generate_review.py <workspace>/iteration-N --skill-name "my-skill" --benchmark <workspace>/iteration-N/benchmark.json &
- Package a skill (if available):
  - python -m scripts.package_skill <path/to/skill-folder>

Conventions agents must follow
- Preserve SKILL.md frontmatter `name` field. When editing or packaging a skill, keep the original name unless user asks to rename.
- Keep SKILL.md under ~500 lines when possible. If a skill needs more content, place large references under `references/` and point to them from SKILL.md.
- When running evals, spawn with-skill and baseline runs in parallel (if platform supports subagents). Save outputs under a workspace/iteration-X directory as described in SKILL.md.

What not to do
- Do not invent developer commands. If a command or workflow is not present in SKILL.md or scripts/, do not assume one exists.
- Do not create git commits or push to remote without explicit user instruction.

Where to look next (high-value files)
- Any SKILL.md inside a top-level folder
- skill-creator/scripts/ — packaging, run_loop, aggregate and viewer helpers
- plan-before-build/SKILL.md — example skill that requires English-only responses and documents compatibility: python + git

If something is missing
- Ask exactly one short question (use the in-conversation question tool) only if the repo cannot answer a required operational detail (for example: which branch to push to, or who owns a skill). Prefer reading SKILL.md and scripts first.

Minimal checklist for an agent new to this repo
1. Read <skill>/SKILL.md
2. Inspect <skill>/scripts/ for runnable helpers
3. Confirm required tools (python, git) are available
4. Run the documented verification command (e.g., generate_review.py or scripts.aggregate_benchmark)
5. Report results in English and include exact command outputs when relevant

This file is intentionally concise. Keep repo-specific items here only — do not add generic LLM usage tips.

Key commands (run from repo root)
- Run the main eval+improve loop (requires Python 3.x):
  - python -m plan-before-build or: python plan-before-build/SKILL.md  # (NOT A SCRIPT)  # see skill scripts below
- Skill-creator workflows (use these exact scripts under skill-creator/scripts):
  - python -m skill-creator.scripts.run_loop --eval-set <workspace>/evals.json --skill-path <skill-dir> --model <model-id>
  - python -m skill-creator.scripts.run_eval --eval-set <workspace>/evals.json --skill-path <skill-dir> --model <model-id>
  - python skill-creator/scripts/aggregate_benchmark.py <workspace>/iteration-N --skill-name <name>
  - python skill-creator/eval-viewer/generate_review.py <workspace>/iteration-N --skill-name "my-skill" --benchmark <workspace>/iteration-N/benchmark.json
  - python skill-creator/scripts/package_skill.py <skill-dir> --output <outdir>

Entrypoints found (what agents should care about)
- Python CLI scripts under skill-creator/scripts are the runtime entrypoints (run_loop.py, run_eval.py, improve_description.py, aggregate_benchmark.py, package_skill.py, quick_validate.py).
- The eval viewer is a small HTTPServer started by skill-creator/eval-viewer/generate_review.py (it calls server.serve_forever()).

Environment & requirements
- Required: python (3.x) and git. The SKILL.md files list these explicitly for the included skills. Install Python packages if the skill documentation requires them (check each skill's references or scripts for imports).
- Optional: network access and credentials — some scripts call external LLM CLIs (e.g., claude -p) via subprocess; ensure relevant environment variables or CLI tools are available before running run_loop or improve_description.

Repository quirks & verification
- There is no top-level package.json/pyproject.toml/Cargo.toml or CI workflows — this repo is a collection of skills, not a monorepo application.
- There is no .pre-commit-config.yaml; do not assume pre-commit hooks exist.
- Verification steps agents should use before finishing work:
  1) Read SKILL.md for the target skill (plan-before-build or skill-creator). SKILL.md is authoritative.
  2) Run quick_validate.py for a skill directory if present: python skill-creator/scripts/quick_validate.py <skill-dir>
  3) When editing runtime code or tests, prefer to ask the user before committing or pushing.

If something is missing
- If you need CI, opencode.json, or a root README added, create them at the repo root. If you want, I can create opencode.json + a brief README template for this workspace.
