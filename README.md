# claude-marketplace

Public Claude Code plugin marketplace.

## Install

```
/plugin marketplace add Lanqu/claude-marketplace
```

## Browse plugins

```
/plugin discover
```

## Plugins

### psych-review

Multi-perspective code review through 10 cognitive disorder lenses with structured consensus aggregation.

Each reviewer agent roleplays a distinct psychological bias (Paranoid, OCD, GAD, ADHD, Schizoid, Narcissist, Depression, DID, Manic, Hypochondriac) that forces attention onto a specific concern area. Findings flagged independently by multiple reviewers are high-confidence real issues.

**Usage:**

```
/psych-review                      # Review current branch vs main (5 default reviewers)
/psych-review feat-my-feature      # Review specific branch
/psych-review #123                 # Review PR
/psych-review --all                # Use all 10 reviewers
/psych-review --pack security      # Security-focused pack
/psych-review --verbose            # Include full per-reviewer tables
```

**Packs:**

| Pack | Reviewers |
|------|-----------|
| default | Paranoid, OCD, GAD, ADHD, Schizoid |
| security | Paranoid, GAD, Schizoid, Hypochondriac |
| quality | OCD, Depression, Narcissist, Schizoid |
| chaos | ADHD, DID, Manic, Hypochondriac |

**Install:**

```
/plugin install psych-review@claude-marketplace
```
