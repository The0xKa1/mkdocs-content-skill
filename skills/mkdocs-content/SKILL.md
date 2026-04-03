---
name: mkdocs-content
description: Format or create Markdown content in MkDocs repositories and update `mkdocs.yml` navigation with minimal safe edits. Use when Codex needs to rewrite a docs page, save user-provided Markdown into a MkDocs docs directory, normalize Markdown style for MkDocs compatibility, or register a page in the MkDocs nav without disturbing unrelated YAML structure or comments.
---

# MkDocs Content

Edit repository files directly. Do not rely on bundled scripts for formatting or navigation updates.

## Required Inputs

Accept either:

- A path to an existing Markdown file.
- Raw Markdown plus the target file path.

Before editing, identify:

- The target Markdown file.
- The repository `mkdocs.yml`.
- `docs_dir` from `mkdocs.yml`, or `docs/` if omitted.

If the target file is outside `docs_dir`, stop and tell the user instead of guessing how it should be registered in navigation.

## Workflow

1. Read the target Markdown file if it already exists.
2. Read `mkdocs.yml` before making nav changes.
3. Rewrite the Markdown to match the rules below.
4. Save the Markdown file.
5. Update `mkdocs.yml` only if the page is missing from `nav`.
6. Report what changed, including whether the Markdown body changed and whether `mkdocs.yml` changed.

## Markdown Rules

Apply these rules pragmatically. Improve consistency, but do not rewrite correct content just to make cosmetic churn.

### Spacing and Paragraphs

- Keep one blank line between paragraphs.
- Keep one blank line before lists.
- Keep one blank line after a list before the next normal paragraph.
- Do not insert extra blank lines between adjacent list items in the same list.
- Do not force a blank line between a list item and its indented continuation text, nested list, quote, or code block.
- Keep one blank line before and after headings when that does not break surrounding Markdown structures.
- Prefer one paragraph per line. Remove needless manual line breaks inside normal prose.

### Headings

- Use a space after heading markers: `# Title`.
- Keep exactly one H1 when practical.
- Do not introduce skipped heading levels unless the existing document already uses them and changing them would be risky.

### Emphasis

- Use `**bold**` for bold text.
- Keep surrounding spaces when bold text sits inline with normal words.

### Lists

- Use `-` for unordered lists unless the existing local list intentionally uses ordered items.
- Use consistent indentation for nested lists, normally 2 spaces.
- Keep list markers and indentation stable inside existing complex content.
- When prose transitions into a list, ensure there is a blank line before the first list item unless the surrounding Markdown construct forbids it.
- When a list transitions back into prose, ensure there is a blank line before the next normal paragraph.
- Treat tables, block quotes, and fenced code blocks as separate block structures; do not add paragraph/list spacing that would break them.

Example:

```markdown
This paragraph introduces the list.

- Item one
- Item two

This paragraph resumes after the list.
```

Avoid:

```markdown
This paragraph introduces the list.
- Item one
- Item two
This paragraph resumes after the list.
```

### Code Blocks

- Use fenced code blocks.
- Add a language tag when it is clear from context.
- Do not rewrite code samples unless formatting requires it.

### Links and Images

- Use normal Markdown link and image syntax.
- Avoid bare URLs when a normal link is reasonable.

### Mixed Chinese and English

- Add readable spacing between Chinese and English terms when it improves readability and does not alter literal syntax.

### Math

Follow the project’s stated math style strictly:

- Use `$$ ... $$` for inline math, on a single line, with surrounding spaces.
- Use `\[ ... \]` for display math, on separate lines, with blank lines before and after.
- Use environments such as `aligned` for multi-line display math.
- Do not introduce `$...$`.
- Do not use `$$...$$` for display math blocks.
- Keep inline math entirely on one physical line. If the expression needs line breaks, convert it to display math.
- Inside display math, keep `\[` and `\]` on their own lines.
- Use one expression line for simple display math.
- For multi-line display math, use an explicit LaTeX environment such as `aligned`, `gathered`, or `cases`; put each logical equation line on its own line inside that environment.
- Do not insert blank lines inside a display math block unless the LaTeX environment specifically requires them.
- Keep surrounding prose outside the display math block; do not leave trailing sentence text on the same line as `\[` or `\]`.

Do not touch math inside fenced code blocks.

Example:

```markdown
We minimize $$ L = \|x - y\|^2 $$ during training.

\[
\begin{aligned}
f(x) &= ax + b \\
g(x) &= cx + d
\end{aligned}
\]

This paragraph continues after the display math.
```

Avoid:

```markdown
We minimize
$$ L = \|x - y\|^2 $$
during training.

\[ f(x) = ax + b \]
```

## Navigation Update Rules

Treat `mkdocs.yml` as hand-maintained configuration. Make the smallest possible edit.

### Before Editing `nav`

- Preserve existing hierarchy, ordering style, and labels when possible.
- Do not duplicate an entry if the target path is already present anywhere in `nav`.
- Prefer matching the surrounding nav pattern over inventing a new one.

### Derive the Nav Entry

- Compute the file path relative to `docs_dir`.
- Use the first H1 as the page title when available.
- Otherwise derive the title from the filename.

### Insert Strategy

- If an obvious existing section matches the file’s directory, add the page there.
- If no obvious section exists, add a minimal new entry that follows the current nav style.
- If the existing nav is too custom or ambiguous to update safely, stop and tell the user what is ambiguous instead of forcing a rewrite.

### YAML Safety

- Avoid broad reformatting of `mkdocs.yml`.
- Preserve comments and unrelated formatting whenever possible.
- Change only the lines required to register the new page.

## Guardrails

- Do not modify unrelated files.
- Do not reorder large nav sections just to make them look cleaner.
- Do not claim a rule was enforced if you did not actually verify it in the file.
- Prefer safe manual edits over clever automation.

## Output Expectations

After finishing, summarize:

- Which Markdown file was created or edited.
- Whether content formatting changed.
- Whether `mkdocs.yml` changed.
- Any rule you intentionally left unchanged because the existing document structure was ambiguous or risky to normalize.
