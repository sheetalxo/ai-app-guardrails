# ai-app-guardrails

Agent Skills that make AI coding tools build **secure, clean, production-ready apps** — fewer vulnerabilities, a proper backend, and a tidy UI/UX.

| Skill | Use for |
|---|---|
| `secure-app-builder` | Any web app/API/backend: security rules, backend structure, UI/UX, self-review checklist |
| `android-play-store-app` | Android apps for Google Play: secure storage, permissions, release build, Play checklist |

Both follow the open [Agent Skills](https://agentskills.io) format (a folder with `SKILL.md`).

## Install

**Gemini CLI**
```bash
# all skills in this repo
gemini skills install https://github.com/<your-username>/ai-app-guardrails.git
# or one skill
gemini skills install https://github.com/<your-username>/ai-app-guardrails.git --path skills/secure-app-builder
gemini skills install https://github.com/<your-username>/ai-app-guardrails.git --path skills/android-play-store-app
```

**GitHub CLI (v2.90+), works for Gemini CLI, Claude Code, Cursor, Codex, Antigravity**
```bash
gh skill install <your-username>/ai-app-guardrails secure-app-builder --agent gemini
```

**Claude Code / Claude.ai**: copy the skill folders into `~/.claude/skills/`, or upload the `.skill` files in Claude's skill settings.

**Google AI Studio** (no skill folders): paste `ai-studio/system-instructions.md` into *System instructions*.

Replace `<your-username>` with your GitHub username. Keep the repo public, or authenticate git if it is private.
