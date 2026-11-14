---
name: update-skills
description: End-of-chat review. Works out what this session taught that should outlive it, then writes it into memories, skills and working files. Use when the user asks "anything to learn", "anything to update", "before I archive this chat", "wrap up", "update skills", "update memory", "what did we learn", or when a session is clearly ending after non-trivial work. Also use mid-session when the user corrects how you work, or when a skill misfires and its triggers need fixing.
---

# End-of-chat review

Decide what this session taught that a future session would otherwise rediscover the hard way, then write it down. Default is to act, not to ask. Deletions are the exception.

Work the steps in order. Create a todo per step.

## 1. Read the current state first

```
cat ~/.claude/projects/<project-slug>/memory/MEMORY.md
ls ~/.claude/projects/<project-slug>/memory/
ls ~/.claude/skills/ .claude/skills/ 2>/dev/null
```

`<project-slug>` is the working directory with every `/` replaced by `-`, so `/home/me/Projects/app` becomes `-home-me-Projects-app`. Also read the project's `AGENTS.md` or `CLAUDE.md`, whichever it has.

You cannot judge what is new until you know what is already recorded.

## 2. Harvest candidates

Scan the whole session for:

- **System facts**: IDs, project keys, URLs, endpoints, where a thing lives, which account owns it.
- **Gotchas**: something that looked broken but was not, a trap that cost time, a tool behaving unlike its docs.
- **Corrections to how you work**: voice, format, process, tone. These become `feedback` memories, with the why.
- **Decisions, including rejected options**, and the reason.
- **Skill misfires**: a skill that should have triggered and did not, or triggered wrongly. Fix its `description` and trigger phrases.

## 3. Bin ruthlessly

Do not save:

- Anything already in the repo, git history, AGENTS.md, CLAUDE.md or an existing skill.
- Task state that dies with this chat.
- General knowledge about how a public tool works.
- Vague impressions with no action attached.

Test: if the next session could find it in 30 seconds by reading the code, bin it.

## 4. Verify before writing

Every claim you are about to save must be checked **in this session**, not recalled. Read the live page, call the API, open the file. A memory is read as fact for months, so a guess laundered into a memory is worse than no memory.

If you cannot verify a detail, keep the note and write the doubt into it: "snippet ID not confirmed". Never round an assumption up to a fact.

Convert every relative date to an absolute one. Record the state as it actually is, including decisions the user has not yet made.

## 5. Find the existing home

`grep -ril "<topic>"` across the memory folder and the relevant skill. Update the existing file rather than adding a near-duplicate. If this session proved an existing memory or skill wrong, correct it now and say so in the report.

## 6. Write

- **Memories**: one fact per file, frontmatter, correct `type` (`user` / `feedback` / `project` / `reference`), `[[links]]` to related memories. Add the one-line pointer to `MEMORY.md`.
- **Skills first for anything another Claude needs.** Simon also runs Claude on the ingagent VM, which loads the shared skills but not this Mac's memory folder. Every rule, preference or fact that changes how work gets done goes into the skill that does that work; a memory alone reaches only this machine. Keep the memory too, and point it at the skill section that now holds the rule (Simon, 10 Nov 2026).
- **Then sync ingagent.** Skills there do not pull by themselves. `ssh simon@100.65.182.54`, then for each changed skill in `~/.claude/skills/<name>`: `git fetch`, save any local diff to `~/skill-backups/<date>-pre-sync/<name>.patch`, and `git pull --ff-only`. If local edits touch an incoming file, `git stash`, pull, `git stash pop`. Push any real lesson the VM left uncommitted. `ingenio-website-page` keeps VM-only path edits (`/home/simon`, `~/.config/bws/env`) in its working tree on purpose: never commit them. Check every skill shows `behind 0 ahead 0` against `@{u}` (first done 10 Nov 2026). The VM has no `skills-index` checkout, so a skill added to the index never arrives there by itself: if `~/.claude/skills/<name>` is missing, `gh repo clone mrsmickers/<name>-skill ~/.claude/skills/<name>` and run the skill README's check (`ingenio-prospect-deck` was missing until 12 Nov 2026). If the stash pop conflicts, the VM copy is usually just old: keep the pulled version (`git checkout HEAD -- <file>`), carry over any line upstream lacks, then `git stash drop`.
- **Working on ingagent? Sync the Mac yourself.** Pull the changed skills on the Mac with the same steps; never hand Simon the commands (Simon, 14 Nov 2026). The Mac's SSH port times out from ingagent (`100.102.88.56:22`, 14 Nov 2026), so `ListAgents`, pick an idle Remote Control session whose name ends `- mac`, and `SendMessage` it the exact pull steps, the expected commit per repo, and a request to reply to this session with `git log -1 --oneline` and the `@{u}` counts. That route confirms nothing by itself: report it as sent, not done, until the reply arrives.
- **Skills**: edit the specific section. Small surgical changes, not rewrites. A new skill only when the session revealed a repeatable multi-step procedure, never for a single fact.
- **Working files**: wherever the project's `AGENTS.md` or `CLAUDE.md` says, or a `working/` folder in the project if it says nothing. Use descriptive kebab-case names. Move anything worth keeping out of `/tmp` or the scratchpad before the session ends.

## 7. Report, briefly

- **Saved**: file, one line each.
- **Updated**: file, what changed.
- **Deliberately skipped**: what, and why. This matters most, because it is the user's chance to overrule you.
- **Corrections** made to existing memories or skills, flagged plainly.
- **Open items**: decisions still sitting with the user.

## Deletions need a yes

Never delete a memory or a skill section silently. List the file, why it is now wrong, and what should replace it. Wait for the answer.
