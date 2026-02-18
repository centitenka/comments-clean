---
name: comments-clean
description: >
  Clean and refactor comments in code files. Four modes: conservative removal,
  balanced removal, aggressive removal, and refactor (consolidate/split/simplify).
  Triggered by: clean comments, remove comments, strip comments, comments-clean,
  delete comments, purge comments, refactor comments, 清除注释, 删除注释, 重构注释.
user_invocable: true
---

# Comments Clean

Accurately process comments in code files across a repository. Supports all common programming languages with four modes: three removal levels and one refactor mode.

## Workflow

### Step 1 — Ask Mode

Use AskUserQuestion to ask the user which mode to use:

| Mode | Label | Description |
|------|-------|-------------|
| **Conservative** | 保守删除 | Only remove clearly useless comments (AI-generated, code-restating, commented-out code) |
| **Balanced** | 均衡删除 | Conservative + outdated, obvious, decorative comments |
| **Aggressive** | 激进删除 | Remove everything except comments with proven value |
| **Refactor** | 重构注释 | Consolidate scattered comments, split overloaded ones, simplify verbose ones — no deletion |

### Step 2 — Ask Scope

Use AskUserQuestion to ask the cleaning scope:

- **Entire repository** — scan all code files in the working directory
- **Specific directory** — user provides a directory path
- **Specific files** — user provides file path(s)

### Step 3 — Load Reference

Read the file `references/language-comment-patterns.md` (relative to this skill) to load language comment syntax patterns and the list of functional comments that must never be removed.

### Step 4 — Discover Target Files

Use Glob to find code files in the target scope. Use file extensions to identify language.

**Skip these directories** (always exclude via glob patterns):
`node_modules`, `.git`, `.svn`, `.hg`, `vendor`, `dist`, `build`, `out`, `target`, `.next`, `.nuxt`, `__pycache__`, `.tox`, `.venv`, `venv`, `env`, `.env`, `coverage`, `.nyc_output`, `.cache`, `tmp`, `temp`, `.idea`, `.vscode`, `.vs`, `*.min.js`, `*.min.css`, `*.bundle.js`, `*.map`

**Target file extensions**:
`.js`, `.jsx`, `.ts`, `.tsx`, `.mjs`, `.cjs`,
`.py`, `.pyw`,
`.java`, `.kt`, `.kts`, `.scala`,
`.c`, `.h`, `.cpp`, `.cxx`, `.cc`, `.hpp`, `.hxx`,
`.cs`,
`.go`,
`.rs`,
`.rb`, `.erb`,
`.php`,
`.swift`,
`.dart`,
`.lua`,
`.r`, `.R`,
`.hs`,
`.m` (MATLAB/Objective-C),
`.pl`, `.pm`,
`.sh`, `.bash`, `.zsh`, `.fish`,
`.sql`,
`.html`, `.htm`, `.xml`, `.svg`,
`.css`, `.scss`, `.sass`, `.less`,
`.vue`, `.svelte`,
`.yaml`, `.yml`, `.toml`,
`.ps1`, `.psm1`,
`.vim`,
`.ex`, `.exs`,
`.clj`, `.cljs`

### Step 5 — Process Files

**If mode is Refactor**, for each file:

1. **Read** the file content
2. **Identify** all comments based on the language's comment syntax
3. **Consolidate** adjacent or nearby comments about the same subject into one
4. **Split** comments that cover multiple unrelated topics into focused ones
5. **Simplify** verbose or redundant phrasing within comments
6. **Edit** to apply changes, preserving all factual content and intent
7. **Track** statistics (consolidated count, split count, simplified count)

**If mode is Conservative, Balanced, or Aggressive** (removal modes), for each file:

1. **Read** the file content
2. **Identify** all comments based on the language's comment syntax
3. **Classify** each comment as redundant or valuable per the cleaning level
4. **Edit** to remove redundant comments, handling whitespace correctly
5. **Track** statistics (removed count, kept count, categories)

Process files in batches. Read multiple files in parallel where possible. Use Edit (not Write) to make surgical changes.

