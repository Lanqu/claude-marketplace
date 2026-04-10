# psych-review

Multi-perspective code review through simulated cognitive disorder lenses.

## Usage

```
/psych-review                          # Review current branch vs main (5 default reviewers)
/psych-review feat-my-feature          # Review specific branch
/psych-review #123                     # Review PR
/psych-review --all                    # Use all 10 reviewers
/psych-review --pack security          # Security-focused pack
/psych-review --pack quality           # Code quality pack
/psych-review --pack chaos             # Creative/divergent pack
/psych-review --verbose                # Include full per-reviewer tables
```

## How It Works

Dispatches N parallel reviewer agents, each roleplaying a reviewer with a distinct psychological disorder. Each disorder simulates a cognitive bias that forces attention onto a specific concern area. After all agents return, findings are aggregated into a consensus table using structured tags for reliable grouping. Unique findings (caught by only one reviewer) are highlighted separately as potentially high-value novel catches.

## Reviewers

| Persona | Focus Area |
|---------|------------|
| Paranoid | Security: attack vectors, injection, auth, dependency supply chain |
| OCD | Surface consistency: naming patterns, formatting, method signature symmetry |
| GAD (Anxiety) | Edge cases: null inputs, failure cascading, missing error paths |
| ADHD | Random details: typos, stale comments, cross-file inconsistencies |
| Schizoid | Structure: cyclomatic complexity, coupling, cohesion, duplication |
| Narcissist | Architecture: API design, abstraction levels, code elegance |
| Depression | Maintainability: abstraction decay, documentation rot, entropy |
| DID (Multiple) | Three voices: big picture + confusion test + wild edge cases |
| Manic | Ambition: under-engineering, missed scalability, framework opportunities |
| Hypochondriac | Production health: observability gaps, resource leaks, missing timeouts |

## Packs

| Pack | Reviewers | Use Case |
|------|-----------|----------|
| `default` | Paranoid, OCD, GAD, ADHD, Schizoid | General review |
| `security` | Paranoid, GAD, Schizoid, Hypochondriac | Security and resilience audit |
| `quality` | OCD, Depression, Narcissist, Schizoid | Maintainability and design review |
| `chaos` | ADHD, DID, Manic, Hypochondriac | Divergent/creative perspectives |
| `--all` | All 10 | Maximum coverage |

## Consensus Thresholds

Thresholds scale with reviewer count (N):
- **CONSENSUS**: flagged by >= 60% of reviewers
- **NOTABLE**: flagged by >= 30% of reviewers
- **UNIQUE**: flagged by 1 reviewer (highlighted, not buried)
