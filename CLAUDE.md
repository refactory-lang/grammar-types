## Project: grammar-types

Language-agnostic type projection library for tree-sitter grammars. Part of the [refactory-lang](https://github.com/refactory-lang) organization. Extracts the generic type machinery from `rust-ir` and parameterizes it over an arbitrary grammar type `G`, enabling typed IR builders for any tree-sitter grammar.

### Architecture

This is a **pure type-level library** -- no runtime code, only TypeScript type definitions.

- **Entry** (`src/index.ts`): All type exports
- **Grammar primitives**: `NodeKind<G>`, `NamedKind<G>`, `TextBrand<K>`
- **Recursive expansion**: `ExpandOneKind`, `ExpandSlot` with cycle detection via visited-set pattern
- **Field extraction**: `FieldMap`, `FieldName`, `FieldInfo`, `RequiredFieldName`, `OptionalFieldName`
- **Node shapes**: `DerivedNodeFields`, `DerivedNodeChildren`, `DerivedNodeShape`
- **Main projection**: `NodeType<G, K>` -- the primary export
- **Builder helpers**: `NodeBuilderInput`, `BuilderConfig`, `BuilderInputValue`
- **Alias map**: `AliasMap<G>` for ergonomic field renames

### Running

```bash
pnpm typecheck     # type check (tsgo)
pnpm lint          # lint (oxlint)
pnpm format        # format (oxfmt)
```

### Key Files

| File | Purpose |
|------|---------|
| `src/index.ts` | All type exports -- the entire public API |
| `package.json` | Package config (ships TS source, no build step) |
| `tsconfig.json` | TypeScript configuration |

### Conventions

- All types are parameterized over a generic grammar type `G` (not hardcoded to any language)
- Package ships TypeScript source directly; consumers must use a TS-aware bundler or the Codemod JSSG runtime
- Uses `pnpm` as package manager
- Type checking via `tsgo` (native TypeScript compiler)
- No runtime dependencies -- this is a pure type-level library

### Speckit Workflow

This repo uses [speckit](https://github.com/speckit) for specification-driven development.

- **Specs**: `specs/<NNN-feature-name>/spec.md` -- feature specifications
- **Plans**: `specs/<NNN-feature-name>/plan.md` -- implementation plans with tasks
- **Checklists**: `specs/<NNN-feature-name>/checklists/` -- quality gates
- **Templates**: `.specify/templates/` -- spec, plan, task, checklist templates
- **Extensions**: `.specify/extensions/` -- verify, sync, review, workflow hooks

**Branch convention**: Feature branches are named `<NNN>-<short-name>` matching the spec directory.

**Issue -> Spec flow**: Issues labeled `ready-to-spec` trigger the `ready-to-spec-notify` workflow, which assigns Copilot to run the speckit workflow and produce a spec + plan + tasks.
