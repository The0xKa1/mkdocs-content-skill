# mkdocs-content

`mkdocs-content` is a Codex skill for editing Markdown in MkDocs repositories.

It tells Codex how to:

- format or rewrite Markdown pages with consistent spacing and math rules
- create new docs pages from raw Markdown
- update `mkdocs.yml` navigation with minimal, safe edits
- avoid broad YAML rewrites or destructive automation

This repository intentionally ships as a pure instruction skill. It does not include formatter scripts.

## What The Skill Does

When invoked, the skill tells Codex to:

1. identify the target Markdown file and `mkdocs.yml`
2. detect `docs_dir`
3. normalize Markdown structure with minimal churn
4. handle list spacing and math formatting explicitly
5. register the page in `nav` only if it is missing
6. preserve existing `mkdocs.yml` hierarchy, comments, and style whenever possible

## Installation


## Usage

Ask Codex to use the skill explicitly:

```text
Use $mkdocs-content to format docs/guide/intro.md and update mkdocs.yml if needed.
```

```text
Use $mkdocs-content to save this Markdown as docs/tutorials/setup.md and register it in the nav.
```

## Design Notes

- The skill is instruction-only on purpose.
- `mkdocs.yml` is treated as hand-maintained configuration.
- If navigation structure is ambiguous, Codex should stop and explain the ambiguity instead of forcing a rewrite.

## Publishing To GitHub

Before publishing, consider adding:

- a license file if you want reuse terms to be explicit
- repository topics such as `codex-skill`, `mkdocs`, `markdown`, and `documentation`
- a short repository description matching the skill summary
