# AI_skills

Claude Code slash-command skills for AI-assisted engineering.

## Skills

### `/nasa-code`
Audit code against NASA JPL's *Power of Ten* rules (extended for AI-assisted development). Flags violations across control flow, resources, correctness, abstraction, and AI hygiene.

Inspired by Gerard Holzmann's [Power of Ten — Rules for Developing Safety Critical Code](https://spinroot.com/gerard/pdf/P10.pdf) (NASA/JPL, 2006), adapted for the era of LLM-generated code.

## Install

Drop any `.md` file in this repo into `~/.claude/commands/` to make it available as a global slash command in Claude Code.

```bash
cp nasa-code.md ~/.claude/commands/
```

Then invoke with `/nasa-code` inside any Claude Code session.
