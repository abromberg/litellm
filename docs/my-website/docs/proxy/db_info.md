# What is stored in the DB

The LiteLLM Proxy uses a PostgreSQL database to store various information. Here's are the main features the DB is used for:
- Virtual Keys, Organizations, Teams, Users, Budgets, and more.
- Per request Usage Tracking

## Link to DB Schema

You can see the full DB Schema [here](https://github.com/BerriAI/litellm/blob/main/schema.prisma)

## DB Tables

### Organizations, Teams, Users, End Users

| Table Name | Description | Row Insert Frequency |
|------------|-------------|---------------------|
| LiteLLM_OrganizationTable | Manages organization-level configurations. Tracks organization spend, model access, and metadata. Links to budget configurations and teams. | Low |
| LiteLLM_TeamTable | Handles team-level settings within organizations. Manages team members, admins, and their roles. Controls team-specific budgets, rate limits, and model access. | Low |
| LiteLLM_UserTable | Stores user information and their settings. Tracks individual user spend, model access, and rate limits. Manages user roles and team memberships. | Low |
| LiteLLM_EndUserTable | Manages end-user configurations. Controls model access and regional requirements. Tracks end-user spend. | Low |
| LiteLLM_TeamMembership | Tracks user participation in teams. Manages team-specific user budgets and spend. | Low |
| LiteLLM_OrganizationMembership | Manages user roles within organizations. Tracks organization-specific user permissions and spend. | Low |
| LiteLLM_InvitationLink | Handles user invitations. Manages invitation status and expiration. Tracks who created and accepted invitations. | Low |
| LiteLLM_UserNotifications | Handles model access requests. Tracks user requests for model access. Manages approval status. | Low |

### Authentication

| Table Name | Description | Row Insert Frequency |
|------------|-------------|---------------------|
| LiteLLM_VerificationToken | Manages Virtual Keys and their permissions. Controls token-specific budgets, rate limits, and model access. Tracks key-specific spend and metadata. | **Medium** - stores all Virtual Keys |

### Model (LLM) Management

| Table Name | Description | Row Insert Frequency |
|------------|-------------|---------------------|
| LiteLLM_ProxyModelTable | Stores model configurations. Defines available models and their parameters. Contains model-specific information and settings. | Low - Configuration only |

### Budget Management

| Table Name | Description | Row Insert Frequency |
|------------|-------------|---------------------|
| LiteLLM_BudgetTable | Stores budget and rate limit configurations for organizations, keys, and end users. Tracks max budgets, soft budgets, TPM/RPM limits, and model-specific budgets. Handles budget duration and reset timing. | Low - Configuration only |


### Tracking & Logging

| Table Name | Description | Row Insert Frequency |
|------------|-------------|---------------------|
| LiteLLM_SpendLogs | Detailed logs of all API requests. Records token usage, spend, and timing information. Tracks which models and keys were used. | **High - every LLM API request** |
| LiteLLM_ErrorLogs | Captures failed requests and errors. Stores exception details and request information. Helps with debugging and monitoring. | **Medium - on errors only** |
| LiteLLM_AuditLog | Tracks changes to system configuration. Records who made changes and what was modified. Maintains history of updates to teams, users, and models. | **Off by default**, **High - when enabled** |

## How to Disable `LiteLLM_SpendLogs`

You can disable spend_logs by setting `disable_spend_logs` to `True` on the `general_settings` section of your proxy_config.yaml file.

```yaml
general_settings:
  disable_spend_logs: True
```


### What is the impact of disabling `LiteLLM_SpendLogs`?

- You **will not** be able to view Usage on the LiteLLM UI
- You **will** continue seeing cost metrics on s3, Prometheus, Langfuse (any other Logging integration you are using)

