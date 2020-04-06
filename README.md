# Automatic Releases with Custom Message

This action simplifies the GitHub release process by automatically uploading assets, generating changelogs, handling pre-releases, and so on. It includes a custom message placed before the generated changelog.

## Contents

1. [Usage Examples](#usage-examples)
1. [Supported Parameters](#supported-parameters)
1. [Event Triggers](#event-triggers)
1. [License](#license)

> **NOTE**: This is a fork of _GitHub Automatic Releases_ from [marvinpinto/actions](https://github.com/marvinpinto/actions) to customise it with a release message before the changelog.

> **NOTE**: The tomlinscote/action-automatic-releases repository is an automatically generated mirror of the tomlinscote/actions monorepo containing this and other actions.

## Usage Examples

### Automatically generate a pre-release when changes land on master

This example workflow will kick in as soon as changes land on `master`. After running the steps to build and test your project:

1. It will create (or replace) a git tag called `latest`.
1. Generate a changelog from all the commits between this, and the previous `latest` tag.
1. Generate a new release associated with the `latest` tag (removing any previous associated releases).
1. Update this new release with the specified title (e.g. `Development Build`).
1. Add the message "My Message" to the release before the changelog.
1. Upload `LICENSE.txt` and any `jar` files as release assets.
1. Mark this release as a `pre-release`.

```yaml
---
name: "pre-release"

on:
  push:
    branches:
      - "master"

jobs:
  pre-release:
    name: "Pre Release"
    runs-on: "ubuntu-latest"

    steps:
      # ...
      - name: "Build & test"
        run: |
          echo "done!"

      - uses: "tomlinscote/action-automatic-releases@latest"
        with:
          repo_token: "${{ secrets.GITHUB_TOKEN }}"
          automatic_release_tag: "latest"
          prerelease: true
          title: "Development Build"
          message: "My Message"
          files: |
            LICENSE.txt
            *.jar
```

### Create a new GitHub release when tags are pushed to the repository

Similar to the previous example, this workflow will kick in as soon as new tags are pushed to GitHub. After building & testing your project:

1. Generate a changelog from all the commits between this and the previous [semver-looking](https://semver.org/) tag.
1. Generate a new release and associate it with this tag.
1. Add the message "My Message" to the release before the changelog.
1. Upload `LICENSE.txt` and any `jar` files as release assets.

```yaml
---
name: "tagged-release"

on:
  push:
    tags:
      - "v*"

jobs:
  tagged-release:
    name: "Tagged Release"
    runs-on: "ubuntu-latest"

    steps:
      # ...
      - name: "Build & test"
        run: |
          echo "done!"

      - uses: "tomlinscote/action-automatic-releases@latest"
        with:
          repo_token: "${{ secrets.GITHUB_TOKEN }}"
          prerelease: false
          message: "My Message"
          files: |
            LICENSE.txt
            *.jar
```

## Supported Parameters

| Parameter               | Description                                                | Default  |
| ----------------------- | ---------------------------------------------------------- | -------- |
| `repo_token`\*\*        | GitHub Action token, e.g. `"${{ secrets.GITHUB_TOKEN }}"`. | `null`   |
| `draft`                 | Mark this release as a draft?                              | `false`  |
| `prerelease`            | Mark this release as a pre-release?                        | `true`   |
| `automatic_release_tag` | Tag name to use for automatic releases, e.g `latest`.      | `null`   |
| `title`                 | Release title; defaults to the tag name if none specified. | Tag Name |
| `files`                 | Files to upload as part of the release assets.             | `null`   |
| `message`\*\*               | Message to go on the release before the changelog.         | `null`   |

### Notes:

- Parameters denoted with `**` are required.
- The `files` parameter supports multi-line [glob](https://github.com/isaacs/node-glob) patterns, see repository examples.

## Event Triggers

The GitHub Actions framework allows you to trigger this (and other) actions on _many combinations_ of events. For example, you could create specific pre-releases for release candidate tags (e.g `*-rc*`), generate releases as changes land on master (example above), nightly releases, and much more. Read through [Workflow syntax for GitHub Actions](https://help.github.com/en/articles/workflow-syntax-for-github-actions) for ideas and advanced examples.

## License

The original source code for this project was released under the [MIT License](/LICENSE) by [marvinpinto](https://github.com/marvinpinto). This project is not associated with GitHub.
