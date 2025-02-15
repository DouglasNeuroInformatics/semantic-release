<!-- PROJECT LOGO -->
<div align="center">
  <a href="https://github.com/DouglasNeuroInformatics/semantic-release">
    <img src="https://raw.githubusercontent.com/DouglasNeuroInformatics/semantic-release/main/.github/assets/dnp-utility-logo.png" alt="Logo" width="100" >
  </a>
  <h3 align="center">@douglasneuroinformatics/semantic-release</h3>
  <p align="center">
    Semantic release configuration and workflows for DNP projects 
  </p>
</div>

<!-- PROJECT SHIELDS -->
<div align="center">

![license](https://img.shields.io/github/license/DouglasNeuroInformatics/semantic-release)
![version](https://img.shields.io/github/package-json/v/DouglasNeuroInformatics/semantic-release)

</div>
<hr />

## About

This is a convenience wrapper around [semantic-release](https://github.com/semantic-release/semantic-release) designed to be used with GitHub Actions. It uses [conventional commit messages](https://www.conventionalcommits.org/en/v1.0.0/) to generate release notes and a changelog. When a release is necessary, it is automatically published to npm, pushing the newly created tag, revised package.json and updated changelog to GitHub.

## Installation

```sh
pnpm add -D @douglasneuroinformatics/semantic-release husky
```

> Note: Husky is an optional peer dependency, and could be replaced with any other toolchain that ensures that conventional commits are followed.

## Configuration

First, setup Husky (if applicable):

```sh
pnpm exec husky init
```

Then, add the following git hook:

**`.husky/commit-msg`**

```sh
#!/bin/sh

pnpm exec commitlint --edit $1
```

Now, add the following configuration:

**`package.json`**

```json
{
  "commitlint": {
    "extends": ["@douglasneuroinformatics/semantic-release/commitlint-config"]
  },
  "release": {
    "extends": ["@douglasneuroinformatics/semantic-release"]
  }
}
```

Next, generate an access token for [npm](https://www.npmjs.com/) with read and write permissions for the package in question and set the `NPM_TOKEN` secret for the repository.

Then, add the following workflow:

**`.github/workflows/release.yaml`**

```yaml
name: Release
on:
  push:
    branches: ['main']
  workflow_dispatch:
permissions:
  contents: read
jobs:
  release:
    permissions:
      contents: write
      issues: write
      pull-requests: write
      id-token: write
    uses: DouglasNeuroinformatics/semantic-release/.github/workflows/release.yaml@main
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
    with:
      build-command: pnpm build # optional
      lint-command: pnpm lint # optional
      test-command: pnpm test # optional
```
