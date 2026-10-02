# ts-pnpm-template

A production-ready TypeScript template for Node.js scripts, CLIs, and libraries with comprehensive tooling for code quality, testing, security, and CI/CD.

## Features

- **[TypeScript](https://www.typescriptlang.org)** with strict configuration for Node.js
- **[tsx](https://github.com/privatenumber/tsx)** for fast TypeScript execution in development
- **[Vitest](https://vitest.dev)** for testing with coverage (90% threshold)
- **Code Quality**: Biome formatting, linting, and import organization; Husky and lint-staged
- **Architecture Enforcement**: dependency-cruiser and Knip
- **Security**: Semgrep and Biome recommended lint rules
- **CI/CD**: GitHub Actions with comprehensive PR checks

## Getting Started

### Prerequisites

- Node.js >=24.0.0 <25
- pnpm 12.8.1 (pinned via `packageManager`; Corepack enabled)

### Installation

```bash
pnpm install
```

### Updating packages

```bash
pnpm update --latest
pnpm fix
pnpm check
```

Dependencies use the latest stable releases, including TypeScript 7. Biome replaces ESLint, its plugins, and Prettier with one development dependency licensed under MIT OR Apache-2.0. Its recommended preset is supplemented with explicit error-level equivalents of the former strict ESLint policies; TypeScript, dependency-cruiser, and Semgrep remain separate checks.

pnpm retains its release-age protection, with an approved exception only for `@types/node@26.6.4`. Dependency install scripts for `core-js`, `esbuild`, and `@swc/core` remain denied in `pnpm-workspace.yaml`; no blanket script approval is enabled.

dependency-cruiser uses `@swc/core` (Apache-2.0, development-only) to parse TypeScript 7 source and type imports while preserving path aliases. Its six error-level rules reject circular imports, undeclared packages, unresolved imports, imports of test files, production imports from `src/__tests__`, and runtime imports of development dependencies. Orphan detection is left to Knip; generated warning/info rules and graph-reporter settings are omitted. It still emits a TypeScript-compiler compatibility warning when loading `tsconfig.json`; SWC source scanning is active, and the warning is not suppressed.

Biome checks and formats supported source and configuration files and organizes imports. Markdown and YAML are not formatted by these scripts. VS Code uses the `biomejs.biome` extension.

The lint policy restores cognitive complexity 12, function-body length 80 (excluding blank lines), seven parameters, console restrictions, parent-import restrictions, and available type-aware promise, assertion, readonly, and conversion checks. Former test-file exceptions are retained. Some equivalents are stricter: non-const assertions are banned entirely, string arrays require sort comparators, and function-line counting differs from ESLint.

`.semgrep.yml` supplements Biome with checks for lint suppression comments, `@ts-nocheck`, short single-line `@ts-expect-error` descriptions (minimum 10 characters), concrete-function type queries, pass-through re-exports including types, and todo calls. Re-export/type-query/todo checks retain the former `*.test.ts` exception; the Vitest reporter still rejects skipped/todo tests. Semgrep runs separately in CI, not in `pnpm check`; locally run `semgrep scan --config .semgrep.yml --error .`.

These are best-effort equivalents, not full ESLint parity. Semgrep uses textual checks for re-exports and type queries, which can match code-like strings/comments; the verified Semgrep 1.179 parser does not support `export type *`, but the textual rule still detects it. Expect-error description checking covers single-line comments. Full unsafe-any flow, unbound-method checking, template-expression type restrictions, cyclomatic complexity, abbreviation dictionaries, and specialist regex/ReDoS analysis are not recreated.

### Development

```bash
pnpm dev
```

Runs `src/index.ts` in watch mode with tsx.

### Build

```bash
pnpm build
```

Compiles TypeScript to `dist/` using `tsc`.

### Run

```bash
pnpm start
```

Executes the compiled output from `dist/`.

## Scripts

| Script               | Description                                                                      |
| -------------------- | -------------------------------------------------------------------------------- |
| `pnpm dev`           | Run in development with tsx watch                                                |
| `pnpm build`         | Compile TypeScript to dist/                                                      |
| `pnpm start`         | Run compiled output                                                              |
| `pnpm lint`          | Check formatting, lint rules, and imports in one Biome pass                        |
| `pnpm typecheck`     | Run TypeScript compiler (no emit)                                                |
| `pnpm test`          | Run tests with Vitest                                                            |
| `pnpm test:ci`       | Run tests with coverage                                                          |
| `pnpm test:watch`    | Run tests in watch mode                                                          |
| `pnpm test:coverage` | Run tests with coverage report                                                   |
| `pnpm check`         | **Full quality gate**: Biome, typecheck, tests, depcruise, knip, dupcheck             |
| `pnpm fix`           | Apply safe formatting/lint/import fixes in one Biome pass, then Knip fixes         |
| `pnpm depcruise`     | Check architecture boundaries                                                    |
| `pnpm knip`          | Find unused dependencies/exports                                                 |
| `pnpm dupcheck`      | Check for code duplication                                                       |

## Quality Gates

This repository enforces high standards:

- **Coverage**: 90% per file for statements, branches, functions, and lines
- **No skipped/todo tests**: Vitest strict reporter fails builds
- **Architecture boundaries**: Enforced via dependency-cruiser
- **No code duplication**: jscpd detects copy-pasted code
- **Security**: Semgrep scans for OWASP Top 10, Node.js, TypeScript, and secrets

## GitHub Security Settings

> **Note**: To enable the full security scanning pipeline (Semgrep), you may need to enable **Advanced Security** in your GitHub repository settings.
>
> **Settings** → **Security** → **Code security and analysis** → **GitHub Advanced Security**
>
> This enables:
>
> - Secret scanning
> - Dependency review
> - Code scanning with Semgrep

## CI/CD

Pull requests automatically run:

1. Combined Biome formatting, lint, and import checks; typecheck
2. Vitest with coverage (strict mode)
3. Architecture validation (dependency-cruiser)
4. Unused code detection (knip)
5. Duplicate code detection (jscpd)
6. Security scanning (Semgrep)

All checks must pass before merging.

## AI Agent Guidelines

See [AGENTS.md](./AGENTS.md) for coding rules and conventions when using AI assistants.

## License

MIT
