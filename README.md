# update-skills

An end-of-chat review for AI coding agents. At the end of a working
session, say "wrap up" or "anything to learn?" and it works out what the
session taught that the next session would otherwise have to rediscover:
IDs and URLs, traps that cost time, corrections to how the agent works,
decisions and why they were made. It checks each fact, then writes it
into memories, skills and working files, and reports what it saved and
what it chose to skip.

It bins more than it keeps. Anything already in the code, the git history
or an existing skill is left out, and every claim is checked in the
session before it is saved, so a guess never turns into a memory that
later sessions read as fact.

---

## Install it

**Hand this page's URL to Claude and say "install this skill for me".**
Claude can do every step below except the one under "What only you can
do". If you would rather do it by hand, the same commands work.

### For Claude: install procedure

Work through this in order. Report what you did and stop at the first
step that needs the user.

**1. Find the skills folder for the tool you are running in.**

| Tool | Folder |
|------|---|
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` |
| OpenClaw | `~/clawd/skills/` |
| Any other AgentSkills tool | its own skills folder |
| Claude Desktop or Cowork | no folder; see step 6 |

**2. Check the prerequisite.**

```bash
command -v git
```

If nothing prints, tell the user to install git (`brew install git` on
macOS, `sudo apt install git` on Ubuntu) and stop.

**3. Clone into a folder named exactly `update-skills`.**

The folder name is not cosmetic. A skill's `name:` must match its folder
or the tool will not load it. The repository is called
`update-skills-skill`, so the destination must be given explicitly.
Substitute the folder from step 1 for `~/.claude/skills`.

```bash
git clone https://github.com/mrsmickers/update-skills-skill.git ~/.claude/skills/update-skills
```

If the destination already exists, an older copy is installed. Update it
with the command under "Keeping it current" instead of cloning again.

**4. Check it landed.**

```bash
head -3 ~/.claude/skills/update-skills/SKILL.md
```

Expect `---` followed by `name: update-skills`.

**5. Tell the user to restart their tool**, because skills are read at
startup. Then give them the smoke test below.

**6. Claude Desktop or Cowork only.** There is no skills folder. The user
uploads a tarball under Customize, then Private Skills. Build one from a
clone:

```bash
tar -czf update-skills.tar.gz --exclude=.git -C ~/.claude/skills update-skills
```

### What only you can do

1. **Restart the tool.** Quit and reopen Claude Code, or your other
   tool, so it picks up the new skill.

### If it does not work

| What you see | Why | Fix |
|---|---|---|
| `destination path 'update-skills' already exists` | A copy is already installed | `git -C ~/.claude/skills/update-skills pull --ff-only` |
| Skill never triggers | Folder misnamed, or tool not restarted | Folder must be `update-skills` exactly; restart the tool |
| It saves skills and working files but no memories | Your tool has no memory folder at the path in step 1 of `SKILL.md` | Tell it where memories live in your `AGENTS.md`, or accept skills-only updates |

### Keeping it current

```bash
git -C ~/.claude/skills/update-skills pull --ff-only
```

---

## Use it

At the end of a session, say any of:

- "wrap up"
- "anything to learn?"
- "update skills" or "update memory"
- "before I archive this chat"

It also runs mid-session when you correct how the agent works, or when a
skill fires at the wrong time and its triggers need fixing.

Smoke test, to confirm it works. In a session where you have done some
real work and corrected the agent at least once, say:

> Anything to learn from this chat?

A good answer reads the existing memory index first, saves your
correction as a `feedback` memory with a **Why:** line, and ends with a
short report under the headings Saved, Updated, Deliberately skipped,
Corrections and Open items. In a session where nothing new happened, it
should say there is nothing worth saving.

### What it will not do

- Delete a memory or a skill section without asking. It lists what it
  wants to remove and why, then waits for a yes.
- Save a claim it could not check in the session. It keeps the note and
  writes the doubt into it.
- Create a new skill for a single fact. New skills are only for a
  repeatable multi-step procedure.

### Tool support

It is built for Claude Code and reads and writes Claude Code's memory
folder, `~/.claude/projects/<project-slug>/memory/`. In other tools
the skill, working-file and reporting steps work as written; the memory
steps need you to say where memories live.

## What is in the folder

- `SKILL.md`: the seven-step review the agent follows.
- `README.md`: this page.
- `LICENSE`: MIT.

## Where this comes from

Written and maintained by Simon Smyth
([@mrsmickers](https://github.com/mrsmickers)). Issues and pull requests
are welcome.
