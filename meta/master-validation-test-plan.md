# Master validation test plan

This repo is Nativefier: a Node/TypeScript CLI plus an Electron app wrapper.

## Goal

Validate that the `master` branch is working in a way that matches the project's own CI and smoke-test expectations.

## Key facts

- Primary branch: `master`
- CI workflow: `.github/workflows/ci.yml`
- CI validates:
  - lint on Node 20
  - tests on Node 16 and Node 20
  - Linux, macOS, and Windows
  - Playwright only on Windows
- Manual smoke test script: `.github/manual-test`

## Recommended validation flow

1. Use a supported Node version first.
   - Prefer Node 20 for the main validation pass.
   - Also run a compatibility pass on Node 16.
   - Do not treat Node 24 as the source of truth because the project does not officially test it.

2. Start from clean `master`.

   ```bash
   git checkout master
   git pull --ff-only origin master
   npm ci
   ```

3. Run the same core checks as CI.

   ```bash
   npm run lint
   npm run test:noplaywright
   ```

4. Run Playwright where it matches CI best.
   - Best on Windows.

   ```bash
   npm run test:playwright
   ```

5. Run the manual smoke test.

   ```bash
   npm run test:manual
   ```

   Validate at least:
   - generated app launches
   - tray behavior works
   - context menu works
   - no Electron runtime or deprecation errors
   - screen-share flow works where supported

6. Final confidence check.
   - Strongest signal that `master` works:
     - local Node 20 pass
     - local manual smoke-test pass on your OS
     - green CI on `master`

## Minimum practical path

If you only want a short local verification pass:

```bash
fnm use 20
npm ci
npm run lint
npm run test:noplaywright
npm run test:manual
```

## Notes

- `.nvmrc` remains at Node 16 because the project still documents and CI-tests that as the minimum supported version.
- `.node-version` is set to Node 20 so tools like `fnm` can auto-load the preferred day-to-day development version.

## Current master validation state (local)

- Forced TypeScript to `5.1.x` to satisfy ESLint.
- Ran `npm i` instead of `npm ci` because the lockfile needed to be updated.
- `npm run lint` works.
