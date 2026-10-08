# Companion skills for cloudflare-infra-worker

Cloudflare publishes official Agent Skills at https://github.com/cloudflare/skills. If they are installed, load the relevant one for build and implementation detail:

- `cloudflare`: choosing products and architecture
- `wrangler`: CLI commands, configuration, deploys
- `workers-best-practices`: writing or reviewing Workers code
- `durable-objects`: Durable Objects code
- `agents-sdk`: agents and remote MCP servers
- `turnstile-spin`: Turnstile setup and repair
- `cloudflare-one` / `cloudflare-one-migrations`: Zero Trust and Access
- `cloudflare-email-service`: Email Routing and Email Sending
- `web-perf`: performance audits
- `nextjs-on-cloudflare`: Next.js on Cloudflare
- `sandbox-*`: Cloudflare Sandbox apps

These are reference material only. The `cloudflare-infra-worker` skill still governs safety, modes, previews, rollback, per-domain scoping and the change log. If a companion skill conflicts with it about whether or how to change a live resource, the `cloudflare-infra-worker` safety rules win.
