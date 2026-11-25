# Releasing

opencode-auto-retitle is released with [Changesets](https://github.com/changesets/changesets) and published to the
public npm registry, `https://registry.npmjs.org/`.

## The registry

The npm registry may be set to something else on your machine, for example an internal mirror in
`NPM_CONFIG_REGISTRY`. Every command that talks to npm must name the public registry:

- For `npm` and `pnpm` commands, pass `--registry https://registry.npmjs.org/`.
- For tools that call npm themselves, such as `pnpm changeset publish`, set both spellings of the variable, since an
  existing uppercase variable would otherwise win:

  ```sh
  NPM_CONFIG_REGISTRY=https://registry.npmjs.org/ npm_config_registry=https://registry.npmjs.org/ <command>
  ```

Run plain `npm` commands outside the repository, for example from your home directory. The repository requires pnpm
through `devEngines`, so npm refuses to run inside it.

## Once per machine

Log in to the public registry with pnpm, which `pnpm changeset publish` uses, and check it:

```sh
cd ~ && pnpm login --registry https://registry.npmjs.org/
cd ~ && pnpm whoami --registry https://registry.npmjs.org/
```

Publishing requires two-factor authentication, so keep your authenticator app at hand.

## 1. Describe each change

```sh
pnpm changeset
```

Pick the bump type and write one or two sentences for the changelog. Commit the file in `.changeset/` with the
change. Before 1.0, use `patch` for fixes and `minor` for features and breaking changes.

## 2. Bump the version

```sh
git switch main && git pull
pnpm typecheck
pnpm changeset version
git add -A && git commit -m "chore: release <version>" && git push
```

`changeset version` consumes the files in `.changeset/`, bumps `package.json` and writes `CHANGELOG.md`.

## 3. Publish

With a fresh code from your authenticator app:

```sh
NPM_CONFIG_REGISTRY=https://registry.npmjs.org/ npm_config_registry=https://registry.npmjs.org/ pnpm changeset publish --otp=<code>
git push --follow-tags
```

Changesets publishes the version if it is not on the registry yet and tags it, such as `opencode-auto-retitle@0.2.0`.

## 4. Verify

```sh
cd ~ && npm view opencode-auto-retitle version --registry https://registry.npmjs.org/
```

A brand-new package can take a few minutes to show up. A published version can never be published again, even after
it is unpublished, so fix a broken release with a new patch version.
