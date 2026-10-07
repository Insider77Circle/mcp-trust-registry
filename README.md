# 🔐 mcp-trust-registry

> Every MCP registry wants to be an app store. This one is a list you can point an agent at — and trust.

MCP servers have a discoverability problem *and* a trust problem. Existing
registries are built for human browsing: a name, a one-line summary, a link.
None of them answer the two questions an agent actually needs before calling
a server:

1. **Is this server still the one that was vouched for?** — answered with a
   tool-schema hash + source commit next to a certification chain.
2. **What do I install for a whole job, not one tool?** — answered with
   curated, trust-checked bundles ("give me everything for email").

## The trust model

```
publisher ──submits──▶  listing { name, summary, source URL,
                                  schema_hash, source_commit }
                                    │
agent ──calls──▶  registry_verify(server_id)
                                    │
                    ┌───────────────┴───────────────┐
                    ▼                               ▼
              schema hash                    source commit
              recomputed                     re-fetched
                    └───────────────┬───────────────┘
                                    ▼
                    VERIFIED / MISMATCH / UNREACHABLE
```

A listing is a *claim*. Verification re-checks the claim against the live
server. Drift — a changed schema, a moved commit — surfaces as `MISMATCH`,
not as a silent behavior change in your agent's toolchain.

## The tools (this registry is itself an MCP server)

| Tool | What it does |
|---|---|
| `registry_search(query, category?, filters?)` | Ranked servers with trust metadata: schema hash, source commit, vouch chain, payment info |
| `registry_verify(server_id)` | Recompute schema hash + source commit; return VERIFIED / MISMATCH / UNREACHABLE with evidence |
| `registry_bundle(theme)` | Curated bundle for a theme (`email`, `finance`, `dev-tools`) with per-server trust status |
| `registry_publish(metadata)` | Submit a listing with schema hash + source commit; returns listing ID + verification receipt |
| `registry_watch(server_id)` | Subscribe; notify on schema-hash or source drift from vouched values |

Public read. Publisher-signed writes. No review queues, no gatekeeping —
trust is cryptographic, not editorial.

## Why this exists

Real voices, October 2026:

- *"We went looking for somewhere to list our MCP servers and did not find
  one that fit. The registries that exist are built for discovery at scale —
  a name, a one-line summary, a link."* — [@lonniev](https://x.com/lonniev)
- *"What is missing is a plain list you can point an agent at and trust
  without the gatekeeping."* — [@JE4NVRG](https://x.com/JE4NVRG)
- *"I'd put the tool schema hash and the source commit next to the
  certification chain, so a client can check that the server it's calling is
  still the one that was vouched for."* — [@AzielEliab](https://x.com/AzielEliab)
- *"I deployed 35 MCP servers in 10 days... Zero external API calls. Nothing
  is reaching anyone."* — [alexcodebytes](https://dev.to/alexcodebytes/i-deployed-35-mcp-servers-in-10-days-heres-what-nobody-tells-you-about-ai-monetization-4j8h)
- 15,465 public MCP servers analyzed across 5 registries: *"no marketplace
  vetting"* — [@TheHackersNews](https://x.com/TheHackersNews)

The supply side is loud: builders can't get discovered, and agents can't
verify what they find. This repo is the build that closes both gaps.

## Roadmap

- [ ] Registry data model + JSON schema for listings
- [ ] `registry_search` over the official MCP Registry API + GitHub commit data
- [ ] `registry_verify` — schema-hash recomputation + commit comparison
- [ ] `registry_bundle` — first curated bundles (email, dev-tools)
- [ ] `registry_publish` with publisher-signed writes
- [ ] `registry_watch` — drift subscriptions
- [ ] Public hosted endpoint agents can point at

## Status

🚧 **In build.** The spec above is the target; the code is coming.
Issues and PRs welcome — especially from MCP server publishers who want
their servers listed *and* verifiable.

## License

MIT — see [LICENSE](LICENSE).
