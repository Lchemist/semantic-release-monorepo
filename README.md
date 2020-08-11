# @lchemist/semantic-release-monorepo

Apply [`semantic-release`](https://github.com/semantic-release/semantic-release)'s automatic publishing to monorepo packages. Forked from [`pmowrer/semantic-release-monorepo`](https://github.com/pmowrer/semantic-release-monorepo).

[![npm package](https://img.shields.io/npm/v/@lchemist/semantic-release-monorepo/latest.svg)](https://www.npmjs.com/package/@lchemist/semantic-release-monorepo)
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)

## Features

- Enable independent versioning on each monorepo package.
- Generate independent release note & git tag for each monorepo package.
- Allow a single GitHub repository to contain many `npm` packages.

## Major Diff

This fork uses `<PACKAGE_NAME>@<PACKAGE_VERSION>` as format of git tag and release note's version title (e.g. `@abc/core@1.2.3`).

## Install

```bash
npm install -D semantic-release @lchemist/semantic-release-monorepo
```

NOTE: `semantic-release` and `@lchemist/semantic-release-monorepo` must be accessible in each monorepo package. (Install both at root directory of monorepo if you use Lerna).

## Usage

In [release config file](https://github.com/semantic-release/semantic-release/blob/master/docs/usage/configuration.md#configuration-file):

```json
{
  "branches": "main",
  "extends": "@lchemist/semantic-release-monorepo"
}
```

With CLI:

```
semantic-release -e @lchemist/semantic-release-monorepo
```

## How

This library modifies the `context` object passed to `semantic-release` plugins in the following way to make them compatible with a monorepo.

Instead of attributing all commits to a single package, commits are assigned to packages based on the files that a commit touched.

If a commit touched a file in or below a package's root, it will be considered for that package's next release. A single commit can belong to multiple packages and may trigger the release of multiple packages.

In order to avoid version collisions, generated git tags are namespaced using the given package's name: `<PACKAGE_NAME>@<PACKAGE_VERSION>`.

| Step             | Description                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `analyzeCommits` | Filters `context.commits` to only include the given monorepo package's commits.                                                                                                                                                                                                                                                                                                                                                  |
| `generateNotes`  | <ul><li>Filters `context.commits` to only include the given monorepo package's commits.</li><li>Modifies `context.nextRelease.version` to use the [monorepo git tag format](#how). The wrapped (default) `generateNotes` implementation uses this variable as the header for the release notes. Since all release notes end up in the same Github repository, using just the version as a header introduces ambiguity.</li></ul> |
