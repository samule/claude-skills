# idea-to-mvp

A Claude Code skill that takes a startup idea from validation to an MVP architecture, following the Idea and MVP stages of Anthropic's [*Founder's Playbook: Building an AI-Native Startup*](https://assets.claude.com/503ee04b4aa55a4db49a69b993463b6fa8dad6d8.pdf).

## What it does

| Phase | What happens | Output |
|---|---|---|
| 1. Hypothesis | Asks you questions and turns the idea into a testable hypothesis with kill criteria | `01-hypothesis.md` |
| 2. Research | Pre-mortem (why it fails), tiered competitors, Reddit and reviews, market and trends | `02-research.md` |
| 3. Verdict | Evidence for and against, bias check, and proceed / pivot / drop | `03-verdict.md` |
| 4. Discovery | Who to interview, where to find them, interview script | `04-discovery.md` |
| 5. Interviews | Synthesizes your interview notes and assesses problem-solution fit | `05-interviews.md` |
| 6. MVP | Generates `CLAUDE.md`, scope, decision log, metrics, and security checklist | `mvp/` |

It stops at the end of each phase and waits for your OK. Progress is saved in `ideas/<slug>/STATUS.md`, so you can stop (for example, to run interviews) and resume later.

## Installation

For all your projects, clone this repo and copy the skill folder into your personal skills:

```bash
git clone https://github.com/samule/claude-skills
cp -R claude-skills/skills/idea-to-mvp ~/.claude/skills/
```

Or for a single project: copy `skills/idea-to-mvp/` to `.claude/skills/idea-to-mvp/` inside that project's repo.

## Usage

```
/idea-to-mvp An app that reconciles expense reports with the accounting system for SMBs
```

To resume an idea:

```
/idea-to-mvp ideas/expense-reconciliation
```

Phase 2 needs web search. Reddit and some review sites block automated reading; when that happens the skill falls back to search results and alternative sources, and tells you what it couldn't read.

The skill is invoked by hand only (`disable-model-invocation: true`). If you'd rather have Claude use it whenever you describe an idea, delete that line from the `SKILL.md` frontmatter.

The skill's instructions are in English, but it replies in whatever language you write in.

## Structure

```
idea-to-mvp/
├── SKILL.md                    # the 6 phases and the gates between them
└── references/
    ├── research.md             # how to research in Phase 2
    └── mvp-templates.md        # Phase 6 templates
```
