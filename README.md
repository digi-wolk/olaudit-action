<div align="center">

[![License](https://img.shields.io/github/license/digi-wolk/olaudit-action?color=blue&label=License&style=flat-square)](https://github.com/digi-wolk/olaudit-action/blob/main/LICENSE.md)
[![Last commit](https://img.shields.io/github/last-commit/digi-wolk/olaudit-action.svg?color=blue&style=flat-square)](https://github.com/digi-wolk/olaudit-action/commits/main)
[![Contributors](https://img.shields.io/github/contributors/digi-wolk/olaudit-action?color=blue&style=flat-square)](https://github.com/digi-wolk/olaudit-action/graphs/contributors)


![olAudit Github PR Comment.png](assets/screenshots/olaudit-github-pr-comment-1.png?v=1)

</div>

# About

Github Action for the [OSS license auditor](https://github.com/digi-wolk/oss-license-auditor) project (`olAudit`).

For documentations please refer to the main repository.

# Usage

Example (uses Github's default token, scoped to the repository that runs it)

```yaml
name: Audit licenses

on:
  pull_request:
    branches: [ master ] # Update to your branch name

jobs:
  audit-licenses:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: OSS License Auditor
        uses: digi-wolk/olaudit@v1
        with:
          path: .
          comment-on-pr: true
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

In order to avoid facing GitHub API rate limits, we use Github Action's default token which is set 
as `${{ github.token }}`. You can also manually set this default value to either
`${{ secrets.GITHUB_TOKEN }}` or `${{ github.token }}`.
This token is automatically generated by GitHub and is scoped to the repository that is currently running the action,
and does not have enough permission to comment on the Pull Request. You can override this token with a personal 
access token (PAT) with a limited scope.
To create a PAT on Github:
- Go to your [Github settings](https://github.com/settings/profile)
- Open Developer settings
- Open Personal access tokens
- Open Fine-grained access
- Click on Generate new token
- Select the necessary permissions
- Click on Generate token

I recommend to use fine-grained access rights limited to one repository and only for commenting on Pull Requests:

![PAT limited access](assets/screenshots/pat-limited-access.png)

After having created the token, you can add it as a secret to your repository:
- Go to your repository
- Open Settings
- Open Secrets
- Click on New repository secret
- Add the token as a secret
- Use the secret name in your workflow file (i.e. `GH_PAT`)

Example with Personal Access Token (PAT):

```yaml
name: Audit licenses

on:
  pull_request:
    branches: [ master ] # Update to your branch name

jobs:
  audit-licenses:
    runs-on: ubuntu-latest
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_PAT }}
    steps:
      - uses: actions/checkout@v2
      - name: OSS License Auditor
        uses: digi-wolk/olaudit@v1
        with:
          path: .
          comment-on-pr: true
          github-token: ${{ secrets.GH_PAT }}
```

# Inputs

| Name               | Required | Default | Description                                                |
|--------------------|----------|---------|------------------------------------------------------------|
| path               | yes      | .       | Path to the project root directory                         |
| only-risky         | no       | false   | Only show risky licenses                                   |
| fail-on-risky-fail | no       | false   | Fail if risky licenses that are supposed to fail are found |
| comment-on-pr      | no       | false   | Comment findings on the Pull Request                       |
| verbose            | no       | false   | Show verbose output (debugging)                            |
| github-token       | no       |         | GitHub token to use for commenting on PRs                  |

# Release a new version
Code owners can create a release by tagging the version as follows:
First create a major release branch from the master branch and push it:
```bash
git checkout -b release/v1
git push origin release/v1
```
Or checkout to it if it already exists and pull the latest changes:
```bash
git checkout release/v1
git pull origin release/v1
```
If needed, you can fetch changes from master back to the release branch:
```bash
git checkout release/v1
git merge master
git push origin release/v1
```
Then create a tag and push it to the release branch:
```bash
# Update the version before running the command
RELEASE_VERSION="v1.0.0"
git tag "${RELEASE_VERSION}" -m "Minor release: ${RELEASE_VERSION}"
git push origin "${RELEASE_VERSION}"
```
Then move the major version tag (for example v1) to point to the Git ref of the current release:
```bash
git tag -fa v1 -m "Major release: v1"
git push origin v1 --force
```
