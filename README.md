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

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: production
    steps:
      - run: echo "Deploying to production..."
