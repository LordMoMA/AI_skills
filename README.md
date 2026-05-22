# AI_skills

Claude Code slash-command skills and Codex skills for AI-assisted engineering.
<img width="1536" height="1024" alt="ChatGPT Image May 22, 2026, 11_13_31 AM" src="https://github.com/user-attachments/assets/d78f7156-c433-46ab-b279-99087bc28449" />

## Skills

### Claude Code: `/nasa-code`

Audit code against NASA JPL's *Power of Ten* rules (extended for AI-assisted development). Flags violations across control flow, resources, correctness, abstraction, and AI hygiene.

Inspired by Gerard Holzmann's [Power of Ten — Rules for Developing Safety Critical Code](https://spinroot.com/gerard/pdf/P10.pdf) (NASA/JPL, 2006), adapted for the era of LLM-generated code.

### Codex: `$nasa-code`

The same audit workflow packaged as a Codex skill under `codex/nasa-code/`.

## Install

### Claude Code

Drop any top-level Claude command `.md` file into `~/.claude/commands/` to make it available as a global slash command in Claude Code.

```bash
cp nasa-code.md ~/.claude/commands/
```

Then invoke with `/nasa-code` inside any Claude Code session.

### Codex

Copy the skill folder into your Codex skills directory.

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills/nasa-code"
cp -R codex/nasa-code/. "${CODEX_HOME:-$HOME/.codex}/skills/nasa-code/"
```

Then invoke with `$nasa-code` inside any Codex session.

## Read the article

[![NASA Code](https://github.com/user-attachments/assets/d2ebc9c1-18cb-4d83-883c-1413f370283d)](https://leestack.dev/writing/nasa-rules-for-code-that-cant-fail)
