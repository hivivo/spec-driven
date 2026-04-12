# Agent Instructions

This repository is the documentation control plane for the project. Your job here is to turn backlog items into high-quality specs and decisions that humans can review and approve.

## Boundaries

- Do not add application code in this repository.
- Do not treat an unmerged spec as approved work.
- Do not merge your own PRs.
- Do not close backlog issues from a spec PR.
- Do not start implementation from a draft branch, open spec PR, or any unmerged spec.
- Do not update a merged spec on your own unless a human explicitly asks for a docs fix or the change is a purely mechanical fix such as a broken link, wrong file path, or formatting typo.
- Once a spec is merged to `main`, implementation agents may pick it up automatically in the appropriate code repository unless the related issue is labeled `blocked` or `decision-needed`.

## Source of truth

- GitHub issues are the backlog and problem intake layer.
- The GitHub issue number is the stable coordination key for specs, branches, and cross-repo references.
- GitHub issue assignees and PR reviewers carry active ownership and review routing. Do not add generic `owner` metadata to specs.
- `specs/` on `main` contains approved product specs.
- Draft specs may exist on feature branches and in open PRs before approval, but they are not approved until merged to `main`.
- `decisions/` on `main` contains approved architecture decision records.
- Draft ADRs may exist on feature branches and in open PRs before approval, but they are not approved until merged to `main`.
- `main` is the only branch that implementation agents should treat as approved.

## Default workflow

1. Find an issue labeled `needs-spec` or otherwise assigned for drafting.
2. Read the issue, related specs, and relevant ADRs before proposing changes.
3. Use the linked GitHub issue number as the stable identifier for the draft.
4. Copy [`templates/spec.md`](./templates/spec.md) into a new file named `specs/<issue-number>-<slug>.md`.
5. Fill in the template with a clear problem statement, scope, acceptance criteria, risks, and implementation handoff notes.
6. If the spec introduces a cross-cutting technical decision, draft or update an ADR from [`templates/adr.md`](./templates/adr.md) in the same PR, typically named `decisions/<issue-number>-<slug>.md`.
7. Update the catalogs in `specs/README.md` and `decisions/README.md` when specs or ADRs are added, replaced, or superseded.
8. Open a PR against `main` and link the issue in the PR description under a `Related issue` section.
9. Use plain links or `Refs #123`. Do not use `Fixes #123`, `Closes #123`, or `Resolves #123`.
10. Wait for human review and approval.
11. After merge, treat the spec on `main` as the approved implementation contract and as eligible for automatic implementation pickup.
12. After merge, update the issue and implementation PRs as work progresses. Do not revise the merged spec unless the edit is explicitly requested or purely mechanical.

## Branch naming

- Spec branch: `spec/<issue-number>-<slug>`
- ADR branch: `adr/<issue-number>-<slug>`
- Repo hygiene branch: `docs/<short-slug>`

## Spec expectations

Every spec should make it easy for a human reviewer and later implementation agent to answer:

- What problem are we solving?
- Who is it for?
- What is in scope and out of scope?
- What should success look like?
- What are the edge cases, risks, and open questions?
- What should the implementation agent build first?

Prefer a narrower spec with explicit follow-ups over an overly broad spec with vague requirements.

Before requesting review, make sure the spec is already ready for automatic implementation pickup after merge:

- include implementation guidance directly in the spec
- link any relevant ADRs and dependencies
- keep acceptance criteria concrete and testable
- note any follow-up tracking needed in the implementation repo

After merge, treat the spec as immutable by default:

- update the GitHub issue, labels, and implementation PRs as work progresses
- use same-file edits only for human-requested docs fixes or purely mechanical fixes
- if the contract changes, open a new issue and a new spec that uses `supersedes`

## GitHub issue labels

Use issue labels to show what stage the work is in. For the issue tied to your spec:

- `backlog`: captured idea, not ready for drafting yet
- `needs-spec`: ready for a spec draft; this is the main queue for spec-writing agents; remove `backlog` when this is applied
- `spec-draft`: a spec branch or PR exists; remove `needs-spec` when this is applied
- `approved`: the spec PR is merged and implementation may start automatically; remove `spec-draft`
- `in-progress`: implementation has started in a code repo
- `implemented`: implementation is merged or shipped; remove `in-progress`
- `blocked`: work cannot proceed until an external dependency or decision is resolved
- `decision-needed`: a human decision or ADR is needed before the work should continue

If you have permission to update labels, keep them current as the work moves. If you cannot update labels directly, call out the intended label change in the PR description or a PR comment.

GitHub issue labels are the workflow state for this repo.
Keep front matter limited to fields that change routing or cross-repo traceability.

## ADR rules

Create an ADR when a change affects:

- system boundaries
- database or event models
- auth, security, or compliance posture
- API contracts used by multiple features
- deployment or operational strategy

Use the `deciders` field only when there are clear humans or teams whose approval is required for that decision.

If an ADR lists `deciders`, route ADR review to them. If the deciders are unknown, leave the field empty and rely on the normal issue and PR reviewer flow.

If a decision is still unresolved, say so clearly instead of pretending it is settled.

## PR rules

Each PR should contain:

- one primary spec or ADR
- links to the issue and any related docs
- a concise summary of what changed
- a reviewer checklist
- an explicit note about unresolved questions
- enough detail that the merged document can be used immediately by an implementation agent
- updated the manual catalogs when specs or ADRs were added, replaced, or superseded

If a PR mixes unrelated specs, split it.
