# Cline Integration

[Cline](https://cline.bot) is an AI coding agent available as a VS Code
extension, a JetBrains plugin, and a standalone CLI. It supports **Skills** —
directories containing a `SKILL.md` with `name` + `description` frontmatter,
loaded on demand when the description matches your request. See Cline's
[Skills documentation](https://docs.cline.bot/customization/skills) for the
full spec.

Each agency agent is rendered as a Cline skill: a directory named
`agency-<slug>/` containing a `SKILL.md` with `name` + `description`
frontmatter and the persona as the instruction body. The generated files come
from `scripts/convert.sh --tool cline`, which writes one skill directory per
agency agent into `integrations/cline/`. Those generated files are not
committed (see `.gitignore`); regenerate them locally.

## Generate

From the repository root:

```bash
./scripts/convert.sh --tool cline
```

## Install

Run the installer to copy skills into Cline's global skills directory:

```bash
./scripts/install.sh --tool cline
```

This copies the generated files into:

```text
~/.cline/skills/agency-<slug>/SKILL.md
```

To install into a specific project instead of globally, use `--path`:

```bash
cd /your/project && /path/to/agency-agents/scripts/install.sh --tool cline --path .cline/skills
```

Use `--division` / `--agent` to install a subset, or set `CLINE_SKILLS_DIR`
to override the destination.

## Usage

Once installed, a skill activates either by auto-trigger — Cline matches your
message against each skill's `description` and loads it automatically — or
by explicitly typing its slash command:

```text
/agency-frontend-developer
```

## Known Issues (Cline)

These are open Cline bugs, not issues with this integration or its generated
output:

- **Skills don't appear in the `/` suggestion menu** ([cline#13805](https://github.com/cline/cline/issues/13805)):
  the VS Code extension's slash-command dropdown only lists default commands,
  workflows, and MCP prompts — skills are never listed there, even when
  installed and enabled. Typing the full command (e.g.
  `/agency-frontend-developer`) and pressing Enter still loads the skill
  correctly; it just won't show as you type.
- **Skill creation writes to the wrong directory** ([cline#12523](https://github.com/cline/cline/issues/12523)):
  creating a new skill through Cline's own Skills menu writes it to
  `~/.agents/skills/`, while Cline's runtime reads skills from
  `~/.cline/skills/`. This integration is unaffected — `install.sh` writes
  directly to `~/.cline/skills/`, not through that UI — but if you're
  troubleshooting a skill you created by hand, check both directories.

## Notes

- Skill directories and the `name` field are both prefixed with `agency-` to
  avoid colliding with your own or other skills.
- Cline caps `description` at 1024 characters; agency agent descriptions are
  one-liners well under that limit.
- If you're on WSL, install from the same environment your Cline extension
  actually runs in (Remote-WSL vs. native Windows/VS Code) — `$HOME` differs
  between them, and the installer writes wherever `~` resolves for the shell
  it's run from.
- If you update agents in this repo, regenerate the Cline output before
  reinstalling.

## File Format

Each skill is a `SKILL.md` with Cline-compatible frontmatter:

```yaml
---
name: agency-frontend-developer
description: Expert frontend developer specializing in...
---
[persona and instructions]
```
