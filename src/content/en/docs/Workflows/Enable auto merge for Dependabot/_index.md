---
title: Enable Auto Merge for Dependabot
weight: 5
categories: [Automation]
tags: [GitHub Actions, Dependabot, CI/CD]
description: >
  Automatically merge Dependabot pull requests using GitHub Actions
---

Keeping dependencies up to date is essential for maintaining the security and stability of your projects, but manually merging dozens of Dependabot pull requests can be tedious and time-consuming. By automating this process with GitHub Actions, you can ensure that approved dependency updates are merged quickly and efficiently, freeing up time for more valuable work. 

In this article, you'll learn how to set up an auto-merge workflow for Dependabot PRs using GitHub Actions and the GitHub CLI. This automation will help you streamline your CI/CD pipeline and keep your codebase secure with minimal manual intervention.

```yaml
name: Dependabot auto-merge
on:
    pull_request:
        types: [opened, reopened]
    workflow_call:

permissions:
    contents: write
    pull-requests: write

jobs:
    dependabot:
        runs-on: ubuntu-latest
        if: ${{ github.actor == 'dependabot[bot]' }}
        steps:
            - name: Dependabot metadata
              id: metadata
              uses: dependabot/fetch-metadata@08eff52bf64351f401fb50d4972fa95b9f2c2d1b
              with:
                  github-token: "${{ secrets.GITHUB_TOKEN }}"
            - name: install GH CLI
              uses: sersoft-gmbh/setup-gh-cli-action@3cb41a4434ca35de4d1c16e00dc7e16d38409494
              with:
                  version: stable
            - name: Enable auto-merge for Dependabot PRs
              run: gh pr merge --auto --rebase "$PR_URL"
              env:
                  PR_URL: ${{github.event.pull_request.html_url}}
                  GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
```
