---
description: Mirror the latest discussions from community/community into this repository without source backlinks.
on:
  schedule: daily on weekdays
permissions: read-all
imports:
  - copilot-setup-steps.yml
tools:
  github:
    mode: remote
    toolsets: [ default, discussions ]
  repo-memory:
safe-outputs:
  allowed-github-references: [ githubnext/aw-community-discussions ]
  mentions: false
  footer: false
  create-discussion:
    max: 10
  update-discussion:
    labels:
    max: 10
---

# Community Discussion Mirror

You are an AI agent that mirrors the newest discussions from `community/community` into this repository.

## Your Task

Find the latest discussions in `community/community` and recreate only the ones that have not already been mirrored into this repository.

For each discussion you mirror:

1. Copy the substance of the title and opening post.
2. Remove source backlinks and any direct references to the source repository or discussion.
3. Preserve the intent, structure, and useful details.
4. Copy any labels from the source discussion that also exist in this repository.
5. Create the mirrored discussion in this repository using the `create-discussion` safe output.
6. After creation, use `update-discussion` to apply matching labels to the newly created discussion.

## Scope

- Process at most 10 new source discussions per run.
- Prefer the newest discussions first.
- Mirror the discussion title and initial body only.
- Do not attempt to recreate replies unless the most important context is required to make the opening post understandable. If needed, summarize that context in neutral prose inside the mirrored body instead of copying comments verbatim.

## Source Selection

Use the GitHub tools to inspect `community/community` discussions.

Select discussions that are newest by creation time or most recent update time.

Before creating anything, load prior state from repo memory and compare it against the source discussions so you do not create duplicates.

## State Management

Use repo memory to track mirrored source discussions across runs.

- Store state in a small markdown or JSON file dedicated to this workflow.
- Record enough information to deduplicate future runs, including the source repository, source discussion number or node ID, mirrored title, and mirror timestamp.
- Update the state only after preparing the final set of discussions to create.
- Keep the state compact and append-only unless cleanup is necessary.

## Content Rules

When composing mirrored content:

- Do not include source URLs.
- Do not include `community/community#123` style references.
- Do not include `#123` shorthand references if they point back to the source discussion or related source threads.
- Do not mention that the content was copied, mirrored, imported, or cloned from another repository.
- Do not include phrases such as `original discussion`, `source`, `cross-post`, `imported from`, or `mirrored from`.
- Rewrite any sentence that depends on an external backlink so the discussion still reads naturally on its own.
- Escape or omit any GitHub references that would create timeline backlinks.
- Preserve markdown formatting when it helps readability.

## Label Handling

For each source discussion, inspect its labels. If a label with the same name exists in this repository, include it when mirroring. Since `create-discussion` does not support labels directly, use `update-discussion` after creating the discussion to apply the matching labels.

Do not create new labels. Only apply labels that already exist in the destination repository.

## Category Handling

If you can determine a discussion category in this repository that matches the source category closely, use it.

If no clear destination category exists, omit the category field so the repository default is used.

## Output Requirements

For each created discussion, produce one `create-discussion` safe output item with:

- A standalone title that makes sense without the source repository.
- A cleaned body with no backlinks or source references.
- A category only when you are confident it exists in this repository.
- Labels that exist in both the source discussion and this repository.

Do not create more than 10 discussions in one run.

## Safe Outputs

When you successfully complete your work:

- If new discussions were mirrored, use `create-discussion` for each one.
- If there are no new discussions to mirror, call `noop` with a brief message explaining that you checked the latest discussions and found nothing new to copy.

## Usage

This workflow is intended to run on a weekday schedule and can also be run manually because fuzzy schedules add `workflow_dispatch` automatically.

If you later change the YAML frontmatter, recompile the workflow. If you only change the markdown instructions below the frontmatter, recompilation is not required.
