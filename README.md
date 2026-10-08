# Community Agent Skills

Ten free, MIT-licensed [Agent Skills](https://agentskills.io) for everyday work: inbox triage, finding files, scheduling meetings, tracking subscriptions, meal planning, backups, task capture, translation and summaries, batch file jobs, and careful Cloudflare administration.

They follow the open Agent Skills format (`skills/<name>/SKILL.md` with `name` and `description` frontmatter), so the same folders work in Claude Code, Claude.ai, OpenAI Codex, Cursor, GitHub Copilot, Gemini CLI, Grok Build, and other tools that read the format. Each skill also has a plain copy-paste prompt in [`prompts/`](prompts/) for chatbots that don't support skills.

This is the any-agent edition of [Cutseven/grok-community-skills](https://github.com/Cutseven/grok-community-skills). The Grok edition is packaged as a Grok Build plugin, has been submitted to xAI's plugin marketplace ([pending review](https://github.com/xai-org/plugin-marketplace/pull/1276)), and adds two Grok-only extras: a hook that asks before destructive shell commands and a read-only subagent for untrusted content.

## Skills

| Skill | What it does | Prompt version |
|---|---|---|
| [`smart-inbox-triage`](skills/smart-inbox-triage/SKILL.md) | Sorts email into urgent/action/info/spam, pulls out action items and due dates, writes a short digest | [prompt](prompts/smart-inbox-triage.md) |
| [`universal-file-finder`](skills/universal-file-finder/SKILL.md) | Finds local files from a plain-language description (name, content, size, date, type) | [prompt](prompts/universal-file-finder.md) |
| [`meeting-sync-bot`](skills/meeting-sync-bot/SKILL.md) | Collects availability across time zones, ranks slots, drafts invites | [prompt](prompts/meeting-sync-bot.md) |
| [`subscription-tracker`](skills/subscription-tracker/SKILL.md) | Finds recurring charges in statements, flags renewals, drafts cancellation requests | [prompt](prompts/subscription-tracker.md) |
| [`meal-planner-grocery`](skills/meal-planner-grocery/SKILL.md) | Plans meals from what you have and builds a low-waste grocery list | [prompt](prompts/meal-planner-grocery.md) |
| [`auto-backup-restore`](skills/auto-backup-restore/SKILL.md) | Plans and runs encrypted, versioned backups (restic) and safe restores | [prompt](prompts/auto-backup-restore.md) |
| [`one-command-task-capturer`](skills/one-command-task-capturer/SKILL.md) | Turns one sentence into a structured task, catches duplicates, runs a weekly review | [prompt](prompts/one-command-task-capturer.md) |
| [`context-translator-summarizer`](skills/context-translator-summarizer/SKILL.md) | Translates for meaning, then gives a bullet summary and glossary | [prompt](prompts/context-translator-summarizer.md) |
| [`batch-task-runner`](skills/batch-task-runner/SKILL.md) | Chains rename/convert/resize/compress/zip jobs, with a dry-run preview first | [prompt](prompts/batch-task-runner.md) |
| [`cloudflare-infra-worker`](skills/cloudflare-infra-worker/SKILL.md) | Read-first Cloudflare engineer: DNS, Workers/Pages, WAF, SSL/TLS, Tunnel/Access; changes only in EXECUTE mode, with previews, rollback plans and explicit confirmation for destructive actions | [prompt](prompts/cloudflare-infra-worker.md) |

## How they work

The skills are instructions only: no scripts, no MCP servers, nothing that runs on install. Each one has a **Capabilities to use** section that describes the actions it needs (for example "fetch emails" or "search files"). The agent uses whatever tools it already has for those actions: email or calendar connectors, MCP servers, file access, a shell. If it has no suitable tool, the skill tells it to say so and offer a manual alternative instead of pretending.

### Safety

Every skill follows the same rules:

- It never sends, posts, deletes, moves, or overwrites anything until you've seen exactly what will happen and said yes.
- Content from emails, files, statements, and web pages is treated as data, never as instructions.
- `cloudflare-infra-worker` is read-only by default. It changes nothing until you switch it to EXECUTE mode, and destructive or high-risk actions still need a specific confirmation.

## Install

Every tool below reads the same `SKILL.md` folders. Put the skill folders you want where your tool looks for them. The paths and commands come from each tool's current documentation, linked in each section.

The quickest manual install for most tools is to copy the folders into `~/.agents/skills/`. OpenAI Codex, Cursor, GitHub Copilot, Gemini CLI and Grok Build all read that folder. Claude Code uses `~/.claude/skills/` instead.

```bash
git clone https://github.com/Cutseven/community-agent-skills.git
mkdir -p ~/.agents/skills
cp -R community-agent-skills/skills/* ~/.agents/skills/
```

Read a skill before you install it. Skills are instructions your agent will follow.

### Any supported agent: `npx skills`

The open-source [`skills` CLI](https://github.com/vercel-labs/skills) (the one behind [skills.sh](https://skills.sh/docs)) installs skills from a GitHub repo into the right folder for many agents, including Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI and Grok Build:

```bash
npx skills add Cutseven/community-agent-skills --list                         # list the skills in this repo
npx skills add Cutseven/community-agent-skills --skill smart-inbox-triage     # one skill, choose agents interactively
npx skills add Cutseven/community-agent-skills --skill '*' -g -a claude-code  # all skills, user-wide, for one agent
```

skills.sh says the CLI sends anonymous install counts to rank its leaderboard. Use the manual copy above if you'd rather not.

### Claude Code

Claude Code loads personal skills from `~/.claude/skills/<name>/SKILL.md` and project skills from `.claude/skills/<name>/SKILL.md`. Run one with `/skill-name`, or let Claude pick it up from the description.

```bash
mkdir -p ~/.claude/skills
cp -R community-agent-skills/skills/smart-inbox-triage ~/.claude/skills/
```

Docs: [Extend Claude with skills](https://code.claude.com/docs/en/skills)

### Claude.ai and the Claude desktop app

1. Turn on **Code execution and file creation** in Settings > Capabilities. On Team and Enterprise plans, an Owner enables it in organization settings.
2. Zip one skill folder so the folder itself is the top level of the archive:
   ```bash
   cd community-agent-skills/skills && zip -r smart-inbox-triage.zip smart-inbox-triage/
   ```
3. Go to **Customize > Skills**, click **+**, then **Create skill > Upload a skill**, and upload the zip. Turn the skill on.

Claude.ai uploads accept only the spec's frontmatter fields (`name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools`). These skills use only those fields.

Docs: [Use skills in Claude](https://support.claude.com/en/articles/12512180-use-skills-in-claude), [How to create custom skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills)

### OpenAI Codex (CLI, IDE extension, ChatGPT desktop app)

Codex reads user skills from `~/.agents/skills/` and repository skills from `.agents/skills/` (in the working directory and each parent up to the repo root). Mention a skill with `$skill-name` or pick it from `/skills`. You can also ask the built-in `$skill-installer` to install skills from this repo.

Docs: [Build skills (Codex)](https://developers.openai.com/codex/skills)

### Cursor

Cursor loads skills from `~/.cursor/skills/` or `~/.agents/skills/` (user) and from `.agents/skills/` or `.cursor/skills/` (project). Run one by typing `/` in Agent chat. Cursor doesn't import skills straight from a GitHub URL (that needs a plugin marketplace), so copy the folders.

Docs: [Agent Skills (Cursor)](https://cursor.com/docs/context/skills)

### GitHub Copilot (cloud agent, CLI, VS Code and JetBrains agent mode)

With GitHub CLI 2.90.0 or later (`gh skill` is in public preview):

```bash
gh skill preview Cutseven/community-agent-skills smart-inbox-triage
gh skill install Cutseven/community-agent-skills smart-inbox-triage --scope user
```

Or copy folders into `~/.copilot/skills/` or `~/.agents/skills/` (personal), or into `.github/skills/`, `.claude/skills/` or `.agents/skills/` in a repository (project).

Docs: [About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills), [Adding agent skills for GitHub Copilot](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/add-skills)

### Gemini CLI

```bash
gemini skills install https://github.com/Cutseven/community-agent-skills.git --path skills/smart-inbox-triage
```

Or copy folders into `~/.gemini/skills/` or `~/.agents/skills/` (user), or `.gemini/skills/` or `.agents/skills/` (workspace). `/skills list` shows what loaded. Gemini asks for your consent before it activates a skill.

Docs: [Agent Skills (Gemini CLI)](https://geminicli.com/docs/cli/skills/)

### Grok Build

Grok Build reads skills from `~/.grok/skills/`, `./.grok/skills/` and `~/.agents/skills/`, and also reads Claude Code and Cursor skill folders. Skills appear as slash commands, for example `/smart-inbox-triage`.

On Grok you can instead install the [Grok edition](https://github.com/Cutseven/grok-community-skills) as a plugin: `grok plugin install Cutseven/grok-community-skills --trust` (or from xAI's plugin marketplace once it's listed).

Docs: [Skills, Plugins & Marketplaces (Grok Build)](https://docs.x.ai/build/features/skills-plugins-marketplaces)

### Agents that only read AGENTS.md

[AGENTS.md](https://agents.md/) is a plain instruction file, not a skills system, so there's no official way to load skills through it. A workaround for any agent that reads AGENTS.md and can open files: copy the skill folders into your project's `.agents/skills/` and add this to `AGENTS.md`:

```markdown
## Skills
Task playbooks live in `.agents/skills/<name>/SKILL.md`. Each file starts with a `description`
that says when to use it. When a request matches one, read that SKILL.md and follow it.
```

### Chatbots without skill support

Open the skill's file in [`prompts/`](prompts/), copy the whole block into a new chat, and send it. Then ask for what you need. The prompts are generated from the `SKILL.md` files, so they carry the same rules, including the safety rules.

## Validation

Every skill passes the official Agent Skills reference validator, [`skills-ref`](https://github.com/agentskills/agentskills/tree/main/skills-ref):

```bash
git clone https://github.com/agentskills/agentskills.git
cd agentskills/skills-ref && python3 -m venv .venv && . .venv/bin/activate && pip install -e .
cd /path/to/community-agent-skills
for d in skills/*/; do skills-ref validate "$d"; done
```

[`scripts/validate.py`](scripts/validate.py) adds repo checks: spec length limits, only spec frontmatter fields, `SKILL.md` under 500 lines, the shared safety rules present in every skill, and `prompts/` in sync with the skills.

To change a skill, edit `skills/<name>/SKILL.md`, then run `python3 scripts/build_prompts.py` and `python3 scripts/validate.py`.

## References

- Agent Skills specification: https://agentskills.io/specification
- Anthropic's skills repository and format: https://github.com/anthropics/skills
- Claude Code skills: https://code.claude.com/docs/en/skills
- Claude.ai skills: https://support.claude.com/en/articles/12512180-use-skills-in-claude
- OpenAI Codex skills: https://developers.openai.com/codex/skills
- Cursor skills: https://cursor.com/docs/context/skills
- GitHub Copilot skills: https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/add-skills
- Gemini CLI skills: https://geminicli.com/docs/cli/skills/
- Grok Build skills and plugins: https://docs.x.ai/build/features/skills-plugins-marketplaces
- AGENTS.md: https://agents.md/
- `skills` CLI: https://github.com/vercel-labs/skills

## Disclaimer

These are unofficial community skills. They are not made by, affiliated with, or endorsed by Anthropic, OpenAI, GitHub, Microsoft, Google, Anysphere (Cursor), xAI, Cloudflare, or any other company whose products they mention. Product names are used only to describe what a skill works with.

The skills are provided as-is, without warranty (see [LICENSE](LICENSE)). Review a skill before you use it, and take extra care before letting any AI agent act on live accounts, production infrastructure, or important data. You are responsible for what your agent does with them.

Built by Gustavo Cadena with help from AI tools.

## License

MIT © Gustavo Cadena. See [LICENSE](LICENSE).
