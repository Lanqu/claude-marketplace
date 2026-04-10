---
name: psych-review
description: |
  Multi-perspective code review through simulated cognitive disorder lenses.
  Dispatches parallel reviewer agents, each with a distinct psychological bias, and
  aggregates consensus findings. Use when the user asks for "psych review", "multi-perspective
  review", "paranoid review", "review from different angles", or names a specific disorder
  persona. Also triggers on "psych-review --all", "psych-review --pack security", etc.
  <example>User: "psych review this branch"</example>
  <example>User: "run a paranoid review of the PR"</example>
  <example>User: "psych-review --pack security"</example>
---

# Psych Review

Multi-perspective code review where parallel agents each review through a distinct cognitive disorder lens. Each persona forces attention onto a specific concern area that others ignore. Findings flagged independently by multiple reviewers with different biases are high-confidence real issues.

## Arguments

`<target>` - what to review. Supports:
- Branch name: `feat-my-feature`
- PR number: `#123`
- Commit range: `abc123..def456`
- File paths: `src/auth.ts src/db.ts`
- Empty (default): current branch vs main/master

Flags:
- `--all` - use all 10 reviewers
- `--pack <name>` - use a preset pack: `security`, `quality`, `chaos`
- `--verbose` - show full per-reviewer tables (default: consensus + unique findings only)

## Workflow

### Phase 1: Resolve Target

Determine what to review based on arguments:

1. If no argument given, detect the main branch (`main` or `master`) and diff against it
2. If a branch name, diff against main: `git diff main...<branch>`
3. If a PR number, use `gh pr diff <number>`
4. If a commit range, use `git diff <range>`
5. If file paths, use `git diff HEAD -- <files>`

Collect:
- The full diff text
- List of changed files with diff stats (`git diff --stat`)
- Read the full content of each changed file (for agents to inspect beyond the diff)

**Large diffs (30KB+):** Do NOT paste the full diff into agent prompts. Instead, provide the diff stats and the list of changed functions/classes extracted from the diff. Instruct agents to use Read/Glob/Grep to inspect files directly. This keeps agent prompts within context limits.

**Trivial diffs (under 500 bytes or 1-2 files):** Suggest single-persona mode. Multiple agents reviewing a 3-line change produces noise, not signal.

### Phase 2: Select Reviewers

Read the persona definitions from `references/personas.md` in this skill directory.

Choose the reviewer set based on flags:

| Pack | Reviewers | Count |
|------|-----------|-------|
| `default` (no flag) | Paranoid, OCD, GAD, ADHD, Schizoid | 5 |
| `--pack security` | Paranoid, GAD, Schizoid, Hypochondriac | 4 |
| `--pack quality` | OCD, Depression, Narcissist, Schizoid | 4 |
| `--pack chaos` | ADHD, DID, Manic, Hypochondriac | 4 |
| `--all` | All 10 | 10 |

**Single persona mode:** If the user names a specific persona (e.g., "run a paranoid review"), dispatch only that one reviewer. Skip Phase 4 and present that reviewer's findings directly.

### Phase 3: Dispatch Parallel Agents

Launch ALL selected reviewers in a SINGLE message with multiple Agent tool calls. Each agent runs as a plain subagent (do NOT use `superpowers:code-reviewer` - it has its own instructions that conflict with persona prompts).

Each agent's prompt MUST include:
1. The full persona definition copied from `references/personas.md` (do NOT make the agent read the file)
2. The diff (or diff stats + file list for large diffs)
3. The list of changed files with full paths
4. Instructions to use Read, Glob, Grep freely to inspect source code
5. The structured output format below

**Agent prompt template:**

```
Review the following code changes.

## Your Persona

You are a code reviewer with {DISORDER_NAME}. {FULL_PERSONA_BLOCK}

Stay in character throughout your review. Your comments should reflect your persona's
cognitive patterns while making technically valid observations.

## Changes to Review

{DIFF_OR_SUMMARY}

## Changed Files

{FILE_LIST}

## Your Job

1. Read the diff carefully
2. Use Read, Glob, Grep to inspect the full source files and surrounding code as needed
3. Write your review findings using the EXACT format below
4. Each finding must reference a specific file and line (or line range)
5. Stay in character

## Output Format (strict)

For each finding, use this exact structure:

FINDING {N}:
file: {relative file path}
line: {line number or range}
tag: [{short canonical tag, e.g. null-safety, naming, injection, complexity}]
severity: {high | medium | low}
comment: {your finding, in character voice}

After all findings, write:
SUMMARY: {1-2 sentence summary in character}
FINDING_COUNT: {total number of findings}
```

### Phase 4: Aggregate Consensus

After all agents return:

1. Parse each agent's findings using the structured `FINDING` blocks (file + tag + severity + comment)
2. Group findings mechanically: same file AND same tag = same finding. Different files or different tags = different findings, even if the prose sounds similar
3. Count how many distinct reviewers flagged each grouped finding
4. Classify using scaled thresholds based on reviewer count N:
   - **CONSENSUS** - flagged by >= ceil(N * 0.6) reviewers (high confidence)
   - **NOTABLE** - flagged by >= ceil(N * 0.3) reviewers (likely real, worth checking)
   - **UNIQUE** - flagged by 1 reviewer (potentially the most valuable - novel catches others missed)

### Phase 5: Present Results

Output two sections (three with `--verbose`):

**Section 1: Consensus and Notable Findings**

```
## Consensus Findings

| # | File | Tag | Finding | Severity | Flagged By | Count |
|---|------|-----|---------|----------|------------|-------|
| 1 | path/to/file.kt | null-safety | Description | high | Paranoid, GAD, Schizoid | 3/5 |
```

Sort by count descending, then severity descending.

**Section 2: Unique Finds (The Novel Catches)**

These are the findings only one reviewer caught. They may be noise, but they are also where multi-perspective review earns its cost - the things a single reviewer would miss entirely. Present them with the reviewer name so the user can calibrate trust based on the persona.

```
## Unique Finds

| # | Reviewer | File | Tag | Finding | Severity |
|---|----------|------|-----|---------|----------|
| 1 | ADHD | path/to/file.kt | stale-comment | Found a TODO from 2019 | low |
```

**Section 3 (only with `--verbose`): Per-Reviewer Tables**

For each reviewer, present their full findings table:

```
### {Persona Name}

| # | File | Line | Tag | Severity | Comment |
|---|------|------|-----|----------|---------|
| 1 | path/to/file.kt | 42 | null-safety | high | Finding in character voice |
```

## Persona Reference

Persona definitions are in `references/personas.md` in this skill directory. Read that file during Phase 2 to get the full persona blocks for agent prompts.

**Available personas:** Paranoid, OCD, Narcissist, DID, GAD, Depression, ADHD, Manic, Schizoid, Hypochondriac
