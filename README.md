# Support Inbox to Linear

Support email becomes Linear bugs, refunds get handled, and fixed bugs get a reply in-thread.

An MCP server with **12 workflows** across Gmail, Linear, Stripe, Granola, Google Docs, Slack, Google Calendar, Google Drive, Google Forms, Notion and Google Sheets. Each workflow is a prompt your agent runs as a slash command, over the 30 tools it needs and no others.

```bash
uv tool install https://github.com/r28ai/support-inbox-mcp/releases/download/v0.1.0/support_inbox_mcp-0.1.0-py3-none-any.whl
claude mcp add support -- support-inbox-mcp
```

It installs with [uv](https://docs.astral.sh/uv/) from this repository's release, with no git and nothing to build; nothing but Charter and the libraries it uses comes from PyPI. To update, run the install line from the [latest release](https://github.com/r28ai/support-inbox-mcp/releases/latest). If a desktop app cannot find `support-inbox-mcp`, give it the full path from `which support-inbox-mcp` (`where support-inbox-mcp` on Windows).

Then ask your agent to **connect your apps**, or run `/mcp__support__setup`.

## Connect your apps

Ask the agent to connect one ("connect Linear"). It tells you where to get that app's key and the command that stores it, and the next call works, with no restart. The agent never asks for a key in the chat.

Or connect everything this server uses from a terminal:

```bash
support-inbox-mcp login            # each app in turn
support-inbox-mcp login linear     # just one
support-inbox-mcp status           # what is connected
```

Tokens and keys go to your operating system's keychain (macOS Keychain, Windows Credential Manager, the Secret Service on Linux), and are checked with one read-only call to the app's own API before they are kept. Every key, token and OAuth client is yours: we register no app with any of these services, and nothing passes through a server of ours, because there isn't one.

| App | How it connects | Or set |
|---|---|---|
| Google | Browser sign-in, over your own OAuth client ([make one](https://docs.r28.ai/charter/auth/setup/google)). | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` |
| Linear | Your own key ([get one](https://linear.app/settings/account/security)), entered once. | `LINEAR_API_KEY` |
| Stripe | Your own key ([get one](https://dashboard.stripe.com/apikeys)), entered once. | `STRIPE_API_KEY` |
| Granola | Your own key ([get one](https://docs.granola.ai/help-center/sharing/integrations/granola-api)), entered once. In the Granola app: Settings → Connectors → API keys. Business plan or above. | `GRANOLA_API_KEY` |
| Slack | Your own key ([get one](https://docs.r28.ai/charter/auth/setup/slack)), entered once. A bot token from your own Slack app, which the guide sets up in about three minutes. | `SLACK_BOT_TOKEN` |
| Notion | Your own key ([get one](https://www.notion.so/profile/integrations)), entered once. Then share the pages it should see with the integration. | `NOTION_API_KEY` |

A variable set in your client's config always wins over the keychain.

## Workflows

| Workflow | What you get | Apps |
|---|---|---|
| **Support email → Linear bug with a reply drafted** <br>`support_email_to_linear_bug_with_a_reply_drafted` | Bug reports leave the inbox as issues, and the customer gets a real acknowledgement. | Gmail, Linear |
| **Fixed → reply in the original thread** <br>`fixed_to_reply_in_the_original_thread` | Customers who reported a bug hear it's fixed in the same email thread. | Linear, Gmail |
| **QBR account brief** <br>`qbr_account_brief` | Spend, payment issues, open requests and last calls, on one page before the QBR. | Stripe, Linear, Granola, Google Docs |
| **Churn signal → save call** <br>`churn_signal_to_save_call` | A cancellation at period end triggers a call while there is still time. | Stripe, Granola, Slack, Google Calendar |
| **New customer onboarding kickoff** <br>`new_customer_onboarding_kickoff` | Plan doc shared, kickoff booked and welcome sent on day one. | Stripe, Google Drive, Google Calendar, Gmail |
| **Customer Slack channel → requests and bugs** <br>`customer_slack_channel_to_requests_and_bugs` | Asks in shared channels get tracked and answered with a link. | Slack, Linear |
| **Refund request handled end to end** <br>`refund_request_handled_end_to_end` | The charge is found, the refund issued and the reply drafted, with a human approving the refund. | Gmail, Stripe |
| **NPS detractor follow-up** <br>`nps_detractor_follow_up` | Low scores from paying accounts get a personal reply and their complaint tracked. | Google Forms, Stripe, Gmail, Linear |
| **Help-center gap finder** <br>`help_center_gap_finder` | Questions asked three times with no article get a draft article. | Gmail, Notion |
| **Escalation runbook** <br>`escalation_runbook` | Priority raised, call booked, customer told: one command instead of four tabs. | Slack, Linear, Google Calendar, Gmail |
| **Internal Q&A from the docs** <br>`internal_q_and_a_from_the_docs` | Questions in #ask get answered in-thread from the wiki and Drive, with links. | Slack, Notion, Google Drive |
| **Account health sheet** <br>`account_health_sheet` | Billing state and open bugs per account in one sheet the CS team sorts by. | Stripe, Linear, Google Sheets |

Every prompt takes one optional argument, `details`: the repo, team, channel, customer or date range you mean, so the agent does not have to ask. In Claude Code, put it in quotes, or only its first word arrives:

```
/mcp__support__support_email_to_linear_bug_with_a_reply_drafted "the support@ inbox, Linear team SUP"
```

Reads run without asking. Before anything that creates, sends, changes or deletes, the prompt tells the agent to show you the call and wait.

1 of the 12 workflows need no Google or Granola credential.

## Other clients

**Claude Desktop**: install [uv](https://docs.astral.sh/uv/getting-started/installation/) if you have not, since Claude Desktop starts the server with it, then open the `.mcpb` from the [latest release](https://github.com/r28ai/support-inbox-mcp/releases/latest). Claude asks for any keys in its own settings and keeps them in your keychain. The first start takes a few seconds longer, while uv installs it.

**VS Code** (`.vscode/mcp.json`): VS Code asks for each key the first time the server starts and stores it securely. Leave out any you stored with `login`.

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "google-client-secret",
      "description": "Google: OAuth client secret",
      "password": true
    },
    {
      "type": "promptString",
      "id": "linear-api-key",
      "description": "Linear: Personal API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "stripe-api-key",
      "description": "Stripe: Secret or restricted key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "granola-api-key",
      "description": "Granola: API key",
      "password": true
    },
    {
      "type": "promptString",
      "id": "slack-bot-token",
      "description": "Slack: Bot token (xoxb-\u2026)",
      "password": true
    },
    {
      "type": "promptString",
      "id": "notion-api-key",
      "description": "Notion: Integration secret (ntn_\u2026)",
      "password": true
    }
  ],
  "servers": {
    "support": {
      "type": "stdio",
      "command": "support-inbox-mcp",
      "env": {
        "GOOGLE_CLIENT_SECRET": "${input:google-client-secret}",
        "LINEAR_API_KEY": "${input:linear-api-key}",
        "STRIPE_API_KEY": "${input:stripe-api-key}",
        "GRANOLA_API_KEY": "${input:granola-api-key}",
        "SLACK_BOT_TOKEN": "${input:slack-bot-token}",
        "NOTION_API_KEY": "${input:notion-api-key}",
        "GOOGLE_CLIENT_ID": ""
      }
    }
  }
}
```

**Cursor** (`.cursor/mcp.json`) starts it the same way:

```json
{
  "mcpServers": {
    "support": {
      "command": "support-inbox-mcp"
    }
  }
}
```

**Codex** (`~/.codex/config.toml`) starts a turn without waiting for a server unless it is `required`, and then the agent has none of its tools. `required = true` makes the session wait for it, and `startup_readiness = "catalog"` waits for its tool list rather than just its connection:

```toml
[mcp_servers.support]
command = "support-inbox-mcp"
required = true
startup_readiness = "catalog"
startup_timeout_sec = 30
```

Name the server `support`. A host builds each tool's name from that key, and a longer one can push a tool past the 64 characters a function name allows.

## Built with Charter

Every tool here is a [Charter](https://github.com/r28ai/charter) declaration: a Pydantic schema saying where each field goes on the wire. Charter's runtime builds the request, attaches and refreshes the credential, and trims the response before the model reads it. It runs in your process, with no proxy and no telemetry.

The 30 tool schemas come to 50,308 tokens.

The same tools work in your own agent, without MCP:

```python
from charter.adapters.openai import to_openai_tools
from charter_packs_mcp import FAMILIES

tools = FAMILIES["support"].tools()
definitions = to_openai_tools(tools)   # or charter.adapters.langchain
```

Need an API that isn't here? [Write a pack](https://docs.r28.ai/charter/start/coding-agents): your coding agent writes the declarations, and Charter's conformance suite checks them.

<details>
<summary>All 30 tools</summary>

- **Gmail**: `gmail_threads_list`, `gmail_threads_get`, `gmail_drafts_create`, `gmail_threads_modify`, `gmail_messages_send`
- **Linear**: `linear_search_issues`, `linear_issue_create`, `linear_issues_list`, `linear_attachments_list`, `linear_customer_needs_list`, `linear_customer_need_create`, `linear_issue_update`
- **Stripe**: `stripe_customers_retrieve`, `stripe_invoices_list`, `stripe_subscriptions_list`, `stripe_customers_list`, `stripe_charges_list`, `stripe_refunds_create`
- **Granola**: `granola_notes_list`
- **Google Docs**: `gdocs_documents_create`
- **Slack**: `slack_chat_post_message`, `slack_conversations_history`
- **Google Calendar**: `gcalendar_events_insert`
- **Google Drive**: `gdrive_files_copy`, `gdrive_permissions_create`, `gdrive_files_list`
- **Google Forms**: `gforms_forms_responses_list`
- **Notion**: `notion_search`, `notion_pages_create`
- **Google Sheets**: `gsheets_spreadsheets_values_update`

</details>

## License

Apache 2.0.
