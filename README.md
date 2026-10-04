# Skills

Agent skills for Claude Code (and other runtimes that read `SKILL.md` skills).

| Skill | Use when |
|---|---|
| [researching-a-reference-game](skills/researching-a-reference-game/SKILL.md) | Building a game like, inspired by, or cloning an existing game, before designing its mechanics. It grounds every rule in the best available sources (reimplementations, data mines, expert play) instead of memory. |

## Install

Copy or symlink a skill folder into your personal skills directory:

```bash
git clone https://github.com/drewpledger/skills.git
cp -r skills/skills/researching-a-reference-game ~/.claude/skills/
```

Claude Code picks it up automatically; it triggers from the skill's `description`.
