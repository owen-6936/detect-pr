# Detect Existing PR Action

This GitHub Action checks a repository for an existing open pull request from a specific branch. It is designed to be used in workflows that automatically create pull requests, preventing the creation of duplicates.

[![GitHub Marketplace](https://img.shields.io/badge/GitHub-Marketplace-blue)](https://github.com/marketplace/actions/detect-existing-pr)
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/owen-6936/detect-pr?style=for-the-badge)](https://github.com/owen-6936/detect-pr/releases)
[![Build Status](https://img.shields.io/github/actions/workflow/status/owen-6936/detect-pr/test-action.yml?branch=main&style=for-the-badge)](https://github.com/owen-6936/detect-pr/actions/workflows/test-action.yml)
[![License](https://img.shields.io/github/license/owen-6936/detect-pr?style=for-the-badge)](https://github.com/owen-6936/detect-pr/blob/main/LICENSE)

## Usage

Use this action in a GitHub workflow to check if a pull request already exists before creating a new one. The action's outputs can be used in a subsequent step to conditionally skip a PR creation.

### Example Workflow

This example demonstrates how to use `detect-pr` to check for an existing pull request and then, only if one does not exist, proceed to create a new one.

```yaml
name: "Create a Pull Request"

on:
  push:
    branches:
      - 'feature/**'

jobs:
  create-pr:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Detect Existing PR
        id: pr-check
        uses: owen-6936/detect-pr@v1.0.0
        with:
          branch: ${{ github.ref_name }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
        
      - name: Create Pull Request
        if: ${{ steps.pr-check.outputs.skip_pr_creation == 'false' }}
        run: |
          gh pr create --title "Feature: ${{ github.ref_name }}" --body "Adds new feature branch."
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
````

-----

### Inputs

| Name           | Description                                                               | Required |
|----------------|---------------------------------------------------------------------------|----------|
| `branch`       | The name of the branch to check for an existing pull request.             | `true`   |
| `github-token` | A GitHub token with `pull-requests: read` permissions to perform the check.| `true`   |

-----

### Outputs

| Name                 | Description                                    |
|----------------------|------------------------------------------------|
| `skip_pr_creation`   | Set to `true` if an existing PR is found.      |
| `pr_number`          | The number of the existing pull request, if found. |

-----

## Author

This action was created by [owen-6936](https://github.com/owen-6936).
