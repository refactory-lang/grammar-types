# @refactory/grammar-types

Language-agnostic type projection from tree-sitter grammars to typed TypeScript IR builders.

## What it does

`grammar-types` takes a tree-sitter grammar's type definitions (published via `@codemod.com/jssg-types`) and projects them into fully typed TypeScript IR node shapes, builder inputs, and builder configs. All field definitions are 100% grammar-derived -- zero hand-rolled types.

The key insight: the type machinery for projecting grammar definitions into typed builders is **language-independent**. Whether you're building a Rust IR, a Go IR, or a Python IR, the same recursive type expansion, cycle detection, field extraction, and builder loosening logic applies. This package extracts and parameterizes that machinery over a generic `Grammar` type parameter `G`.

## How rust-ir uses it

The `rust-ir` package was the original home of this type machinery. With `grammar-types` extracted, rust-ir becomes a thin consumer:

```ts
import type RustTypes from '@codemod.com/jssg-types/langs/rust';
import type { NodeType, BuilderConfig } from '@refactory/grammar-types';

// All Rust IR node types are now one-liners:
type StructItem = NodeType<RustTypes, 'struct_item'>;
type FunctionItem = NodeType<RustTypes, 'function_item'>;

// Builder configs too:
type StructItemConfig = BuilderConfig<RustTypes, StructItem>;
```

## Creating a new language IR

To create an IR for a new language, you only need the grammar types from `@codemod.com/jssg-types`:

```ts
import type GoTypes from '@codemod.com/jssg-types/langs/go';
import type { NodeType, BuilderConfig, AliasMap } from '@refactory/grammar-types';

// Define your alias map for ergonomic field names
type GoAliases = {
  function_declaration: { name: 'identifier' };
} & AliasMap<GoTypes>;

// Project any grammar node into a typed IR node
type GoFunctionDecl = NodeType<GoTypes, 'function_declaration', [], GoAliases>;
type GoStructType = NodeType<GoTypes, 'struct_type'>;

// Builder configs with automatic required/optional inference
type GoFunctionDeclConfig = BuilderConfig<GoTypes, GoFunctionDecl>;
```

## Key types

| Type | Description |
|------|-------------|
| `NodeType<G, K>` | Main export -- projects grammar node kind `K` into a fully typed IR node |
| `NodeKind<G>` | All node kind string literals for grammar `G` |
| `NamedKind<G>` | Named (non-anonymous) node kinds |
| `TextBrand<K>` | Branded string carrying its originating node kind(s) |
| `NodeBuilderInput<G, T>` | Loosened input type for builder factory functions |
| `BuilderConfig<G, T>` | Builder configuration with defaultable fields made optional |
| `AliasMap<G>` | Per-kind field rename overrides for ergonomic API names |
| `ValidationResult` | Result type for tree-sitter parse validation |

## Installation

```bash
pnpm add @refactory/grammar-types
```

## Requirements

- TypeScript 5.9+ (or `tsgo` native preview)
- `@codemod.com/jssg-types` for grammar type definitions
- `type-fest` for utility types
