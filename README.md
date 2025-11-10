# BMAD Orchestrating

Autonomous software development using Claude Code. No humans in the loop.

## QuickStart Checklist

- Add these files to your project
- Have Claude create a `project-overview.md` to provide concise context of your project to your orchestrator. Aim for a paragraph, keep it brief.
- Then, start Claude with `--dangerously-skip-permissions` and paste the `orchestrator.md` contents to your main, fresh Claude Code thread. That thread will invoke subagents as needed, and will continue for **1 entire Epic**. You can edit the prompt if you want it to continue for all epics.
- Disregard "cowboy-mode.md" UNLESS you want to parallelize (in which case, see [cowboy-mode](##cowboy-mode))

## The Three Agents

**@sm-scrum** - Creates detailed stories from epics. Loads PRD/Architecture context. MUST mark "Ready for Development".

**@dev** - Implements stories, writes tests, validates everything passes. MUST mark "Ready for Review".

**@qa-quality** - Reviews against acceptance criteria, creates quality gate (PASS/CONCERNS/FAIL/WAIVED). MUST mark "Done" or "In Progress".

**note: scrum master and QA agents run "haiku" model for speed and context efficiency**

## What This Does

Automates the **Scrum Master -> Dev -> QA** cycle until your entire epic is implemented and reviewed. Claude continuously:

1. Drafts stories (SM agent)
2. Implements code (Dev agent)
3. Reviews implementation (QA agent)
4. Has dev fix issues if QA sources any and repeats until "Done"
5. Moves to next story automatically

**You only get interrupted when the entire epic is finished or there's a critical blocker.**

## What's Included

This repository contains:

- **BMAD v4** - Complete framework in `.bmad-core/`
- **Pre-configured agents** - SM, Dev, and QA agents ready to use in `.claude/agents/`
- **Orchestrator prompts** - Orchestrator.md for sequential implementation, and cowboy-mode for parallelized workflow

```
orchestrator.md                   # Sequential: one epic, one story at a time
cowboy-mode.md      # Parallel: multiple epics/stories simultaneously
.claude/agents/                   # SM, Dev, QA agent configs (pre-loaded)
.bmad-core/                       # Full BMAD v4 framework
```

## cowboy-mode

1. Prompt Claude to make a `workstreams.md` file, which will analyze your PRD and architecture docs to establish what workstreams (sequences of stories) depend upon, i.e. stories 2.1-2.10 depend upon story 1.1 to be Done, etc.

### workstreams.md prompt

```
Review `docs/prd.md` and `architecture.md` to identify parallelizable workstreams. Analyze Epic/Story dependencies to determine:
- Which stories block others (e.g., Story 1.1 dev environment setup must precede Stories 2.1-2.5 frontend work)
- Which workstreams can proceed independently once unblocked
- Optimal execution order for maximum parallelization

Create `docs/workstreams.md` that clearly maps dependencies and parallel execution paths, enabling foolproof concurrent development.
```

## Benefits

- **Zero human bottleneck** - Cycles 24/7 until done
- **Quality enforced** - Status gates prevent shortcuts
- **Full audit trail** - Everything logged to `orchestration-flow.md`
- **Scalable** - Sequential for focus, parallel for speed
- **Context efficient** - Agents load only what they need

## What's in .bmad-core

Complete BMAD framework with 10 agents, 20+ tasks, checklists, knowledge base, and templates. Agents reference these as needed.

**TL;DR**: Paste an orchestrator prompt into Claude Code. It runs SM -> Dev -> QA cycles continuously until your entire epic is done. You get pinged when it's finished.
