# CLAUDE.md — operating guide for pkm-syntax

`pkm-syntax` is the extracted **markdown highlighting** layer of `pkm-nvim`
(sibling repo `P:\Active\pkm-nvim`). It highlights any markdown buffer and folds
the frontmatter; it has **no dependency on pkm-nvim** and must stay that way.

## Hard rules

- **Keep `Dependencies: none`.** Never `require('pkm.*')` or reach into note
  state. The whole point of this repo is that the highlighter is self-contained
  and installable on its own. (The roman validator, for example, is duplicated
  here rather than imported.) A dependency on pkm-nvim defeats the extraction.
- **The public API is a contract with pkm-nvim.** `enable(bufnr, highlight_only)`,
  `disable(bufnr)`, `refresh_fold(bufnr)`, `foldtext()`, `setup(opts)`, plus the
  `*_list_pattern` and `_find_*` exports the tests assert. pkm-nvim consumes this
  through its thin `pkm.syntax` facade, so renaming or changing the shape of these
  breaks pkm-nvim — change them in lockstep across both repos. `setup(opts)` takes
  `{ highlight_only?, number? }`; `number = false` hides line numbers (the note
  look), default keeps them. This is standalone-only — pkm-nvim never calls
  `setup()`, so its `enable()`-driven note look (numbers hidden) is unaffected.
- **This is where the highlighting source of truth now lives.** Do not re-add a
  copy of `syntax.lua` or the `queries/` to pkm-nvim — they were removed there on
  purpose (two copies on the runtimepath double-apply the `; extends` query).

## Layout

- `lua/pkm-syntax/init.lua` — the module (patterns, scans, enable/disable, fold).
- `queries/markdown/highlights.scm`, `injections.scm` — tree-sitter queries.

## Verify

```sh
# parse check (zero install, Neovim's own LuaJIT — accepts goto)
nvim --headless --clean -c "lua local f,e=loadfile('lua/pkm-syntax/init.lua'); if not f then print(e) end" -c "qa!"

# static analysis (LuaJIT dialect)
luacheck lua/pkm-syntax/init.lua

# behaviour is covered by pkm-nvim's suite, which loads this repo via
# test/min_init.lua (it prepends ../pkm-syntax to the runtimepath). Run the
# pkm-nvim suite after any change here.
```

## Git

Remote `origin` currently points at `github.com/Vitruvia/pkm-highlight`; the repo
is being renamed to `pkm-syntax` (GitHub auto-redirects). Branch `main`. Commit
here; the author pushes.
