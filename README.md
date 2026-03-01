# tree-sitter-razor

[Tree-sitter](https://tree-sitter.github.io) grammar for [Razor](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/razor) templates — the `.razor` and `.cshtml` file format used by ASP.NET Blazor and Razor Pages.

## Features

- Full Razor syntax: directives (`@page`, `@using`, `@inject`, `@model`, `@inherits`, `@implements`, `@attribute`, `@typeparam`, `@namespace`, `@code`, `@functions`), control structures (`@if`, `@else`, `@foreach`, `@for`, `@while`, `@do`, `@switch`, `@try`, `@lock`, `@using`), expressions (`@expr`, `@(expr)`), and comments (`@* ... *@`)
- HTML elements, attributes, void elements, and self-closing tags
- C# content captured as opaque tokens via an external scanner — no dependency on `tree-sitter-c-sharp`
- C# highlighting injected by editors via `queries/injections.scm`

## Usage

### With Zed

This grammar is used by the [zed-razor](https://github.com/dsbraz/zed-razor) extension. Install it from the Zed extension marketplace.

### With Neovim (nvim-treesitter)

```lua
local parser_config = require("nvim-treesitter.parsers").get_parser_configs()
parser_config.razor = {
  install_info = {
    url = "https://github.com/dsbraz/tree-sitter-razor",
    files = { "src/parser.c", "src/scanner.c" },
    branch = "main",
  },
  filetype = "razor",
}
```

### Standalone

```bash
npm install
npm run generate   # regenerate src/ from grammar.js
npm test           # run the corpus tests
```

Requires [tree-sitter-cli](https://github.com/tree-sitter/tree-sitter/tree/master/cli).

## Grammar architecture

Razor mixes HTML and C# in a single file. The grammar handles this through an **external scanner** (`src/scanner.c`) that switches between HTML and C# modes depending on context.

C# content (inside `@code { }`, `@if (expr) { }`, `@(expr)`, etc.) is captured as opaque tokens and re-highlighted by the editor via language injection — keeping the grammar free of a C# dependency.

The `word` property is set to `$.identifier` and all Razor keywords are matched as explicit `seq('@', 'keyword')` sequences, which prevents identifiers like `@forecast` from being mis-tokenized as `@for`.

## Queries

| File | Purpose |
|---|---|
| `queries/highlights.scm` | Syntax highlighting for Razor-specific nodes |
| `queries/injections.scm` | Injects `csharp` into C# content nodes |

## License

MIT