### Step 6 — Summary Report

**For Refactor mode**, output:

```
## Comments Refactor Report

**Scope**: [description]

| Metric | Count |
|--------|-------|
| Files scanned | X |
| Files modified | X |
| Comments consolidated | X |
| Comments split | X |
| Comments simplified | X |

### Most Modified Files
1. `path/to/file.ext` — X comments restructured
2. `path/to/file.ext` — X comments restructured
3. ...
```

**For removal modes**, output:

```
## Comments Removal Report

**Cleaning level**: [Conservative/Balanced/Aggressive]
**Scope**: [description]

| Metric | Count |
|--------|-------|
| Files scanned | X |
| Files modified | X |
| Comments removed | X |
| Comments kept | X |

### Removed by Category
- Code-restating comments: X
- Commented-out code: X
- AI-generated comments: X
- [other categories as applicable]: X

### Most Modified Files
1. `path/to/file.ext` — X comments removed
2. `path/to/file.ext` — X comments removed
3. ...
```

---

## Cleaning Level Definitions (Removal Modes)

The three levels are **cumulative** — each higher level includes everything from the levels below it.

### Conservative (保守删除)

Remove ONLY these categories:

1. **AI-generated thinking/reasoning comments** — comments that are clearly artifacts of AI code generation (e.g., `// This function calculates the sum of two numbers`, `# We need to iterate through the list`)
2. **Code-restating comments** — comments that literally restate what the code does with zero additional insight (e.g., `i++ // increment i`, `return result # return the result`)
3. **Exact duplicate comments** — identical comments repeated adjacent or nearby
4. **Commented-out code blocks** — code that has been commented out rather than deleted. Identify by: valid syntax structure, semicolons/braces/keywords, assignment operators, function calls. Do NOT treat English prose as commented-out code.
5. **Auto-generated placeholder comments** — e.g., `// TODO: implement this`, `// Add your code here`, `// Your code goes here`, boilerplate template comments with no real content

### Balanced (均衡删除)

Everything in Conservative, PLUS:

6. **Outdated/contradicting comments** — comments that describe behavior that clearly doesn't match the current code
7. **Meaningless TODO/FIXME** — TODOs with no actionable content (e.g., `// TODO`, `// FIXME`, `// HACK` with no description). TODOs with specific descriptions are kept.
8. **Stating-the-obvious comments** — comments explaining trivial, self-evident code that any developer would understand (e.g., `// constructor`, `// getter`, `// initialize variables`, `// import dependencies`)
9. **Pure decorative separators** — lines that are only visual decoration with no semantic section labeling (e.g., `// =========`, `# --------`, `// ***********`). Lines that include a label like `// === Utils ===` are kept.
10. **Closing brace/tag annotations** — e.g., `} // end if`, `} // end for`, `} // end class Foo`, `<!-- /div -->` when the opening is clearly visible

### Aggressive (激进删除)

Remove ALL comments EXCEPT these categories (which must be kept):

- **API documentation / docstrings** — JSDoc, JavaDoc, PHPDoc, Doxygen, Python docstrings, Rust doc comments, etc. on public interfaces
- **"Why" explanations** — comments explaining the reason behind a non-obvious decision, not the "what"
- **Legal / license headers** — copyright notices, license text, SPDX identifiers
- **Security warnings** — comments about security implications, auth requirements, data sensitivity
- **Workaround / hack explanations** — comments explaining why a workaround exists and what it works around (with context)
- **Complex logic explanations** — regex pattern explanations, algorithm descriptions, mathematical formulas
- **Environment / configuration notes** — deployment requirements, version constraints, platform-specific behavior
- **Functional comments** — ALL items from the "NEVER Remove" section in the reference file

---

## Refactor Mode Definitions

Refactor mode restructures comments **without deleting any** (the only exception is exact duplicates produced by consolidation). The original factual content and intent of every comment must be fully preserved.

### Consolidate (整合)

Merge when:

