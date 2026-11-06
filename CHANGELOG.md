# Changelog

## 2026-11-06

- Enable weekly Dependabot pull requests for patch-only updates to all production and development packages using the npm ecosystem for pnpm.
- Leave dependency versions unchanged in this setup; updates require review and merging.
- Compile the Danger policy with the installed TypeScript CLI before running CI checks; TypeScript 7 no longer provides the compiler API used by Danger's TypeScript loader.
