# voice-mission-control Retrieval Commands

Use CLI retrieval instead of repo-wide re-scanning.

## Core read

```bash
sed -n '1,120p' .project/STATE.md
sed -n '1,120p' .project/QUEUE.md
sed -n '1,120p' .project/HANDOFF.md
sed -n '1,120p' CLAUDE.md
```

## Lane-only read

```bash
sed -n '/^## Backend lane/,/^## Frontend lane/p'   .project/WORKSTREAMS.md
sed -n '/^## Frontend lane/,/^## References/p'      .project/WORKSTREAMS.md
```

## Decision lookup

```bash
rg -n "D-[0-9]{3,}" .project/DECISIONS.md
```

## GitHub coordination

```bash
# list open packets
gh issue list --label "type:packet" --state open

# create a packet from a QUEUE.md row
gh issue create --label "type:packet,lane:backend,status:todo" \
  --title "BACK.P1 — Python env + MLX model loading proof" \
  --body-file -

# see id -> issue mapping
gh issue list --label "type:packet" --json number,title,labels --limit 100

# close with a note
gh issue close <n> -c "Done — see .project/QUEUE.md"

# PRs
gh pr create --fill --web
gh pr list --state open
```

## Packet handoff shape

Use this in GitHub issues, PR bodies, or agent handoff messages:

```text
Lane: <backend|frontend>
Packet: <ID, e.g. BACK.P2>
Scope: <one bounded task>
Read set:
- .project/STATE.md, .project/QUEUE.md, .project/HANDOFF.md
- relevant lane section in .project/WORKSTREAMS.md
- CLAUDE.md invariants
- smallest relevant source file
Contract source:
- <WORKSTREAMS.md delivery contract / architecture doc section>
Proof:
- <script output, latency measurement, screenshot>
Blocked by:
- <missing field / endpoint / decision>   # omit if none
```

## Artifact lookup

```bash
find .project -maxdepth 2 -type f | sort
sed -n '1,120p' .project/DOCS-MAP.md
```
