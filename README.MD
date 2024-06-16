<div align="center">
  <a href="https://github.com/Reloaded-Project/devops-changelog" target="_blank">
    <img src="assets/reloaded-logo.png" alt="Reloaded Logo" width="100"/>
  </a>

  <h1 align="center">Reloaded Changelog Workflow</h1>

  <p>
    <a href="LICENSE">
      <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="License">
    </a>
  </p>
</div>

This is a GitHub composite action that generates a changelog using the 
[auto-changelog][auto-changelog] tool. It provides various options to customize the changelog
generation process and supports uploading the generated changelog as a GitHub release or an artifact.

## Example Usage

`.github/workflows/changelog.yml`:

```yaml
name: Generate Changelog

on:
  push:
    branches: [ main ]
    tags:
      - '*'

jobs:
  changelog:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Generate Changelog
        uses: Reloaded-Project/devops-changelog@v1
        with:
          output: CHANGELOG.md
          unreleased: true
          commit-limit: 50
          upload-artifact: true
          upload-release: true
          is-release: ${{ startsWith(github.ref, 'refs/tags/v') }}
          release-tag: ${{ github.ref }}
```

## Setup

To use this action in your own repository:

1. Create a new workflow file (e.g., `.github/workflows/changelog.yml`) in your repository.
2. Copy the example usage code from above into the new workflow file.
3. Customize the action inputs according to your requirements.

## Configuration

### Inputs

The following flags are directly passed to the underlying [auto-changelog tool][auto-changelog]:

(PS. If you find it useful, consider donating to the original author)

| Input                   | Required | Default        | Description                                                                  |
| ----------------------- | -------- | -------------- | ---------------------------------------------------------------------------- |
| `output`                | No       | `CHANGELOG.md` | Output file path                                                             |
| `config`                | No       | `""`           | Path to the auto-changelog config file                                       |
| `template`              | No       | `compact`      | Specify template to use [compact, keepachangelog, json]                      |
| `remote`                | No       | `""`           | Specify git remote to use for links                                          |
| `package`               | No       | `""`           | Use version from file as latest release                                      |
| `latest-version`        | No       | `""`           | Use specified version as latest release                                      |
| `unreleased`            | No       | `false`        | Include section for unreleased changes                                       |
| `commit-limit`          | No       | `""`           | Number of commits to display per release                                     |
| `backfill-limit`        | No       | `""`           | Number of commits to backfill empty releases with                            |
| `commit-url`            | No       | `""`           | Override URL for commits, use {id} for commit id                             |
| `issue-url`             | No       | `""`           | Override URL for issues, use {id} for issue id                               |
| `merge-url`             | No       | `""`           | Override URL for merges, use {id} for merge id                               |
| `compare-url`           | No       | `""`           | Override URL for compares, use {from} and {to} for tags                      |
| `issue-pattern`         | No       | `""`           | Override regex pattern for issues in commit messages                         |
| `breaking-pattern`      | No       | `""`           | Regex pattern for breaking change commits                                    |
| `merge-pattern`         | No       | `""`           | Add custom regex pattern for merge commits                                   |
| `commit-pattern`        | No       | `""`           | Pattern to include when parsing commits                                      |
| `ignore-commit-pattern` | No       | `""`           | Pattern to ignore when parsing commits                                       |
| `tag-pattern`           | No       | `""`           | Override regex pattern for version tags                                      |
| `tag-prefix`            | No       | `""`           | Prefix used in version tags                                                  |
| `starting-version`      | No       | `""`           | Specify earliest version to include in changelog                             |
| `starting-date`         | No       | `""`           | Specify earliest date to include in changelog                                |
| `ending-version`        | No       | `""`           | Specify latest version to include in changelog                               |
| `sort-commits`          | No       | `relevance`    | Sort commits by property [relevance, date, date-desc, subject, subject-desc] |
| `release-summary`       | No       | `false`        | Display tagged commit message body as release summary                        |
| `unreleased-only`       | No       | `false`        | Only output unreleased changes                                               |
| `hide-empty-releases`   | No       | `false`        | Hide empty releases                                                          |
| `hide-credit`           | No       | `false`        | Hide auto-changelog credit                                                   |
| `handlebars-setup`      | No       | `""`           | Handlebars setup file                                                        |
| `append-git-log`        | No       | `""`           | String to append to git log command                                          |
| `append-git-tag`        | No       | `""`           | String to append to git tag command                                          |
| `prepend`               | No       | `false`        | Prepend changelog to output file                                             |
| `stdout`                | No       | `false`        | Output changelog to stdout                                                   |

And the following parameters are specific to this workflow.

| Input                       | Required | Default | Description                                                                    |
| --------------------------- | -------- | ------- | ------------------------------------------------------------------------------ |
| `upload-artifact`           | No       | `false` | Upload the generated changelog as an artifact                                  |
| `upload-release`            | No       | `false` | Upload the generated changelog to GitHub Releases                              |
| `is-release`                | No       | `false` | Whether this is a GitHub Release                                               |
| `release-tag`               | No       | `""`    | Tag associated with the GitHub Release                                         |
| `override-starting-version` | No       | `false` | Override the starting version with the release tag if this is a GitHub Release |

## Extra Examples

Find more examples in [the tests][changelog-tests].

### Default Parameters

```yaml
- name: Generate Changelog
  uses: Reloaded-Project/devops-changelog@v1
```

This example uses the default parameters to generate the changelog.

This generates a `CHANGELOG.md` file with the default settings.

### Custom Parameters

```yaml
- name: Generate Changelog
  uses: Reloaded-Project/devops-changelog@v1
  with:
    output: custom-changelog.md
    unreleased: true
    commit-limit: 10
    sort-commits: date-desc
    hide-credit: true
```

This example demonstrates using custom parameters to customize the changelog generation. It sets the following inputs:

- `output`: Specifies the output file name as `custom-changelog.md`.
- `unreleased`: Includes a section for unreleased changes.
- `commit-limit`: Limits the number of commits displayed per release to 10.
- `sort-commits`: Sorts the commits by date in descending order.
- `hide-credit`: Hides the auto-changelog credit.

### Upload Artifact

```yaml
- name: Generate Changelog
  uses: Reloaded-Project/devops-changelog@v1
  with:
    # Upload the changelog as an arifact.
    upload-artifact: true

    # Upload the changelog to GitHub Releases.
    upload-release: true

    # These change how the changelog is generated.
    # They will limit the changelog to changes since last release.
    is-release: ${{ startsWith(github.ref, 'refs/tags/v') }}
    release-tag: ${{ github.ref }}
```

## Why this Exists?

To simplify deployment.

I maintain a lot of projects in my spare time, a very huge amount.

Keeping workflows in sync across all of them is time consuming; so I decided to make a full
wrapper around the [auto-changelog][auto-changelog] utility that most of my repositories use.

Hopefully it turns useful.

## Contributing

Contributions are welcome! If you encounter any issues or have suggestions for improvements,
please open an issue or submit a pull request in this repository.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

[auto-changelog]: https://github.com/cookpete/auto-changelog#command-line-options
[changelog-tests]: ./.github/workflows/test-changelog-workflow.yml