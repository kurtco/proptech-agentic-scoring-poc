# Repository Instructions

- This checkout currently contains documentation and agent guidance only; no application source, package manifest, lockfile, build config, or test runner is present. Do not invent setup or verification commands.
- Read `README.md` or `README.es.md` for the intended product and architecture; they describe the same system in English and Spanish.
- The operative agent guidance is under `ai/agents/`: `agents.md` defines the general execution protocol, `backend-architect.md` defines the NestJS workflow, and `constitution.MD` defines architectural and technology constraints.
- Formal feature requirements and acceptance criteria live under `docs/specs/`; treat them as the source of truth for implementation and focused tests.
- The documented constitution path `.ai/.constitution` does not exist in this checkout; use `ai/agents/constitution.MD` unless the repository layout is changed and verified.
- Treat the current Markdown as design intent, not proof of implemented behavior. Verify future claims against executable project files once they exist.
- Do not modify existing Markdown files when working on repository setup unless the user explicitly requests documentation changes; `AGENTS.md` is the exception when maintaining these instructions.
