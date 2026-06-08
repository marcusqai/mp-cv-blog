---
title: "Grill-Me and Diagnose: Two AI Agent Skills That Changed How I Work"
date: 2026-06-09T07:45:00+08:00
draft: false
tags:
  - tech
  - ai-agents
  - openclaw
  - prompt-engineering
summary: "Two simple skills that transformed my AI agent from eager executor to disciplined engineer."
description: "How grill-me forces alignment before execution and diagnose brings systematic debugging to AI agents, inspired by Matt Pocock and Frederick Brooks."
---

## The Problem: AI Agents That Rush to Execute

We have all been there. You tell your AI assistant to build something, and within seconds it produces a plan, starts writing code, and confidently marches in the wrong direction. Thirty minutes later, you realize the misunderstanding happened in the first ten seconds.

The root cause is not stupidity. It is **misalignment**. The agent heard your words but did not understand your intent.

After months of debugging these failures, I discovered two skills from [Matt Pocock](https://github.com/mattpocock/skills) that fundamentally changed how my AI agents work: **grill-me** and **diagnose**.

## Skill 1: Grill-Me - The Art of Asking Before Doing

### The Core Insight

Matt Pocock describes grill-me in just three sentences:

> Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the decision tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Three sentences. But they invert the entire agent workflow.

**Default flow:** Human specifies, then Agent executes.
**Grill-me flow:** Human describes, Agent interrogates, reach Shared understanding, then Execute.

The concept of a "decision tree" comes from Frederick Brooks' *The Design of Design*. Every design is a tree of choices. You must walk every branch until all leaf nodes are resolved.

### How I Enhanced It for My System

The original skill is beautifully minimal. But for my OpenClaw setup, I needed more guardrails. Here is what I added:

**Three Iron Rules:**

1. **One question at a time.** Multiple questions lead to shallow answers. The human will answer the last one and ignore the rest.

2. **Every question must include a recommended answer.** This prevents "interrogation fatigue." The agent proposes, the human confirms or corrects. Efficient.

3. **Never ask what you can find yourself.** Search memory, read config files, check past decisions first. The human's time is 100x more valuable than the agent's.

**Strict Exit Criteria:**

Before declaring "alignment complete," seven conditions must be met:

- All decision points resolved
- No dangling dependencies
- Success criteria defined
- Edge cases covered
- Risks identified
- Deliverable format confirmed
- No contradictory decisions

If any condition fails, keep asking. No exceptions.

**Durable Artifact:**

The session output must be written to a file. Without a persistent artifact, agent statelessness erases everything. Next session starts cold.

### Real Example

```
Human: "I want to build a supplier quality tracking system"

Agent: Grill-Me activated.
       I am now in interrogator mode.
       I will not execute anything until we reach shared understanding.

       Q1: Target Users
       Who are the primary users of this system?

       My suggestion: Supplier quality team
       Reason: Your daily work interfaces with suppliers
       Alternative: Entire company (but scope too broad)

Human: "Supplier quality team"

Agent: Q2: Success Criteria
       What does "done" look like for this system?
       ...
```

After 10-30 questions (depending on complexity), the agent generates a consensus document. The human confirms. Only then does execution begin.

## Skill 2: Diagnose - Systematic Debugging for AI Agents

### The Core Insight

> Build the right feedback loop, and the bug is 90% fixed.

Most AI agents debug by guessing. They see an error, form a hypothesis, change something, and hope. This is not debugging. This is praying.

Matt Pocock's diagnose skill enforces a six-phase loop with strict gates between each phase.

### The Six Phases

**Phase 1: Build a Feedback Loop**

This is the skill. Everything else is mechanical. If you have a fast, deterministic pass/fail signal for the bug, you will find the cause.

Methods in priority order:
1. Direct retry (command fails, check exit code)
2. Unit test
3. HTTP/curl test
4. Log analysis
5. Minimal reproduction
6. Differential comparison
7. Stress loop (for non-deterministic bugs)

**Phase 2: Reproduce**

Run the loop. Watch the bug appear. Confirm it matches what the user described, not a different bug that happens to be nearby.

**Phase 3: Hypothesize**

Generate 3-5 ranked hypotheses before testing any of them. Each must be falsifiable:

> "If X is the cause, then changing Y will make the bug disappear."

If you cannot state the prediction, the hypothesis is a vibe. Discard it.

**Phase 4: Instrument**

Each probe must map to a specific prediction. Change one variable at a time. Tag every debug log with a unique prefix for cleanup.

**Phase 5: Fix + Regression Test**

Apply the fix. Re-run the feedback loop. Confirm the bug is gone and no side effects appeared.

**Phase 6: Cleanup + Post-Mortem**

Remove all debug instrumentation. Answer three questions:
1. What was the root cause?
2. What would have prevented this bug?
3. What did we learn?

Write it all down. Future you will thank present you.

### Real Example: Active Memory Failure

Here is a real case I debugged yesterday:

```
Diagnose activated

Problem: Memory search occasionally fails
Method: 6-phase disciplined loop

Phase 1: Build feedback loop
  Run: openclaw memory status
  Found: "index metadata is missing"
  Vector search: paused

Phase 2: Reproduce
  Run: openclaw memory search "test"
  Confirmed: "Vector memory search is paused"

Phase 3: Hypotheses
  #1 Index metadata lost -> Predict: rebuild fixes it
  #2 Embedding API unstable -> Predict: curl test fails (ruled out)
  #3 SQLite corruption -> Predict: integrity check fails

Phase 4: Verify
  Test hypothesis #1: rebuild index
  Result: Matches prediction

Phase 5: Fix
  openclaw memory index --force --agent main
  openclaw memory index --force --agent ckc
  openclaw memory index --force --agent reportor
  Verify: search works

Phase 6: Post-mortem
  Root cause: Index metadata lost after update
  Prevention: Add health check to heartbeat
  Learning: active-memory depends on memory index
```

Total time: 3 minutes. Zero guessing.

## How These Skills Work Together

The two skills form a complete lifecycle:

1. **Grill-Me** (before) prevents bugs caused by misalignment
2. **Execute** the agreed plan
3. **Diagnose** (when things break) systematically resolves issues
4. **Learn** from post-mortems, compound over time
5. **Grill-Me** again next time, smarter than before

Both enforce discipline. Both produce durable artifacts. Both learn from history.

## Implementation Notes

These skills are designed for [OpenClaw](https://docs.openclaw.ai), but the principles apply to any AI agent framework:

1. **Role inversion is powerful.** Make the agent challenge plans, not just execute them.
2. **Feedback loops are everything.** Without a pass/fail signal, you are guessing.
3. **Gate each phase.** Do not let the agent skip to "fix" before "reproduce."
4. **Persist everything.** Agent sessions are stateless. Files are not.
5. **Search history first.** The answer might already exist.

## The Results

Since implementing these skills:

- **Zero rework** from misalignment (grill-me catches it upfront)
- **Faster debugging** (diagnose eliminates guessing)
- **Better learning** (post-mortems compound over time)
- **Higher trust** (the agent asks before acting on risky tasks)

The investment is real. Grill-me sessions can take 10-30 minutes. But that is 10-30 minutes saved from rework, misunderstanding, and debugging.

---

*What disciplined processes have you implemented to keep your AI agents aligned and effective?*
