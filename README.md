# GH600-Developing-in-Agentic-AI-Systems
Learn how AI coding agents are transforming software development by planning, acting, and improving within GitHub workflows


	• https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/gh-600

		○ https://learn.microsoft.com/en-us/training/modules/foundations-agentic-ai/
	
			§ https://learn.microsoft.com/en-us/training/modules/design-agent-architecture-integration/
		  
			§ https://learn.microsoft.com/en-us/training/modules/agent-tooling-mcp-execution-environments/
	
		○ https://learn.microsoft.com/en-us/training/modules/design-agent-architecture-integration/
		

A common safe workflow looks like this:
====================================
Agent creates branch

↓
Agent opens pull request (includes plan)

↓
Required reviews validate approach

↓
GitHub Actions run required checks

↓
All checks pass + approvals complete

↓
Pull request can be merged

This structure ensures that execution is gated by both automation and human review.


CODEOWNERS ensures that changes to sensitive areas go to the right reviewers automatically.
===========================================================================================
# File: CODEOWNERS

/security/ @security-team
/.github/workflows/ @platform-team
/infra/ @platform-team
* @core-team


Environments provide a strong control point for risky actions such as deployments and access to protected secrets.
```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: production
    steps:
      - run: echo "Deploying to production..."
```

```yaml
- id: generate_plan
  run: |
    echo "plan=high level steps..." >> "$GITHUB_OUTPUT"

- run: |
    echo "Plan: ${{ steps.generate_plan.outputs.plan }}"

```


For cross-job sharing, publish a job output and reference it from a dependent job:
====================================================================================
```yaml

jobs:
  plan:
    outputs:
      plan: ${{ steps.generate_plan.outputs.plan }}
    steps:
      - id: generate_plan
        run: echo "plan=..." >> "$GITHUB_OUTPUT"

  implement:
    needs: plan
    steps:
      - run: echo "Using plan: ${{ needs.plan.outputs.plan }}"

```
Defensive gating for pull request-only behavior
===============================================

```yaml
name: PR Validation

on:
  pull_request:
    branches: [ main ]
  workflow_dispatch: # allows manual runs, but still gated below

jobs:
  validate-pr:
    # Defensive gating: only run if this is actually a PR context
    if: github.event_name == 'pull_request'

    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Run tests
        run: npm test

      - name: Comment on PR
        run: echo "Validation complete"


```

Uploading artifacts makes evidence durable and reviewable, even when logs scroll away.
====================================================================================
```yaml
- name: Upload test results
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: results/
```

For deeper reading, use official GitHub documentation on:
=========================================================
Creating a pull request template for your repository -- https://docs.github.com/es/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository

Managing rulesets for a repository -- https://docs.github.com/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/managing-rulesets-for-a-repository

Available rules for rulesets -- https://docs.github.com/enterprise-cloud@latest/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets

Troubleshooting required status checks -- https://docs.github.com/en/enterprise-server@3.16/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/troubleshooting-required-status-checks

Using GITHUB_TOKEN for authentication in workflows -- https://docs.github.com/en/actions/configuring-and-managing-workflows/authenticating-with-the-github_token

Security hardening for GitHub Actions -- https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions

Environments -- https://docs.github.com/en/actions/reference/environments

Uploading an artifact in a workflow -- https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts

Uploading a SARIF file to GitHub -- https://docs.github.com/en/code-security/how-tos/scan-code-for-vulnerabilities/integrate-with-existing-tools/uploading-a-sarif-file-to-github

Protecting pushes with secret scanning (push protection) -- https://docs.github.com/code-security/secret-scanning/protecting-pushes-with-secret-scanning

Using hooks with GitHub Copilot agents -- https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-hooks

Tracking GitHub Copilot's sessions -- https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/track-copilot-sessions


A GitHub Agentic Workflow has two main parts:
=============================================
Frontmatter for configuration such as triggers, permissions, tools, and safe outputs
Markdown instructions that describe the job in natural language
The Markdown expresses intent, while the frontmatter defines the boundaries. The workflow is then compiled into a lock file that GitHub Actions executes.

on: schedule: daily
permissions: contents: read issues: read pull-requests: read
safe-outputs: create-issue: title-prefix: "[repo-status] " labels: [report]
tools: github:

Daily Repository Status Report
Create a daily report for maintainers.
Include:
Recent activity (issues, PRs, commits)
Key highlights and risks
Recommended next steps
Keep the report concise and link to relevant issues and pull requests.
