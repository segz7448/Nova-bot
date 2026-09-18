# pstack-mode — NOVA integration notes

How the generic `pstack-mode` package (see `README.md` for upstream
provenance and attribution) is wired into NOVA AUTOMATON.

## Where it is installed

`agent/skills/pstack-mode/`, alongside the other repo-bundled skills
(`nova-engineering-mode`, `distribution-agent`). The package's intended
structure is preserved: `SKILL.md` is the standing process, and it
references `principles.md`, `playbooks.md`, and `model-routing.md` in
the same directory. Copy the whole directory, not just `SKILL.md` —
the loader injects only `SKILL.md`, and the agent reads the sibling
files from disk when the process routes to them.

## Discovery and inheritance

- The runtime loads skills from `config.skillsDir` (default
  `~/.automaton/skills`) at startup (`agent/src/skills/loader.ts`).
  Install by copying `agent/skills/pstack-mode/` into that directory,
  the same way the other repo-bundled skills are installed, or by
  pointing `skillsDir` at a directory containing it.
- The frontmatter sets `auto-activate: true`, matching
  `nova-engineering-mode`, so the process is injected into the system
  prompt of every agent that loads this skills directory — newly
  spawned engineering agents inherit it with no per-session setup.
- Runtime loading wraps skill text in `[SKILL: ... — UNTRUSTED CONTENT]`
  markers and sanitizes it. pstack-mode stays process guidance; the
  loader's trust boundary is unchanged.

## Relationship to NOVA Engineering Mode

`nova-engineering-mode` is the native, auditable workflow runner
(`automaton --engineering`, see `agent/ENGINEERING-MODE.md`) with
repository gates and run records. `pstack-mode` is the engineering
methodology layer: principles and playbooks for how to investigate,
fix, build, and verify. They are complementary, not duplicates:
pstack-mode governs how the work is done; Engineering Mode and the
independent audit gates decide whether it may ship.

## Governance interaction

The `NOVA governance boundary` section in `SKILL.md` is loaded with the
skill and states the precedence rules. Summary: NOVA's CEO/root
authority, hierarchy, department authority, worker permissions, security
policy, independent audit gates, release gates, data/credential
restrictions, spending controls, and customer/contact controls override
pstack-mode on any conflict. Independent audit overrides builder
self-validation; pstack-mode's verification evidence feeds the auditor
and never replaces the audit.

## Recorded conflicts and limitations

1. **Autonomy guidance vs NOVA approval gates.** pstack-mode's
   "don't block on the human" guidance (Step 3, principle 20, playbooks
   15/17) is broader than NOVA's gates: NOVA requires explicit operator
   approval for financial scope, self-modification, external sends,
   credential access, and customer contact regardless of reversibility.
   Resolution: NOVA gates win; recorded in `SKILL.md`, not silently
   chosen.
2. **Self-verified shipping vs independent audit.** Playbooks 14, 17,
   and 18 describe landing after the builder's own verification. NOVA
   requires independent audit and release gates. Resolution: those
   playbooks stop at NOVA's gates; builder verification is evidence for
   the auditor, not a pass.
3. **Shared 10,000-character skill budget.** The loader caps combined
   auto-activate skill instructions at `MAX_TOTAL_SKILL_INSTRUCTIONS`
   (10,000 chars, `agent/src/skills/loader.ts`). Existing auto-activate
   repo skills inject ~6,570 chars; pstack-mode adds ~6,500
   (body plus description and trust-boundary wrapper). Measured on
   2026-09-18 with the built loader: all three auto-activate skills
   enabled = pstack-mode's section falls past the budget and is
   dropped from the prompt entirely (sections are loaded in directory
   order and the budget stops at the limit); nova-engineering-mode +
   pstack-mode = 7,562 chars, no truncation, full section injected.
   Mitigation today: enable per deployment the skills that deployment
   needs (an engineering deployment enables nova-engineering-mode +
   pstack-mode; distribution-agent is for marketplace customer
   acquisition). Recorded as a limitation; raising the cap would be a
   core-loader change and is deliberately out of scope for this
   integration.
4. **Sibling files are read on demand.** Only `SKILL.md` is injected;
   `principles.md`, `playbooks.md`, and `model-routing.md` are read
   from the skill directory when the process routes to them. Deployments
   that copy only `SKILL.md` get a degraded skill.
5. **Unused frontmatter fields.** `version`, `sticky`, and `triggers`
   are preserved for parity with the package and the other repo skills,
   but the current loader reads only `name`, `description`,
   `auto-activate`, and `requires`.
6. **Optional model routing.** `model-routing.md` is advisory; NOVA's
   `modelStrategy` config and orchestration controls (including
   child-agent limits) decide actual routing. The upstream package also
   excludes pstack's subagent definitions and Slack pack by its own
   design, so there is nothing further to port.
