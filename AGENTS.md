# Deployxa Agent Directives for OpenAI Codex

You are equipped with the Deployxa MCP server tools to autonomously manage project deployments, analyze failures, and configure infrastructure on the Deployxa platform.

## Available MCP Tools
- **Project Discovery & Info**: `deployxa_list_projects`, `deployxa_get_project`, `deployxa_analyze_project`
- **Deployments**: `deployxa_create_deployment`, `deployxa_deploy_workflow`, `deployxa_cancel_deployment`, `deployxa_rollback_deployment`, `deployxa_promote_deployment`
- **Progress & Observability**: `deployxa_deployment_progress`, `deployxa_deployment_timeline`, `deployxa_get_deployment_logs`, `deployxa_get_monitoring`, `deployxa_get_metrics`
- **AI Diagnostics**: `deployxa_diagnose_deployment`, `deployxa_explain_deployment`, `deployxa_chat_assistant`
- **Configuration**: `deployxa_list_environment`, `deployxa_set_environment`, `deployxa_list_domains`, `deployxa_add_domain`, `deployxa_get_dns_guidance`

## Core Operating Rules

### 1. Triggering Deployments
- When the user asks to deploy, identify the `projectId` and `branch`.
- Call `deployxa_create_deployment` directly. Mutations are enabled by default and do not require `confirmed: true`.
- Once triggered, monitor the deployment using `deployxa_deployment_progress` until it reaches a terminal status (`READY` or `FAILED`).
- If successful, present the deployed domain URL.

### 2. Diagnosing Failures
- If a build or deployment fails, NEVER speculate or make assumptions from partial logs.
- Call `deployxa_diagnose_deployment` and `deployxa_explain_deployment` to retrieve Engine-verified failure evidence.
- Fetch truncated logs with `deployxa_get_deployment_logs` to show the relevant stack trace.
- Provide actionable fixes (e.g., correcting Dockerfile, dependency versions, or missing environment variables).

### 3. Safety & Secret Protection
- Secret values (tokens, credentials, database passwords) are permanently redacted. Never ask the user to reveal them or print them into terminal sessions.
- Destructive operations (`deployxa_delete_project`, `deployxa_delete_domain`, `deployxa_revoke_api_token`) require explicit confirmation (`confirmed: true`). Ask the user before executing destructive calls.
