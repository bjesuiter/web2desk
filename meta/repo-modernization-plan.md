# Repo modernization plan (from stable baseline)

Baseline: `master` is currently validated and stable (HEAD `ec3a1c1`).

## Requested upgrades

1. ESLint ➜ Oxlint
2. Jest ➜ Vitest
3. `master` branch ➜ `main`
4. Node version policy update

## Additional upgrades recommended

- **CI actions versions**: `actions/checkout@v2`/`setup-node@v2` and `docker/login-action@v1` are old; upgrade to current major versions.
- **Lockfile strategy**: root lockfile is currently missing; restore deterministic installs (`npm ci`) with a single clear strategy (`package-lock.json` vs `npm-shrinkwrap.json`).
- **TypeScript + typings alignment**: once ESLint constraints are gone, update `typescript` and `@types/node` together.
- **TS compiler targets**: update `src/shared/app` `tsconfig` targets/libs to match the new minimum Node/Electron policy.
- **CI matrix cleanup**: remove Node 16 jobs if dropping support; keep Playwright lane explicit.
- **Branch-name references**: update workflows/docs/scripts from `master` to `main`.

## Recommended order (and why)

1. **Create migration safety point** (tag + short rollback note).
   - Why: fast recovery if a tooling migration breaks CI/publish.

2. **Branch rename first (`master` ➜ `main`) with temporary dual triggers**
   - Update workflow triggers to include both branches briefly, then remove `master`.
   - Why: avoids doing major migrations on an about-to-be-renamed branch and prevents CI blind spots.

3. **Node policy update + lockfile normalization**
   - Update: `engines.node`, `.nvmrc`, `.node-version`, CI matrix, and lockfile.
   - Why: Oxlint/Vitest/modern TS depend on a clear Node baseline; this removes environment drift early.

4. **CI workflow modernization**
   - Upgrade GitHub Actions versions and cache paths after lockfile decision.
   - Why: stabilizes pipeline before changing lint/test frameworks.

5. **ESLint ➜ Oxlint migration**
   - Keep lint scope equivalent first; remove ESLint only after parity.
   - Why: smaller blast radius than test-runner migration and faster feedback loop.

6. **Jest ➜ Vitest migration**
   - Migrate config, mocks, coverage, watch scripts, CI commands.
   - Keep Playwright as-is.
   - Why: highest behavioral risk; do it after runtime/CI/lint are stable.

7. **TypeScript + target tuning pass**
   - Bump TS and `@types/node`; adjust `tsconfig` targets/libs.
   - Why: easiest once lint/test infra is settled; avoids debugging multiple moving parts.

## Definition of done per phase

- Green: `npm run lint`, `npm run test:noplaywright`, `npm run test:playwright` (Windows lane), and CI on `main`.
- No phase merges without passing its CI gates.
