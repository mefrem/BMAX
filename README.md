# BMAD Orchestrating

Autonomous software development using Claude Code. No humans in the loop.

## What This Does

Automates the **Scrum Master -> Dev -> QA** cycle until your entire epic is complete. Claude continuously:
1. Drafts stories (SM agent)
2. Implements code (Dev agent)
3. Reviews implementation (QA agent)
4. Fixes issues and repeats until "Done"
5. Moves to next story automatically

**You only get interrupted when the entire epic is finished or there's a critical blocker.**

## Files

```
orchestrator.md                   # Sequential: one epic, one story at a time
orchestrator-parallelized.md      # Parallel: multiple epics/stories simultaneously
.claude/agents/                   # SM, Dev, QA agent configs
.bmad-core/                       # Full BMAD framework
```

## Quick Start

### 1. Choose Your Mode

**Sequential** - Linear, one epic:
- Copy `orchestrator.md`
- Paste into Claude Code
- Watch it cycle through stories until epic complete

**Parallelized** - Multiple epics, max throughput:
- Requires `docs/parallelization-analysis.md` in your project
- Copy `orchestrator-parallelized.md`
- Paste into Claude Code
- Watch it work multiple stories across workstreams simultaneously

### 2. Your Project Needs

```
your-project/
├── docs/
│   ├── project-overview.md           # Project context
│   ├── orchestration-flow.md         # Auto-generated logs
│   └── parallelization-analysis.md   # For parallel mode only
├── stories/                          # Story files
└── .claude/agents/                   # Copy from this repo
```

## How It Works

### Status Gates (Enforced)

```
Draft
  -> SM finalizes -> Ready for Development
  -> Dev implements -> Ready for Review
  -> QA reviews -> Done ✓ OR In Progress (needs fixes)
  -> (If fixes needed) Dev fixes -> Ready for Review
  -> QA re-reviews -> Done ✓
```

**Breaking Mode**: Agents MUST update status or the cycle fails. This ensures quality and progress visibility.

### The Loop

```
┌─ START ─────────────────────────────────┐
│                                          │
│  SM creates story -> "Ready for Dev"    │
│  Dev implements -> "Ready for Review"   │
│  QA reviews -> "Done" or "In Progress"  │
│                                          │
│  If "In Progress":                      │
│    Dev fixes -> QA re-reviews           │
│                                          │
│  If "Done":                             │
│    IMMEDIATELY next story (no pause)    │
│                                          │
└─ REPEAT UNTIL EPIC COMPLETE ────────────┘
```

### Parallel Mode Difference

Works on **multiple stories across multiple epics** simultaneously:

```
Batch 1: Dev on 1.4 + Dev on 2.1 + QA on 3.1 (all independent)
Batch 2: QA on 1.4 + SM creates 2.3 + Dev on 3.1
...continues until ALL epics 100% done
```

Reads `parallelization-analysis.md` to know dependencies. Sequences dependent work, parallelizes independent work.

## The Three Agents

**@sm-scrum** - Creates detailed stories from epics. Loads PRD/Architecture context. MUST mark "Ready for Development".

**@dev** - Implements stories, writes tests, validates everything passes. MUST mark "Ready for Review".

**@qa-quality** - Reviews against acceptance criteria, creates quality gate (PASS/CONCERNS/FAIL/WAIVED). MUST mark "Done" or "In Progress".

## When Does It Stop?

**Auto-continues through**:
- QA feedback cycles
- Story completions
- Normal development work

**Only interrupts you for**:
- Epic(s) complete (all stories "Done")
- Critical blocker (missing docs, conflicting requirements)
- Agent failure (can't update status after 2 tries)
- Excessive iteration (story fails QA 3+ times)

## Benefits

- **Zero human bottleneck** - Cycles 24/7 until done
- **Quality enforced** - Status gates prevent shortcuts
- **Full audit trail** - Everything logged to `orchestration-flow.md`
- **Scalable** - Sequential for focus, parallel for speed
- **Context efficient** - Agents load only what they need

## What's in .bmad-core

Complete BMAD framework with 10 agents, 20+ tasks, checklists, knowledge base, and templates. Agents reference these as needed.

## Example Session

```
[15:23] SM: Created story 1.3 -> Ready for Development
[15:45] Dev: Implemented 1.3 -> Ready for Review
[16:10] QA: Reviewed 1.3 -> In Progress (error handling incomplete)
[16:35] Dev: Fixed 1.3 -> Ready for Review
[16:50] QA: Re-reviewed 1.3 -> Done ✓

[16:51] SM: Created story 1.4 -> Ready for Development
[17:15] Dev: Implemented 1.4 -> Ready for Review
[17:40] QA: Reviewed 1.4 -> Done ✓

[17:41] SM: No more stories needed
[17:41] 🎉 EPIC COMPLETE -> Interrupts you
```

---

**TL;DR**: Paste an orchestrator prompt into Claude Code. It runs SM -> Dev -> QA cycles continuously until your entire epic is done. You get pinged when it's finished.
