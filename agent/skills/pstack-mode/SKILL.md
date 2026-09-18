---
name: pstack-mode
version: 1.0.0
description: >
  A rigorous, verification-first engineering process for coding agents.
  Adapted (with cursor-specific parts removed) from "pstack" by Lauren Tan
  (github.com/cursor/plugins/tree/main/pstack, MIT licensed, "fork it,
  improve it, make it yours"). Works with any agent that can read this
  file and follow instructions — no IDE, plugin system, or specific model
  required.
sticky: true
auto-activate: true
triggers: [bug fix, feature, refactor, debugging, testing, code review, root cause, regression, reliability, release prep, verify, ship, engineering]
---

# pstack-mode

## What this is

This is not a one-off command. Once you've read this file, treat it as a
standing mode for the rest of this session (or, if your host supports
persistent memory, for every session): apply it automatically whenever a
task involves writing, changing, debugging, or reviewing code — without
being asked to invoke it by name. Stay quiet about it otherwise; don't
narrate "entering pstack-mode" or similar.

The goal is not more code. It's less code, of higher quality, verified
before you claim it's done. Bias toward the smallest change that actually
solves the problem, and never report success without having produced
evidence — a passing test you ran, an actual value you read, a diff you
inspected — not a self-report, not "it compiles."

## NOVA governance boundary (Nova-bot integration)

This skill is engineering process guidance, not authority. Inside NOVA
AUTOMATON, the following NOVA rules override anything in this package
when they conflict:

- CEO/root authority, company hierarchy, department authority, worker
  permissions, security policy, independent audit gates, release gates,
  data/credential restrictions, spending controls, and customer/contact
  controls. This skill never grants permission to bypass the
  PolicyEngine, spending limits, constitution, approvals, or
  self-modification controls.
- Independent audit overrides builder self-validation. The evidence this
  process produces (test runs, measured values, inspected diffs) is
  input for NOVA's independent auditor. It never substitutes for the
  audit, and a builder must not report audit-passed or release-passed
  state from its own verification.
- "Don't block on the human" (Step 3, principle 20, playbooks 15 and 17)
  applies only to actions NOVA already permits. Financial scope,
  self-modification, external sends, credential access, and customer
  contact still require explicit operator approval through NOVA's gates,
  even where this package calls an action reversible.
- The shipping and PR playbooks (13, 14, 17, 18) stop at NOVA's release
  gates: no push, merge, publish, or deploy without the approvals those
  gates require.
- model-routing.md is advisory only. NOVA's `modelStrategy` config and
  orchestration controls (including child-agent limits) decide actual
  model and sub-agent routing.

## Activation rule

Apply this mode when the task is:
- writing or changing code of any kind
- debugging, investigating, or explaining a codebase
- reviewing a diff or PR
- any multi-step technical task where being wrong is costly

Skip it for trivial one-line lookups, pure conversation, or tasks where
the user has explicitly asked for a fast, rough answer over a rigorous
one. If unsure, apply it — the principles below don't cost much on a
small task and matter a lot on a big one.

## Step 1 — read the principles index

Before starting real work, mentally run through `principles.md` in this
package. It's short by design — 21 one-line rules grouped into five
categories (core, architecture, verification, delegation, meta). Don't
re-derive engineering judgment from scratch each time; use that list as
your checklist.

## Step 2 — match the task to a playbook

Read `playbooks.md`. Pick the one that matches the task's shape (bug fix,
new feature, perf issue, investigation, refactor, shipping a PR, etc.)
and follow its steps as your working checklist. If nothing matches well,
fall back to this general sequence:

1. **Understand before changing.** If this is a bug, reproduce it first.
   If this is a change to unfamiliar code, read the actual call sites and
   types before writing anything, don't guess at the shape.
2. **Design the smallest correct change.** Prefer deleting or simplifying
   over adding. If you're touching a function boundary, settle the types
   and the caller's usage before writing the body.
3. **Do the work.** Route bulk, repetitive, or high-volume subtasks to
   tools or scripts rather than doing them by hand one at a time — build
   the lever, don't turn the crank yourself.
4. **Verify against the real artifact.** Run it. Read the actual output.
   Look at the actual diff. A test passing is evidence; "this should
   work" is not.
5. **Report honestly, for two audiences.** State what changed and why in
   plain terms (for someone using the result) and what's structurally
   different and worth knowing (for someone maintaining it). Don't pad
   the report with hedging or unearned confidence either way.

## Step 3 — don't block on the human unnecessarily

Proceed and present the result; let the person course-correct after the
fact. Reserve stopping-to-ask for genuinely irreversible or destructive
actions (deleting data, force-pushing, spending money, sending something
externally) — not for ordinary implementation choices you're equipped to
make yourself. Inside NOVA, the governance boundary above still applies:
NOVA's approval gates decide what needs an operator, not this section.

## Model / role routing (optional)

If your host lets you run different sub-tasks on different models or
sub-agents, see `model-routing.md` for a generic template — pstack's
original used per-role model config (fast model for mechanical code,
strong reasoning model for judgment calls, separate models for review
panels). If your host is single-model, ignore this section entirely;
nothing here depends on it.

## What's intentionally not in this package

This tier covers the always-on process, the 21 principles, and all 22
task playbooks. The original pstack also ships two subagent definitions
and a Slack-triage automation pack ("Benny") — those depend on
sub-agent-spawning and Slack/Cursor infrastructure that doesn't
generalize to "any agent," so they weren't ported.

## Attribution

Original concept and content: **pstack**, by Lauren Tan (poteto),
published under the Cursor plugins repo, MIT license. This is an
independent, generalized rewrite with all Cursor-specific mechanics
(plugin manifest format, `~/.cursor/rules` config path, `/add-plugin`
install flow, Cursor built-in commands, Cursor-specific model slugs)
removed or replaced with generic equivalents.
