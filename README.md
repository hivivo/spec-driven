# Agentic Spec-driven Development

This repository is the planning and approval layer for an agentic spec-driven project.
Ideas start as GitHub issues, agents turn approved ideas into specs, humans review and merge those specs, and implementation agents may automatically pick up merged specs from `main`.

## What lives here

- Product and feature specs
- Architecture decision records (ADRs)
- Workflow guidance for humans and agents
- GitHub issue and PR templates for moving work forward

This repository is intentionally docs-only. Application code should live in one or more separate implementation repositories.

## Initial GitHub Setup

When you create a repository from this template, complete these GitHub settings once:

1. Create the workflow labels:
   `backlog`, `needs-spec`, `spec-draft`, `approved`, `in-progress`, `implemented`, `blocked`, `decision-needed`
2. Protect `main` and require PR review before merge.

Issue templates, PR templates, repo structure, and agent instructions already come from the template itself.

## Recommended workflow

1. Create a GitHub issue for an idea, bug, feature request, or workflow improvement.
2. Start the issue in `backlog`. When it is ready for drafting, remove `backlog` and apply `needs-spec`.
3. A spec-writing agent creates a branch such as `spec/42-notifications`, drafts a spec file such as `specs/42-notifications.md`, applies `spec-draft`, and removes `needs-spec`.
4. The agent updates the catalogs in `specs/README.md` and `decisions/README.md` when specs or ADRs are added, replaced, or superseded.
5. The agent opens a PR in this repo, links the issue in the PR description, and avoids `Fixes`, `Closes`, or other auto-closing keywords.
6. Before requesting review, make sure the draft is already complete enough for automatic implementation pickup after merge: implementation guidance is present, relevant ADRs and dependencies are linked, and acceptance criteria are concrete.
7. A human reviews the PR, requests changes if needed, and merges it when the spec is approved.
8. After merge, the issue is relabeled to `approved`, `spec-draft` is removed, and the merged spec on `main` becomes the approved implementation contract.
9. An implementation agent may pick up the approved spec in the code repo without a second human prompt, link back to the merged spec and original issue, and mark the issue `in-progress`.
10. When implementation ships, update the issue or implementation PRs with links and status, remove `in-progress`, and move the issue to `implemented`.

## Repository structure

```text
AGENTS.md
README.md
templates/
  adr.md
  spec.md
specs/
decisions/
.github/
  ISSUE_TEMPLATE/
    feature-request.md
  PULL_REQUEST_TEMPLATE.md
```

## GitHub issue labels

Use issue labels to show what stage the work is in:

- `backlog`: captured idea, not ready for drafting yet
- `needs-spec`: ready for a spec draft; this is the main queue for spec-writing agents; remove `backlog` when this is applied
- `spec-draft`: a spec branch or PR exists; remove `needs-spec` when this is applied
- `approved`: the spec PR is merged and implementation may start automatically; remove `spec-draft`
- `in-progress`: implementation has started in a code repo
- `implemented`: implementation is merged or shipped; remove `in-progress`
- `blocked`: waiting on an external dependency or decision
- `decision-needed`: requires explicit human input or an ADR

If you have permission to update labels, keep them current as the work moves. If not, note the intended label change in the PR description or a PR comment.

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
