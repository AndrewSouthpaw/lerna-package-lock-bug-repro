# Bug repro

## Repro

Notice how `zod@^3.2.0` is specified in `foo/package.json`. We have `zod@3.9.8` currently installed in `node_modules`. Our `foo/package-lock.json` specifies `zod@3.3.4`.

1. `npx lerna bootstrap`

**Expected result**: `zod@3.3.4` is installed into `foo/node_modules`. This is what would happen if you ran `npm i` in `foo`.

**Actual result**: No change; `zod@3.9.8` remains in `foo/node_modules`

## Further discussion

You can get the expected result if you *delete first* `foo/node_modules` and then run bootstrap. It seems like lerna is erroneously skipping the check of `package-lock.json` because it detects that the currently installed package at least satisfied `package.json`. By removing the installed package, it then correctly reads `package-lock.json`.

## Setup

These were the steps to create this repo.

1. `npx lerna init`
1. `npx lerna add foo`
1. `cd packages/foo && npm i zod@^3.2.0`
1. Have `zod@^3.2.0` specified in `packages/foo/package.json`.
1. Manually overwrite `foo/package-lock.json` entry for `zod` to this, another valid package:

```
{
  "name": "foo",
  "version": "0.0.0",
  "lockfileVersion": 1,
  "requires": true,
  "dependencies": {
    "zod": {
			"version": "3.3.4",
			"resolved": "https://registry.npmjs.org/zod/-/zod-3.3.4.tgz",
			"integrity": "sha512-g5V20IUn3QBpbMKjZxKp58ocUOO9/L0OlJ5+rl84QSeN6tK5ea/yQboYA59t2D8Puc/qVyz4YflhCgqR2uMtaA=="
		}
  }
}
```
