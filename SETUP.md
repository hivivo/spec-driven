# Setup

Use this file when you create a repository from this template.

## Initial GitHub setup

Complete these GitHub settings once:

1. Create the workflow labels:
   `backlog`, `needs-spec`, `spec-draft`, `approved`, `in-progress`, `implemented`, `blocked`, `decision-needed`
2. If you use the bundled `@codex` automation example, set up Codex cloud for the repository and connect GitHub access in Codex.
3. Add the Actions secret `GH_TOKEN` with a personal access token for the GitHub user who should author the `@codex` summon comment.
4. Set Actions workflow permissions to `Read and write` so the example workflow can run normally.
5. Protect `main` and require PR review before merge.

Issue templates, PR templates, repo structure, and agent instructions already come from the template itself.

## Bundled automation example

The repository includes one example workflow in [`.github/workflows/request-spec-draft.yml`](./.github/workflows/request-spec-draft.yml).

That example uses the `needs-spec` label as a trigger:

1. A human removes `backlog` and applies `needs-spec`.
2. A workflow posts a short issue comment that tags `@codex` and points it at `AGENTS.md`, using your GitHub token instead of `github-actions[bot]`.
3. The drafting agent reads the issue, drafts the spec, and uses `gh` for GitHub operations.
4. On success, the drafting agent removes `needs-spec`, adds `spec-draft`, and replies on the issue with the PR link.
5. If the drafting agent cannot proceed, it adds `blocked` and explains the blocker in an issue comment.

This keeps the automation simple: the workflow only triggers the drafting task, and the drafting agent owns the resulting issue state.
