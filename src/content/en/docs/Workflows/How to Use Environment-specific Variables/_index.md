---
title: How to Use Environment-specific Variables in GitHub Actions Workflows
weight: 6
categories: [Automation]
tags: [GitHub Actions, variables, secrets, environments, CI/CD]
description: >
  How to configure your GitHub Actions workflows so that variables are automatically set based on the environment you deploy to.
---

# How to Use Environment-specific Variables in GitHub Actions Workflows

When working with multiple environments like **development (dev)**, **acceptance (acc)**, and **production (prod)**, it's common to need different configuration values for each. GitHub Actions provides a powerful way to set up environment-specific variables using **GitHub Environments** and environment-scoped variables/secrets.

This tutorial shows how to configure your GitHub Actions workflows so that variables like `CLIENT_ID` are automatically set based on the environment you deploy to.

---

## 1. Set Up GitHub Environments

First, define your environments in the repository.

1. Go to your repository on GitHub.
2. Navigate to **Settings** > **Environments**.
3. Click **New environment** and create environments for each deployment (e.g., `dev`, `acc`, and `prod`).

---

## 2. Add Environment Variables (or Secrets)

For each environment you created:

1. Click its name.
2. Under **Environment variables**, add an entry for `CLIENT_ID` (and any other variables you need), with a value specific to that environment.

*Example:*

| Environment | CLIENT_ID value    |
| ----------- | ----------------- |
| dev         | dev-123456        |
| acc         | acc-abcdef        |
| prod        | prod-zzzzzz       |

You can add secrets here as well, using the **Secrets** section.

---

## 3. Create a Workflow for Environment-specific Values

Below is an example workflow that sets the environment based on a user input, and automatically uses the correct `CLIENT_ID` from your environment variables.

```yaml
name: Deploy

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        default: 'dev'
        type: choice
        options:
          - dev
          - acc
          - prod

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: ${{ github.event.inputs.environment }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Print CLIENT_ID for this environment
        run: echo "ClientId is ${{ vars.CLIENT_ID }}"
```
> **Note:**  
> - The `${{ vars.CLIENT_ID }}` expression will resolve to the value configured for `CLIENT_ID` in the environment selected by the workflow input.
> - You can use `${{ secrets.SOME_SECRET }}` in the same way for secrets.

---

## 4. How It Works

- When you run the workflow (from the Actions tab or via manual trigger), you'll pick the `environment` (`dev`, `acc`, or `prod`).
- The job executes using that environment context—so all environment variables and secrets tied to that environment become available in your workflow steps.
- This avoids hardcoding values and allows secure management of credentials or configuration for each environment.

---

## 5. Benefits

- **Separation of configuration:** Manage environment-specific values in GitHub settings, not in your code.
- **Security:** You can use secrets for confidential info, and limit who can deploy to each environment.
- **Simplicity:** No need for complicated scripting or conditional logic.

---

## 6. Tips

- Use environment-scoped variables for non-sensitive information, and secrets for passwords/API keys.
- You can also protect environments (require approvals, limit branch access, etc.).
- For more advanced workflows, you can reference environment variables/secrets in any step.

---

**That’s it!**  
Now your GitHub Actions workflows will automatically pick the correct variable values for each environment, keeping your deployments safe and configuration manageable.

---
