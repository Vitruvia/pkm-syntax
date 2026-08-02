# pkm-syntax

PKM-flavoured **markdown highlighting** for Neovim — the highlighting layer of
[pkm-nvim](https://github.com/Vitruvia/pkm-nvim), extracted so it can highlight
**any** markdown file, be installed on its own, and grow independently.

It has **no dependency** on pkm-nvim (or any note state): drop it into any config
and it highlights markdown. pkm-nvim consumes it as a dependency.

## What it highlights

- **List markers with no tree-sitter node** — the Brazilian legal hierarchy and
  friends, in the same colour as native list markers (`@markup.list`):
  - inciso `I -`, `II -` (uppercase roman + ` - `)
  - alínea `a)`, `b)` (lowercase letter + `)`)
  - artigo `Art. 1º` / `Art. 10`, parágrafo `§ 1º` / `§ 10`
  - subalínea `i.`, `ii.` (lowercase roman + `.`, validated as a canonical roman
    numeral so words like `civil.` are left alone)
- **Citations** — `note[0042]`, `bib[…]`, `journal[…]`, `scratch[…]`.
- **`((meta-comments))`** — double-paren spans, multi-line capable.
- **YAML frontmatter** — subdued injection colours, and the frontmatter block is
  **folded** (`foldmethod=manual`).
- Suppresses the green “indented code” colour for 4-space-indented paragraphs
  (PKM convention: only fenced code is code).

Markers that the markdown grammar already recognises (native bullets, digit
lists) keep their normal tree-sitter highlighting.

## Install

Requires Neovim 0.10+ (bundled markdown tree-sitter parser).

With **lazy.nvim**, standalone:

```lua
{ "Vitruvia/pkm-syntax", ft = "markdown", config = function()
    require("pkm-syntax").setup()          -- highlight every markdown buffer
end }
```

`setup({ highlight_only = true })` skips the frontmatter fold (highlighting only).

When used **through pkm-nvim** you do not call `setup()` — pkm-nvim drives
`enable`/`disable` itself; just list `pkm-syntax` as a dependency of pkm-nvim.

## API

```lua
local syntax = require("pkm-syntax")
syntax.setup(opts)                   -- standalone: highlight all markdown (opts.highlight_only)
syntax.enable(bufnr, highlight_only) -- activate on one buffer (highlight_only skips the fold)
syntax.disable(bufnr)                -- deactivate; restore default syntax
syntax.refresh_fold(bufnr)           -- rebuild the frontmatter fold
syntax.foldtext()                    -- fold display text
```

## Scope

Highlighting and folding for markdown. Further syntax/display features may land
here over time. Note-management (the vault, citations graph, commands) lives in
[pkm-nvim](https://github.com/Vitruvia/pkm-nvim).
