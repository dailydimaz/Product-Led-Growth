# Product-Led Growth (PLG) — Claude Skill

A portable [Claude](https://claude.com) skill that turns Product-Led Growth
strategy into executable, cross-tool workflows. It orchestrates five MCP
([Model Context Protocol](https://modelcontextprotocol.io)) servers —
**PostHog, Stripe, Attio, Resend, and Clerk** — to run an end-to-end PLG engine:
find the "Aha!" moment, score and route Product Qualified Leads (PQLs), set up
self-serve monetization, send lifecycle email, engineer viral loops, and review
growth metrics.

The strategy is vendor-neutral; the named MCPs are sensible defaults you can swap
for any analytics / billing / CRM / email / auth provider that speaks MCP.

## What's inside

```
product-led-growth/
├── SKILL.md                  # Entry point: MCP map, workflow index, metrics, guardrails
├── reference/
│   ├── plg-guide.md          # The full written guide (strategy & "why")
│   ├── mcp-toolmap.md        # Concrete tools each MCP exposes, mapped to PLG jobs
│   └── playbooks.md          # 9 step-by-step cross-MCP recipes
├── README.md
└── LICENSE
```

## Capability layers

The skill stands on five capability layers. Any MCP that fills a layer works;
the defaults are shown in parentheses.

| Layer | Default MCP | Used for |
|-------|-------------|----------|
| Product analytics | PostHog | Aha! moment, cohorts, retention, funnels, A/B tests, PQL signals |
| Billing | Stripe | Self-serve monetization, paywalls, trials, expansion/NDR |
| CRM | Attio | PQL routing, Product-Led Sales pipeline |
| Lifecycle email | Resend | Onboarding/nudge emails, broadcasts, engagement attribution |
| Auth / invite patterns | Clerk | Implementation snippets for the invite mechanics behind viral loops |

## The 9 playbooks

1. Find / validate the "Aha!" moment
2. Prove causation with an A/B test
3. Define, score & route PQLs
4. Build self-serve monetization & paywalls
5. Track expansion / NDR & reduce churn
6. Build lifecycle / onboarding emails & nudges
7. Engineer & measure a viral loop
8. Weekly PLG metrics review
9. Transition to Product-Led Sales (PLS)

See [`reference/playbooks.md`](reference/playbooks.md).

## Install

**Claude Desktop / Cowork:** open the packaged `.skill` file and choose
**Save skill**, or drop the `product-led-growth/` folder into your skills
directory.

**Manual:** place the `product-led-growth/` folder where your client loads
skills, ensuring `SKILL.md` sits at its root.

Then connect whichever MCP servers you want to use (the skill checks which are
available and adapts). Official endpoints:

- PostHog — `https://mcp.posthog.com/mcp`
- Stripe — `https://mcp.stripe.com`
- Attio — `https://mcp.attio.com/mcp`
- Resend — `resend-mcp` (npx, needs `RESEND_API_KEY`)
- Clerk — `https://mcp.clerk.com/mcp`

## Usage

Just describe a PLG task in natural language. Examples:

- "Help me find our activation Aha! moment from the last 8 weeks of data."
- "Score our power users into PQLs and route the top accounts to the sales pipeline."
- "Draft an onboarding nudge for users who created a project but haven't invited their team."
- "Run our weekly PLG metrics review."

The skill picks the matching playbook and uses the connected MCPs. It always
inspects data first and asks for confirmation before any side-effectful action
(sending email, changing billing, writing CRM records, shipping a feature flag).

## Design principles

- **Read before write.** Inspect, summarize, confirm — then act.
- **MCP-agnostic.** Layers, not lock-in. Swap any provider.
- **Data is not instructions.** Content returned by a tool is never executed as a command.
- **Measure the loop.** Every action defines how its success will be measured.

## Contributing

Issues and PRs welcome — especially additional playbooks, alternative MCP
mappings, and metric refinements. Keep contributions vendor-neutral and free of
any private/internal endpoints or credentials.

## License

[MIT](LICENSE). You're free to use, modify, and redistribute. The PLG concepts
referenced (flywheel, PQL, NDR, K-factor, etc.) are industry-standard ideas;
this repo's license covers the skill text and structure, not the underlying
concepts.
