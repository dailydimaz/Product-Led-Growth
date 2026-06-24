# MCP Tool Map

Concrete capabilities of each connected server, mapped to PLG jobs. Tool names
can drift; if a named tool isn't present, discover the server's tools first and
use the closest equivalent. Verified against vendor docs (June 2026).

---

## PostHog — `https://mcp.posthog.com/mcp`

The instrument. Auth routes you to the correct region (US/EU). Some tools run
LLMs internally and may bill as PostHog AI spend; these need AI data processing
enabled in org settings.

**What it can do (by category):**
- **Analytics & insights** — create/run insights, trends, funnels, retention,
  lifecycle; list/get dashboards.
- **SQL (HogQL)** — run arbitrary queries over events/persons for custom
  cohort/correlation analysis (confusion-matrix style "Aha!" hunting).
- **Cohorts** — define and read behavioral cohorts.
- **Feature flags** — create, list, update, roll out / target — the mechanism
  for A/B onboarding variants and gating.
- **Experiments** — create and read A/B experiment results.
- **Error tracking** — list error groups, pull stack traces, set issue status /
  assignment rules.
- **CDP destinations** and support-ticket triage (peripheral to PLG).

**Used in PLG for:** defining the retained-user baseline, hunting the "Aha!"
moment via HogQL correlation, building activation/retention cohorts, running A/B
tests through feature flags + experiments, and detecting PQL thresholds to fire
downstream actions.

**Discovery tip:** ask the server for its tool list, or see PostHog's
"Tools reference" at `posthog.com/docs/model-context-protocol/tools`.

---

## Stripe — `https://mcp.stripe.com`

The money layer. OAuth-based; sandbox and live mode are managed separately —
**always state which mode you're in.** Amounts are in the smallest currency unit
(cents).

**Named tools:** `stripe_api_search`, `stripe_api_details`, `stripe_api_read`
(any GET), `stripe_api_write` (any POST/PATCH/PUT/DELETE), `get_stripe_account_info`,
`create_refund`, `create_customer`, `search_stripe_resources`,
`fetch_stripe_resources`, `search_stripe_documentation`, `stripe_report`, plus
integration/implementation planner helpers.

**Reachable API methods (via read/write tools):** customers, charges,
PaymentIntents, Checkout Sessions, invoices (+ finalize, invoice items),
subscriptions (list/retrieve/update/cancel), products, prices, payment links
(+ line items), coupons, promotion codes, disputes, billing portal
configurations, balance, refunds.

**Used in PLG for:** frictionless self-serve upgrades (payment links / checkout),
trial and freemium plan setup (products + prices), paywall enforcement, coupons
& promo codes for activation incentives, expansion/seat changes (subscription
update), and revenue/NDR inputs (invoices, subscriptions).

**Guardrail:** any write (create/update/cancel/refund/price change) is a
permission-required action — confirm first, prefer sandbox unless told otherwise.

---

## Twenty — `twenty-crm-mcp-server`

The open-source, PLG-native CRM. Node.js MCP server requiring `TWENTY_API_KEY`.

**Tools (selected):**
- **People Operations:** `create_person`, `get_person`, `update_person`, `list_people`, `delete_person`.
- **Company Operations:** `create_company`, `get_company`, `update_company`, `list_companies`, `delete_company`.
- **Task & Note Operations:** CRUD for tasks and notes (`create_task`, `create_note`, etc.).
- **Metadata & Search:** `get_metadata_objects`, `get_object_metadata`, `search_records` (intelligent filtering and natural language queries).

**Used in PLG for:** routing PQLs to standard objects (people/companies), searching across records to identify target accounts, logging product-usage context as notes, creating follow-up tasks, and discovering dynamic custom fields mapping to product metrics.

**Guardrail:** any write (create/update/delete) is a permission-required action — confirm first. Automatically discovers custom fields via metadata.

---

## Attio — `https://mcp.attio.com/mcp`

The PLG-native CRM. OAuth; reads auto-approve, writes ask for confirmation.

**Tools (selected):**
- **Records & objects:** `search-records`, `list-records`, `get-records-by-ids`,
  `create-record`, `upsert-record`, `update-record`, `list-attribute-definitions`.
