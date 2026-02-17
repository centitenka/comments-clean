# Language Comment Patterns Reference

## Comment Syntax by Language Family

### C-Family (`//` and `/* */`)

**Languages**: C, C++, Java, C#, Go, Rust, JavaScript, TypeScript, Kotlin, Swift, Dart, Scala, Objective-C

```
// single-line comment
/* multi-line comment */
```

**Rust doc comments**:
```rust
/// outer doc comment
//! inner doc comment (module-level)
```

**Go build tags**:
```go
//go:build linux
//go:generate ...
//go:embed ...
```

**Swift/Kotlin doc comments**:
```swift
/// single-line doc comment
```

### Python (`#` and `"""`)

```python
# single-line comment
"""docstring (triple double-quotes)"""
'''docstring (triple single-quotes)'''
```

### Shell / Bash / Zsh (`#`)

```bash
# single-line comment
```

**Shebang (NEVER remove)**:
```bash
#!/bin/bash
#!/usr/bin/env python3
```

### Ruby (`#` and `=begin/=end`)

```ruby
# single-line comment
=begin
multi-line comment
=end
```

### HTML / XML (`<!-- -->`)

```html
<!-- comment -->
```

**Conditional comments (IE, NEVER remove)**:
```html
<!--[if IE 9]>...<![endif]-->
```

### CSS / SCSS / LESS (`/* */` and `//`)

```css
/* CSS comment */
```

```scss
// SCSS/LESS single-line comment
/* multi-line comment */
```

### SQL (`--` and `/* */`)

```sql
-- single-line comment
/* multi-line comment */
```

### Lua (`--` and `--[[ ]]`)

```lua
-- single-line comment
--[[ multi-line
     comment ]]
```

### PHP (`//`, `#`, `/* */`)

```php
// single-line comment
# single-line comment
/* multi-line comment */
/** PHPDoc comment */
```

### R (`#`)

```r
# single-line comment
```

### Haskell (`--` and `{- -}`)

```haskell
-- single-line comment
{- multi-line comment -}
```

### MATLAB (`%` and `%{ %}`)

```matlab
% single-line comment
%{
multi-line comment
%}
```

### Perl (`#` and `=pod/=cut`)

```perl
# single-line comment
=pod
documentation
=cut
```

### YAML (`#`)

```yaml
# comment
```

### TOML (`#`)

```toml
# comment
```

### INI / Config (`#` and `;`)

```ini
# comment
; comment
```

### Vim Script (`"`)

```vim
" comment
```

### PowerShell (`#` and `<# #>`)

```powershell
# single-line comment
<# multi-line comment #>
```

---

## Functional Comments — NEVER Remove

These comments have semantic meaning to tools, compilers, or runtimes. Removing them changes program behavior.

### Linter / Formatter Directives

```
// eslint-disable-next-line
// eslint-disable-line
/* eslint-disable */
/* eslint-enable */
// prettier-ignore
// tslint:disable-next-line
// @ts-ignore
// @ts-expect-error
// @ts-nocheck
// @ts-check
# noqa
# noqa: E501
# type: ignore
# type: ignore[assignment]
# pylint: disable=...
# pylint: enable=...
# flake8: noqa
# isort: skip
# isort: skip_file
# fmt: off / # fmt: on
# yapf: disable / # yapf: enable
# rubocop:disable / # rubocop:enable
// nolint
// nolint:govet
//noinspection (JetBrains IDEs)
/* c8 ignore next */
/* istanbul ignore next */
/* v8 ignore next */
// biome-ignore
```

### Compiler / Build Directives

```
#pragma once
#pragma pack(...)
#pragma warning(...)
#pragma omp ...
#ifdef / #ifndef / #endif / #else / #elif
#if defined(...)
#define / #undef
#include
#import (Objective-C)
//go:build
//go:generate
//go:embed
//go:noinline
//go:nosplit
//go:linkname
// +build (legacy Go)
// #cgo
#![allow(...)] / #![deny(...)] (Rust attributes — not comments but look similar)
// SAFETY: (Rust unsafe justification — keep by convention)
// swift-tools-version:
// swiftlint:disable / // swiftlint:enable
@_silgen_name (Swift)
```

### Type Annotation Comments

```
# type: (Python 2 type comments)
/** @type {Type} */ (JSDoc in JS files used by TS)
/** @param {Type} name */
/** @returns {Type} */
/** @typedef */
/** @template */
/* :: type */ (Flow)
/* : type */ (Flow)
```

### Source Map / Encoding Directives

```
//# sourceMappingURL=...
//@ sourceMappingURL=... (deprecated form)
# -*- coding: utf-8 -*-
# coding: utf-8
# vim: set ft=... :
/* vim: ... */
```

### Region Markers (IDE folding)

```
// #region / // #endregion
// <editor-fold> / // </editor-fold>
# region / # endregion
#pragma region / #pragma endregion
```

### Framework / Tool-Specific

```
// @flow
// @ts-check
/* @jsx */
/* @jsxImportSource */
/* @vite-ignore */
/* webpackChunkName: "..." */
/* webpackIgnore: true */
// next-line-ignore
// @refresh reset (React Fast Refresh)
# frozen_string_literal: true (Ruby)
# encoding: utf-8 (Ruby)
# magic comment (Ruby)
```

### Documentation Generators (structured doc comments)

These should be treated as **structured documentation**, not regular comments:

```
/** ... */ (JSDoc, JavaDoc, PHPDoc, Doxygen)
/// ... (Rust doc, Swift doc, C# XML doc)
//! ... (Rust inner doc)
"""...""" (Python docstring)
'''...''' (Python docstring)
=pod / =cut (Perl POD)
=begin rdoc / =end (Ruby RDoc)
## @param (Doxygen in Python/Shell)
```

### Shebang Lines

```
#!/bin/bash
#!/usr/bin/env node
#!/usr/bin/env python3
#!/usr/bin/perl
```

These are executable directives — NEVER remove.
