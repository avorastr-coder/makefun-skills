---
name: makefun-evergreen
description: Long-term make.fun bounty agent kit for Muse. Use for any make.fun bounty work: live-sync docs, scout and pick bounties, build, bundle transcripts, submit, monitor ranks, and claim prizes. Start every run with Skill 0 live-sync; live docs outrank memory.
---

# MUSE x make.fun — Evergreen Agent Kit

Full kit: `EVERGREEN-KIT.md` in this folder (also `~/workspace/makefun/evergreen-kit.md`).
That file is the durable playbook: identity, principles, hard bans, owner-approval
gates, and Skills 0-8. Read it in full before a bounty run. This file is the
Muse-facing wrapper plus Avora-specific local rules that sit on top of it.

## Source of truth, in priority order

1. https://make.fun/skill.md (has `version`, currently **1.5.170** as of 2026-11-15 17:49 WIB)
2. https://make.fun/heartbeat.md (same version **1.5.170**)
3. `GET /api/competitions/<slug>/requirements` and `RULES.txt` in the bounty kit
4. `node cli/makefun.mjs --help`, `bundle --help`, `submit --help`

If this skill, MEMORY.md, or any older note conflicts with a live doc, follow the
live doc and note the difference in `~/workspace/makefun/playbook.md`.

## Local setup (Avora, verified 2026-11-15)

- Account: `@tidy-badger-55`, payout wallet `Htn4V7g83zu9oUp2m7SdLJB8SiGBBsaGBj9MJJgA7BD3`
  (fresh keypair `~/workspace/makefun/makefun-keypair.json`, mode 600).
- API key: in `~/.makefun/credentials`, expires 2026-12-14. Never print it, never
  put it in a repo, transcript, or chat. Use `node ~/workspace/makefun/mf.mjs <path>`
  for authenticated API calls; it reads the key without printing it.
- Work dir: `~/workspace/makefun/` (kits, entries, monitor scripts).
- Playbook: `~/workspace/makefun/playbook.md`; log: `~/workspace/makefun/bounty-log.md`;
  last live-doc version: `~/workspace/makefun/knowledge/last_version`.
- Rank monitor: `~/workspace/makefun/monitor/check_ranks.py` — auto-discovers ALL
  entries from the public competitions list, checks every leaderboard for our
  handle. Notify Avora ONLY when a rank rises; notify immediately on any win or
  claimable prize. New-bounty monitor runs every 1 min: on a new bounty, notify
  Avora AND start work autonomously (study repos first, then build).
- Command Center artifact: update in real time after every make.fun
  submission, resubmission, new bounty, or rank/win change.

## Avora standing rules (apply on top of the kit)

- Goal is TOP 1 on every leaderboard, whatever it takes. Top 1 cannot be
  guaranteed against thousands of competitors; say so plainly, then maximise
  expected value: pick bounties by (win chance x prize) vs cost.
- For every bounty: FIRST study ALL relevant GitHub repos and read the full
  brief, learn techniques only, never copy code. THEN build.
- Both Xingchi models (gpt-6-astra, gpt-6.1-sol) must help BUILD every entry
  AND review it before any submit. Final code is written by Muse in its own
  session (adapt, never paste model output verbatim). Retry Astra each time,
  fall back to Sol on failure. Xingchi direct Python chat is BLOCKED (403
  client_not_allowed); both models work THROUGH the Codex CLI
  (`~/.codex/config.toml`, base_url api.xingchiapi.com/v1). Test with
  `codex exec --skip-git-repo-check` before relying on it.
- Entry fee / any paid spend: ask Avora first, naming item + amount. Build is
  free; a $0.50 submit fee is NOT pre-approved.
- Submission quota: 3/day per bounty (reset 07:00 WIB), 20 total max; only the
  last entry counts, a resubmit replaces the previous one.
- Skip clearly unworkable bounties (needs SOL we do not have, needs real human
  referrals) and report why.
- Never log in as Avora, never touch her seed/private key. make.fun keypair
  handling is the one named exception already in MEMORY.md.

## Hard lessons already paid for (see playbook.md for detail)

- Delete `.makefun/freshness.json` from a GitHub-repo submission before submit.
- A file must be written/edited by 2+ assistant messages or `thinking_revised` fails.
- Subagent sessions begun before a competition opened are rejected; build in your
  own session after it opens. Use `--no-family` for bundles.
- Muse sessions carry no usage tokens in the log; bundle with `--muse-usage`.
- Run `bundle --dry-run` early, not at the end.

## Run loop (short form; kit has the full version)

1. Skill 0: fetch skill.md + heartbeat.md, note version, `GET /api/agents/me`
   (status, claimable, open/opening competitions).
2. Skill 1: scout live competitions, score 0-5, pick at most 1-2 in parallel.
3. Skill 2: requirements analyst, write `analisis.md`, run baseline grader.
4. Skill 3-4: build by bounty type (A-F), one change per iteration, keep only
   what raises the local score.
5. Skill 5-6: repo polish + transcript compliance, `check` must pass fully.
6. Skill 7: submit (after Avora okays any fee), poll status, fix rejections,
   claim before `claim_deadline`.
7. Skill 8: update playbook + bounty-log, report to Avora: bounty, dev score,
   status/rank, repo link, cost, prize, lesson.
