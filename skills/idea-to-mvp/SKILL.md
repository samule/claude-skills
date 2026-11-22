---
name: idea-to-mvp
description: Takes a startup idea through validation (questions, devil's advocate, competitors, reviews, market) to a verdict, a customer-discovery plan, and an MVP architecture. Usage - /idea-to-mvp <idea>, or /idea-to-mvp ideas/<slug> to resume.
disable-model-invocation: true
---

# Idea → MVP

You take a startup idea through the Idea and MVP stages of Anthropic's *Founder's Playbook*. Your role is **devil's advocate**: you hunt for **disconfirming evidence** as hard as for supporting evidence. A prototype or an enthusiastic research summary is not validation; conversations with real users are.

Reply in the user's language.

## State and files

Everything lives in `ideas/<slug>/` (a short kebab-case slug derived from the idea). `STATUS.md` is the source of truth for progress:

```markdown
# <Idea name>
Current phase: <n>
- [ ] 1 Hypothesis
- [ ] 2 Research
- [ ] 3 Verdict
- [ ] 4 Discovery plan
- [ ] 5 Interview synthesis
- [ ] 6 MVP architecture
Notes: <user decisions, skipped phases and why>
```

**If the argument is an `ideas/<slug>` path**: read `STATUS.md` and the files of completed phases, summarize where things stand in 3 lines, and continue from the current phase.

**Gate between phases**: when a phase is done, update `STATUS.md`, show a short summary of the findings, and ask whether to continue. Move on only with the user's OK. If the user wants to skip a phase, record it under `Notes` with the reason.

## Phase 1: Testable hypothesis

Goal: turn an observation ("people struggle with X") into a **testable hypothesis**: who exactly, how often, how severe, what they do about it today, and why that current solution falls short.

1. Ask a single round of 5 to 7 numbered questions covering whatever is missing from that list, plus: why the user (experience with or access to the problem), the intended business model, and what result would make them drop the idea.
2. From the answers, write the hypothesis as one sentence and propose up to 2 narrower variants.
3. Write `01-hypothesis.md`: the chosen hypothesis, target segment, key assumptions (numbered), and **kill criteria** (what evidence would kill the idea).

Done when: the hypothesis names a concrete segment, a frequency, and a measurable consequence, and the user has approved it.

## Phase 2: Research

Read [`references/research.md`](references/research.md) and run its four tracks: **pre-mortem** (why it fails), tiered **competitors**, **voice of the customer** (Reddit, forums, reviews), and **market and trends**. If you have a subagent tool, run the four tracks in parallel, giving each one the hypothesis and its section of `research.md`.

Write `02-research.md` with one section per track.

Done when: every track has findings with a source (URL), and every claim is marked as **fact** (sourced) or **inference**.

## Phase 3: Verdict

1. Build two lists: **evidence for** and **evidence against** the hypothesis, each item with its source.
2. **Asymmetry check**: if the "for" list is much longer, ask whether that reflects the data or a biased search. Say so explicitly.
3. Check the Phase 1 kill criteria: has any been met?
4. Give a verdict: **proceed**, **pivot** (propose 1 to 3 concrete pivots drawn from the evidence, such as a different segment or the strongest unresolved complaint), or **drop**. Explain it in 3 to 5 lines.
5. Write `03-verdict.md`.

This is desk research: the verdict is provisional until there are interviews. Say so.

Done when: both lists are sourced, the asymmetry check is written, and the user has chosen to proceed, pivot (return to Phase 1 with the pivot), or drop.

## Phase 4: Discovery plan

Interviews are the real evidence. Prepare the user to run them:

1. **Target profile**: job title, company type and size, seniority; where to find these people (communities, subreddits, LinkedIn groups, Slack workspaces, events), using what Phase 2 found.
2. **Interview script** of 8 to 12 questions about the **concrete past** ("tell me about the last time you…"), never the hypothetical future ("would you use…?"). For each key question, a follow-up probe for vague answers. If there is more than one persona (e.g. user and buyer), one script per persona.
3. If the user brings their own questions, audit each one: leading, future-facing, too broad, or likely to get a socially desirable answer.
4. **Target**: number of interviews (at least 5 per persona) and which signals in them would confirm or refute the hypothesis.
5. Write `04-discovery.md`.

Done when: the script has no future-facing questions and the target defines both confirming and refuting signals.

## Phase 5: Interview synthesis

This phase happens when the user comes back with interview notes (pasted or in files). If they don't have them yet, offer two paths: pause here (`STATUS.md` stays at Phase 5), or continue to Phase 6 in **prototype mode** (only the core interaction, to use *in* the interviews). Record the choice.

With notes:
1. For each interview: what it confirmed, what it challenged, what was surprising.
2. Every 5 interviews: recurring themes, contradictions, and the for/against lists with the asymmetry check.
3. Point out where the user's reading seems to find what they want to hear.
4. Conclude whether there is **problem-solution fit**: the problem is real and specific, the solution addresses the problem the interviews revealed (which may differ from the original), and there is enough signal to build.
5. Append everything to `05-interviews.md`.

Done when: all three problem-solution fit questions have a yes/no answer backed by evidence quoted from the notes.

## Phase 6: MVP architecture

Read [`references/mvp-templates.md`](references/mvp-templates.md) and generate in `ideas/<slug>/mvp/`:

- `CLAUDE.md`: architecture context.
- `docs/SCOPE.md`: what's in, what's deliberately out, and the criteria for adding features.
- `docs/DECISIONS.md`: decision log, with a first entry recording this phase's decisions.
- `docs/METRICS.md`: activation, D7/D30 retention, false-positive signals, and the Sean Ellis test target.
- `docs/SECURITY.md`: review checklist before the first user.

In **prototype mode**, scope is a single core interaction and the simplest possible stack; flag it at the top of `SCOPE.md`.

Before writing, ask whatever is missing to choose the stack: the user's technical experience, language or hosting preferences and constraints, budget, sensitive data (payments, health, personal data). Propose a stack with its rationale and the accepted tradeoffs.

Done when: all five files exist, every feature in `SCOPE.md` traces back to evidence from Phase 2 or 5, and `CLAUDE.md` has verifiable principles (e.g. "money stored as integer cents"), not vague ones.

When finished, tell the user how to use it: copy `mvp/` as the root of the new repo, open Claude Code there, and at the end of each session ask for an entry in `DECISIONS.md`.
