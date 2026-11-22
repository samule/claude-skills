# MVP templates (Phase 6)

Replace everything in `<>`. Every line must be specific to this idea; delete sections that don't apply rather than leaving them generic.

## `CLAUDE.md`

Loaded in every Claude Code session: keep it under ~80 lines. Longer material goes in `docs/` and is imported with `@`.

```markdown
# <Name>

<The problem it solves, in one sentence.>
Users: <segment from the validated hypothesis>.
Expected scale in 6 months: <users / volume>.

## Stack
- <framework and language>
- <database and ORM>
- Auth: <provider>. Use the provider; custom auth is out.
- Deploy: <platform>

## Architecture principles
- <where business logic lives>
- <how data is accessed>
- <multi-tenancy / permissions rule, if applicable>
- <rules for critical data types, e.g. money stored as integer cents>

## Dependencies
- Approved list: <libraries>. Ask before adding any new dependency.

## Accepted tradeoffs (deliberate debt)
- <shortcut> — revisit when <measurable condition>.

## Commands
- <dev, test, migrations>

## Every session
- At the start: read @docs/SCOPE.md. If the task isn't under "In scope", flag it before building.
- At the end: add an entry to @docs/DECISIONS.md.
- Before exposing to users: run the checklist in @docs/SECURITY.md.
```

## `docs/SCOPE.md`

```markdown
# MVP scope
<If prototype mode: "PROTOTYPE MODE — core interaction only, for interviews.">

## Core interaction
<The single action the product's value depends on.>

## In scope
- <feature> — evidence: <source in 02-research.md or 05-interviews.md>

## Out of scope (deliberately)
- <tempting feature> — why it waits: <reason>

## Criteria for adding a feature
Added only if:
- <N> or more active users ask for it unprompted, or
- the data shows drop-off at that step.
Founder ideas go into this file as candidates and get built once they meet the criteria.
```

## `docs/DECISIONS.md`

```markdown
# Decisions

## <YYYY-MM-DD>: <topic>
- Built: <what>
- Decision: <what was chosen and why>
- Assumption introduced: <what is taken as true and when to revisit it>
```

## `docs/METRICS.md`

```markdown
# Metrics (defined before launch)

- Activation: the user <concrete action> within <timeframe>.
- Retention: D7 target <x%>, D30 target <y%>.
- Sean Ellis test: >40% "very disappointed" among active users.
- False positives to watch: <signups without activation, revenue without retention, launch spikes>.
- "Borrowed" traction to discount: <friends, investor network, viral post>.
```

## `docs/SECURITY.md`

```markdown
# Security review before the first user

- [ ] Authentication and session handling
- [ ] Data exposure in API responses (each endpoint returns only the user's/tenant's data)
- [ ] Input validation and injection (SQL, XSS)
- [ ] Secrets kept out of the repo and the client
- [ ] Dependencies with known vulnerabilities
- [ ] <sector requirements: payments, health, personal data>
Anything touching auth, secrets, or sensitive data gets human review in addition to Claude's.
```
