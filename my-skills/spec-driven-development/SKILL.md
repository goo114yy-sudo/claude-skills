---
name: "spec-driven-development"
description: "Use when starting a non-trivial coding project or feature (new app, meaningful feature, or multi-file change) — write what/why before how, before task breakdown, before code, then re-check the result against the spec."
---

# Spec-Driven Development

Based on GitHub's Spec Kit methodology (github/spec-kit). The core idea: specifications should be treated as the executable source of truth, not throwaway scratch notes. Code is generated *from* a spec and plan, not the other way around. This prevents the common failure mode of jumping straight into code before the actual requirements or architecture have been thought through, which produces work that has to be redone once the real intent surfaces.

## When to apply this

Apply the phase discipline below whenever a request is a new project, a meaningful feature, or any change touching multiple files/components. For a trivial one-off script, a single-line fix, or a quick throwaway request, skip straight to implementation — forcing the full phase sequence on small work is overhead, not rigor.

There is no CLI tool required to follow this — the discipline is the point, not the `specify` command. Apply it directly in the conversation and in the files you create.

## The phases

Work through these in order. Each phase's output feeds the next. Do not start writing implementation code until Specify and Plan are both settled (Tasks can be implicit for small work, but must still exist as a checklist before code starts).

### 1. Principles (once per project, skip for small additions to an existing project)

Before any feature work starts on a new project, establish the ground rules that will govern every decision after this point: code quality bar, testing expectations, UX consistency requirements, performance constraints. Write this down once (e.g. as a short `CONSTITUTION.md` or at the top of the plan) rather than re-deciding it ad hoc for every feature.

### 2. Specify — the what and why

Describe user-facing requirements and desired outcomes. Explicitly do NOT talk about tech stack, libraries, or implementation approach in this phase — that is a leak from Plan into Specify and it narrows thinking prematurely. Capture:
- what the user-facing behavior should be
- why it matters / what problem it solves
- user stories or concrete scenarios, not abstract requirements

Output: a specification — a few paragraphs or a short doc — that someone with zero tech-stack opinions could read and agree "yes, that's what I want built."

### 3. Clarify (as needed)

Before moving to Plan, scan the spec for underspecified areas — ambiguous edge cases, unstated assumptions, missing acceptance criteria. Ask the user directly rather than guessing when the ambiguity would change the architecture or scope. Skip this phase when the spec is already unambiguous.

### 4. Plan — the how

Translate the specification into concrete architecture and technology decisions: stack, libraries, data model, module boundaries, integration points. This is where tech-stack talk belongs — never in Specify.

Output: a technical plan document or section listing the concrete choices and why they were made.

### 5. Tasks — break the plan into actionable units

Decompose the plan into discrete, ideally independently-executable work items. Each task should be concrete enough that "done" is unambiguous. For small work this can be an inline checklist; for larger work, a persisted task list (e.g. TaskCreate, or a TASKS.md).

### 6. Analyze (as needed, before implementation on larger work)

Cross-check the spec, plan, and task list against each other for consistency and coverage gaps before writing code — does every requirement in the spec map to at least one task? Does every task trace back to something in the plan? Skip for small work.

### 7. Implement

Execute the tasks. This is the only phase where code gets written.

### 8. Converge

After implementation, re-assess the actual result against the spec, plan, and task list. Anything in the spec that isn't actually satisfied, or any drift between what was planned and what was built, becomes a new task rather than being silently left undone or silently accepted. Repeat steps 7–8 until the implementation and the original spec are in agreement — this is the loop that keeps AI-generated code from quietly diverging from what was actually asked for over multiple iterations.

## Key principles to carry through every phase

- **Intent before mechanism.** Always establish *what* and *why* before *how*. A plan or task list written before the spec is settled is building on sand.
- **Multi-step refinement, not one-shot generation.** Treat the first pass at a spec, plan, or implementation as a draft to be checked and iterated, not a final answer produced in one go.
- **Don't let phases bleed into each other.** Tech-stack opinions in the spec, or requirement changes discovered mid-implementation without updating the spec, are the two most common ways this discipline breaks down in practice — watch for both.
- **Converge explicitly.** The last step is not "implementation is done" but "implementation matches the spec" — these are different claims, and the gap between them is where bugs and missed requirements hide.