- Two or more adjacent or nearby comments describe the same subject and would read more clearly as one block
- A function has a leading block comment and scattered inline comments that together explain the same concept
- A comment is fragmented across multiple single-line comments where one multi-line block would be cleaner

How to consolidate: combine the content into a single well-formed comment block at the most logical position (usually above the relevant code). Remove the now-redundant originals.

### Split (拆分)

Split when:

- A single comment covers two or more clearly unrelated topics
- A block comment is so long that it obscures the structure of the surrounding code
- An inline comment has grown to the point where it would read better as a leading block comment

How to split: divide the content into two or more focused comments, each placed immediately above or beside the code it describes.

### Simplify (精简)

Simplify when:

- A comment uses significantly more words than needed to convey its meaning
- A comment contains filler phrases with no informational value (e.g., "It is worth noting that", "Please be aware that", "As you can see", "Obviously", "Simply")
- A comment restates itself or contains redundant qualifiers

How to simplify: rewrite to convey the same meaning concisely. Do not change the factual content.

### Refactor Constraints (CRITICAL)

- **Never delete** comments in refactor mode — only rewrite, merge, or split
- **Never alter** URLs, issue references, version numbers, or technical values inside comments
- **Never touch** functional comments (linter directives, compiler pragmas, shebang lines, etc.)
- **Never rewrite** license headers or legal text
- **Never rewrite** docstrings/JSDoc/JavaDoc on public APIs — these have a required format
- When in doubt whether a simplification preserves intent, **keep the original wording**

---

## Accuracy Rules (CRITICAL)

These rules apply to **all modes**. When in doubt, **keep the comment unchanged**.

### NEVER Remove (Removal Modes)

- **Functional comments** — linter directives (`eslint-disable`, `@ts-ignore`, `# noqa`, `// nolint`, etc.), compiler directives (`#pragma`, `#ifdef`, `//go:build`, etc.), type annotation comments, source map references, shebang lines. See the full list in `references/language-comment-patterns.md`.
- **Comments containing URLs, issue references, or version numbers** — e.g., `// See https://...`, `// Fixes #123`, `// Since v2.3.0`
- **Test intent comments** — comments in test files that describe what is being tested or why
- **Structured documentation comments** — JSDoc `/** */`, Python docstrings `"""..."""`, Rust `///`, etc.
- **Conditional compilation** — `#ifdef`, `#ifndef`, `#if`, `#endif`, `#else`, `#elif`
- **Region markers** — `// #region`, `// #endregion`, `#pragma region`
- **Comments in configuration files** — comments in `.yaml`, `.toml`, `.ini`, `.env` files that explain configuration options

### Classification Principles

- Evaluate multi-line comment blocks as a **single unit** — do not partially delete a block
- A comment that is 60% restating code but 40% explaining "why" should be **kept**
- When classification is ambiguous, **keep the comment** — false negatives (missed removal) are much cheaper than false positives (wrong removal)
- Consider surrounding context — a comment may seem obvious in isolation but provide value in the larger function

---

## Whitespace Handling

- When removing a comment that occupies its own line(s), delete the entire line(s)
- After deletion, if two consecutive blank lines would result, collapse them to one blank line
- When removing an inline/trailing comment, keep the code on that line and trim trailing whitespace
- When consolidating comments in refactor mode, place the merged comment at the position of the first original; remove the blank lines left by the absorbed originals only if doing so does not cause double-blank-lines elsewhere
- Preserve the file's original indentation style (tabs vs spaces) and line endings (LF vs CRLF)
- Do not add or remove blank lines beyond what is needed to avoid double-blank-lines
- If removing a comment leaves an otherwise-empty block (e.g., an empty `if` body), keep the block structure intact

---

## Performance Guidelines

- Process files in parallel batches of up to 10 files using parallel Read calls
- For large repositories (100+ files), provide progress updates every 20-30 files
- If a file has no removable comments, skip it silently (don't report "0 removed")
- Limit Edit operations — batch nearby removals into fewer Edit calls where possible by using larger old_string/new_string spans
