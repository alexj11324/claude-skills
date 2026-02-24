---
name: troubleshoot
description: "Use when user reports an error, bug, or something not working. Search-first troubleshooting with diagnostic phase. Triggers: debug, error, broken, not working, failing, crash, exception."
allowed-tools: WebSearch, WebFetch, AskUserQuestion, Read, Glob, Grep
argument-hint: <error or symptom description>
---

# Troubleshoot

Search-first diagnostic workflow. Human executes commands.

## Workflow

`0.Load → 1.Search → 2.Qualify → 3.Diagnose → 4.Investigate → 5.Learn`

### 0. Load Learnings

Read `learnings.yaml` in project root if exists. Apply known patterns before searching.

### 1. Search (do first)

80% of bugs solved online.

- WebSearch: `[error] [stack] [framework]` on SO, GitHub, Docs, Reddit
- Solution found → skip to 5.Learn

### 2. Qualify (2-3 questions)

AskUserQuestion:
- Stack? (language, framework, runtime)
- Environment? (local, container, cloud)
- Changed recently? (deploy, config, dependency)

### 3. Diagnose

1. **Mental models**: Check learnings.yaml → WebSearch pattern → reason with 5 Whys/Fishbone
2. **Isolation**: Wolf Fence (binary search), swap one variable, minimal repro
3. **Root cause drill**: 5 Whys, Fishbone 6 M's

#### 5 Whys

Ask "why?" up to 5 times to drill past symptoms to root cause.

Example:
- Why did the page go blank? → JS error in render
- Why did render error? → State was undefined
- Why was state undefined? → Auth callback didn't set it
- Why didn't callback set it? → INITIAL_SESSION event was ignored
- Why was it ignored? → async handler missed the sync event

#### Fishbone (6 M's)

Categorize possible causes:
- **Method**: Wrong algorithm, logic error, race condition
- **Machine**: OS, browser, hardware, memory
- **Material**: Bad input data, corrupt state, stale cache
- **Measurement**: Wrong metrics, misleading logs, missing telemetry
- **Milieu (Environment)**: Config drift, env vars, network, DNS
- **Manpower**: Misunderstanding of API, wrong docs version

#### Wolf Fence (Binary Search)

Bisect the problem space:
1. Add checkpoint at midpoint of suspected code path
2. Is bug before or after checkpoint?
3. Repeat in the half that contains the bug
4. Narrow until single cause identified

Pattern matches → suggest fix, skip OODA

### 4. Investigate (OODA)

Only if diagnosis inconclusive.

- **Observe**: User runs command, pastes output
- **Orient**: Analyze output, update hypothesis
- **Decide**: Next diagnostic command or confirm cause
- **Act**: Suggest fix (user executes)

Exit when root cause confirmed and fix verified.

### 5. Learn

After resolution, ask: "Save this learning?"

Append to `learnings.yaml`:
```yaml
- pattern: "<error signature or symptom>"
  cause: "<root cause>"
  fix: "<what resolved it>"
  scope: "global"  # or "project:<name>"
  date: "YYYY-MM-DD"
```

## Quick Reference

| Step | Action | Exit Condition |
|------|--------|----------------|
| 0. Load | Read learnings.yaml | Known pattern? → Apply |
| 1. Search | WebSearch error + stack | Solution found? → Step 5 |
| 2. Qualify | 2-3 questions | Context gathered |
| 3. Diagnose | 5 Whys / Fishbone / Wolf Fence | Root cause identified |
| 4. Investigate | OODA loop | Cause confirmed + fix verified |
| 5. Learn | Save to learnings.yaml | Knowledge persisted |
