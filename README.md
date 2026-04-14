# Agentic Spec-driven Development

This repository is the planning and approval layer for an agentic spec-driven project.
Ideas start as GitHub issues, agents turn approved ideas into specs, humans review and merge those specs, and implementation agents may automatically pick up merged specs from `main`.

## What lives here

- Product and feature specs
- Architecture decision records (ADRs)
- Workflow guidance for humans and automation
- GitHub issue and PR templates for moving work forward
- Runtime instructions for drafting agents

This repository is intentionally docs-only. Application code should live in one or more separate implementation repositories.

## Recommended workflow

1. Create a GitHub issue for an idea, bug, feature request, or workflow improvement.
2. Start the issue in `backlog`.
3. When the issue is ready for specification, move it into your chosen drafting flow. That can be manual or automation-driven.
4. A drafting agent creates a branch such as `spec/42-notifications`, drafts the spec, updates the catalogs when needed, and opens a PR in this repo.
5. The spec PR links the issue without closing it.
6. A human reviews the PR, requests changes if needed, and merges it when the spec is approved.
7. After merge, the issue is relabeled to `approved`, and the merged spec on `main` becomes the approved implementation contract.
8. An implementation agent may pick up the approved spec in the code repo without a second human prompt, link back to the merged spec and original issue, and mark the issue `in-progress`.
9. When implementation ships, update the issue or implementation PRs with links and status, remove `in-progress`, and move the issue to `implemented`.

## Automation

This repository includes a lightweight drafting workflow in [`.github/workflows/request-spec-draft.yml`](./.github/workflows/request-spec-draft.yml).

When `needs-spec` is applied to an issue, the workflow posts a short `@codex` comment that points the drafting agent at `AGENTS.md`. The drafting agent creates the spec PR, updates issue labels, and replies on the issue with the PR link.

This keeps the automation narrow: the workflow triggers the drafting task, and the drafting agent owns the resulting issue state.

## Repository structure

```text
AGENTS.md
README.md
SETUP.md
templates/
  adr.md
  spec.md
.github/
  ISSUE_TEMPLATE/
    feature-request.md
  workflows/
    request-spec-draft.yml
  PULL_REQUEST_TEMPLATE.md
specs/
decisions/
```

## GitHub issue labels

Use issue labels to show what stage the work is in. Humans, workflows, and drafting agents may change labels when they are responsible for the transition.

- `backlog`: captured idea, not ready for drafting yet
- `needs-spec`: ready for a spec draft; remove `backlog` when this is applied
- `spec-draft`: a spec branch or PR exists; remove `needs-spec` when this is applied
- `approved`: the spec PR is merged and implementation may start automatically; remove `spec-draft`
- `in-progress`: implementation has started in a code repo
- `implemented`: implementation is merged or shipped; remove `in-progress`
- `blocked`: work cannot proceed until an external dependency or decision is resolved; this can temporarily coexist with `needs-spec` or `in-progress`
- `decision-needed`: requires explicit human input or an ADR; this can coexist with another workflow label

GitHub issue labels are the workflow state for this repo. Do not add or rely on document-level `status` fields in spec or ADR front matter.
GitHub issue assignees and PR reviewers carry active ownership. Specs should not add a generic `owner` field.
Keep front matter limited to fields that change routing or cross-repo traceability.

## File conventions

### Specs

- Use the GitHub issue number as the stable spec identifier
- Store specs in `specs/<issue-number>-<slug>.md`
- Start from [`templates/spec.md`](./templates/spec.md)
- Use one spec per meaningful deliverable or tightly related slice of work
- Treat `specs/` on `main` as the approved-spec catalog
- Draft specs should exist only on feature branches and in open PRs before approval
- Use `implementation_repo` only when the target code repository is not obvious or there are multiple possible implementation repos
- Use `supersedes` only when this spec replaces an older merged spec
- Before requesting review, make sure the draft is ready for automatic implementation pickup after merge
- After merge, treat the spec as immutable by default
- Same-file edits are only for human-requested docs fixes or purely mechanical fixes such as broken links, wrong file paths, or formatting typos
- If the contract changes, open a new issue and a new spec that uses `supersedes`
- Refresh the spec catalog in `specs/README.md` whenever specs are added, replaced, or superseded
- Keep open questions explicit so reviewers can resolve them in the PR

### ADRs

- Use the related GitHub issue number as the default ADR identifier when one exists
- Store ADRs in `decisions/<issue-number>-<slug>.md`
- Start from [`templates/adr.md`](./templates/adr.md)
- Write an ADR when a choice affects architecture, data model, security, operability, or multiple specs
- Use `deciders` only when specific people or teams must approve the ADR; if listed, they are the right review targets for ADR changes
- Use `related_specs` to point at the specs the ADR governs
- Use `supersedes` only when this ADR replaces an older merged ADR
- Treat `decisions/` on `main` as the approved-ADR catalog
- Draft ADRs should exist only on feature branches and in open PRs before approval
- Refresh the ADR catalog in `decisions/README.md` whenever ADRs are added, replaced, or superseded

## Linking rules

Keep a complete chain of references:

- Issue -> spec PR
- Spec PR -> issue
- Merged spec -> issue and implementation repository
- Implementation PR -> merged spec and issue

The important detail is that spec PRs should link issues without closing them. The issue should stay open until implementation is actually complete, even if implementation starts automatically after the spec merges.
If your tooling supports manually linking a PR in the issue's Development section, use that. Otherwise include `Refs #<issue-number>` in the PR body so the relationship is still visible.

## Suggested branch and PR naming

- Spec branch: `spec/<issue-number>-<slug>`
- Spec PR title: `Spec: <short title>`
- ADR branch: `adr/<issue-number>-<slug>`
- ADR PR title: `ADR: <short title>`

## Review model

Humans approve scope and intent. Agents draft and refine. Once a spec is merged, implementation agents may start automatically from that merged spec on `main`, not from comments on an unmerged PR branch.

## Future improvements

Useful additions once the repo is in use:

- A GitHub Action that checks spec front matter and filename conventions
- Automatic label suggestions for spec PRs
- Cross-repo automation that opens implementation tasks from merged specs