- **Lists (pipelines):** `list-lists`, `list-records-in-list`, `add-record-to-list`,
  `update-list`, `update-list-entry-by-id`, `update-list-entry-by-record-id`,
  `list-list-attribute-definitions`.
- **Notes:** `create-note`, `update-note`, `get-note-body`,
  `search-notes-by-metadata`, `semantic-search-notes`.
- **Tasks:** `list-tasks`, `create-task`, `update-task`.
- **Comments:** `create-comment`, `list-comments`, `list-comment-replies`,
  `delete-comment`.
- **Meetings/calls & emails:** `search-meetings`, `search-call-recordings-by-metadata`,
  `semantic-search-call-recordings`, `get-call-recording`,
  `search-emails-by-metadata`, `semantic-search-emails`, `get-email-content`.
- **Workspace & reporting:** `list-workspace-members`, `list-workspace-teams`,
  `whoami`, `run-basic-report`.

**Used in PLG for:** storing PQL scores on company/person records (`upsert-record`),
routing PQLs into a sales pipeline list (`add-record-to-list`,
`update-list-entry-by-record-id`), logging product-usage context as notes,
creating follow-up tasks for AEs, and PLS pipeline reporting (`run-basic-report`,
e.g. "count open deals by stage").

---

## Plunk — `@ignytehq/plunk-mcp` (env `PLUNK_API_KEY`)

The open-source lifecycle email and automation platform. Requires `PLUNK_API_KEY` (and optionally `PLUNK_PUBLIC_KEY` for event tracking).

**Tool groups:**
- **Transactional:** `send_transactional`, `track_event`, `verify_email`
- **Contacts:** CRUD, bulk import/subscribe/unsubscribe/delete, custom field management
- **Campaigns:** Create, update, send, cancel, test, stats
- **Segments:** Dynamic + static segments, member management, recompute
- **Workflows:** Steps, transitions, executions (the automation builder)
- **Templates:** Reusable email templates referenced from sends/campaigns/workflows
- **Analytics & Events:** Timeseries, top campaigns, top events, history

**Used in PLG for:** sending transactional onboarding nudges, building complex lifecycle workflows directly in Plunk, broadcasting campaigns to dynamic segments (e.g., "signed up this month"), and firing `track_event` to trigger automations. Unlike Resend which relies on external workflow engines, Plunk handles the drip timing and logic internally.

**Guardrail:** sending any email or modifying workflows/campaigns is permission-required. Confirm recipient(s), subject, and content.

---

## Resend — `resend-mcp` (env `RESEND_API_KEY`)

The sending muscle. Pairs with React Email for templates; drip *timing/branching*
lives in your workflow engine (Trigger.dev/Inngest), not in Resend.

**Tool groups:** Emails (send, list, get, cancel, update, batch send; read
inbound + attachments), Contacts (lists, topic subscriptions, custom properties),
Broadcasts (campaigns with scheduling + personalization), Domains (add/verify/
update), Segments, Topics, Contact properties, API keys, Webhooks (sent/bounced/
complained/opened/clicked).

**Used in PLG for:** onboarding nudge emails toward the "Aha!" action, batch
onboarding to new signups, broadcast launch/announcement campaigns, audience
segmentation (e.g. "signed up this month"), and wiring webhooks so
`email.opened` / `email.clicked` events flow back to PostHog (close the analytics
loop — see playbook 6).

**Guardrail:** sending any email is permission-required. Confirm recipient(s),
subject, and content; for broadcasts confirm the audience/segment size first.

---

## Clerk — `https://mcp.clerk.com/mcp`

A **developer-assistant** server, not a data server. Streamable HTTP only.

**Tools:** `clerk_sdk_snippet` (fetch SDK code patterns for a feature),
`list_clerk_sdk_snippets` (list available snippets/bundles).

**Snippet bundles:** `b2b-saas` (orgs + billing + RBAC), `organizations`
(multi-tenant), `waitlist`, `auth-basics`, `custom-flows`, `server-side`.

**Used in PLG for:** generating correct implementation code for the multiplayer
and invitation mechanics that create internal viral loops — organization
creation, member invitations, role-based access — and for waitlist/early-access
gating. It tells you *how to build* the invite loop; it does not report who has
been invited. For live invite/seat counts, instrument those events in PostHog.
