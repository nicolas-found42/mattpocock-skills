# JS/TS: Husky + lint-staged

Replaces steps 3 and 4 of [SKILL.md](./SKILL.md) for a pure JS/TS repo.

## 3. Install

Detect the package manager from the lockfile: `package-lock.json` (npm), `pnpm-lock.yaml` (pnpm), `yarn.lock` (yarn), `bun.lock`/`bun.lockb` (bun). Default to npm. Install as devDependencies:

```
husky lint-staged prettier
```

Then initialise Husky, which creates `.husky/` and adds `"prepare": "husky"` to `package.json`:

```bash
npx husky init
```

## 4. Configure

`.husky/pre-commit` (Husky v9+ needs no shebang):

```
npx lint-staged
npm run typecheck
npm run test
```

Swap `npm` for the detected package manager. Include a `typecheck` or `test` line only when `package.json` has that script, and tell the user which ones were missing.

`.lintstagedrc` (`--ignore-unknown` skips files Prettier cannot parse, such as images):

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

`.prettierrc`, only when no Prettier config exists:

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

Done when `.husky/pre-commit` is executable, `.lintstagedrc` exists, `prepare` is `"husky"`, and a Prettier config exists.
