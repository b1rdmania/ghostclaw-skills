# GhostClaw Skills

Curated skills for [GhostClaw](https://github.com/b1rdmania/ghostclaw) and Claude Code. Each skill is a markdown file — drop it into `.claude/skills/` and it works.

## Install a skill

```bash
# Copy a skill folder into your GhostClaw install
cp -r marketing/page-cro /path/to/ghostclaw/.claude/skills/
```

Or just tell Claude: "Install the page-cro skill from ghostclaw-skills."

## Categories

### Agent Skills (`agent/`)
Core GhostClaw add-ons — Gmail, voice, monitoring, scheduling, multi-agent teams.

| Skill | What it does |
|-------|-------------|
| [`/add-gmail-agent`](agent/add-gmail-agent) | Read, search, send email via Gmail |
| [`/add-voice-transcription`](agent/add-voice-transcription) | Transcribe voice notes (ElevenLabs Scribe) |
| [`/add-voice-reply`](agent/add-voice-reply) | Reply with voice notes (ElevenLabs TTS) |
| [`/add-morning-briefing`](agent/add-morning-briefing) | Scheduled daily/weekly summaries |
| [`/add-heartbeat`](agent/add-heartbeat) | Health checks, alert only when needed |
| [`/add-telegram-swarm`](agent/add-telegram-swarm) | Multi-agent teams in Telegram groups |
| [`/pr-babysitter`](agent/pr-babysitter) | Monitor PRs, fix CI, resolve reviews |
| [`/run-ralph`](agent/run-ralph) | Autonomous overnight task loop |
| [`/add-slack`](agent/add-slack) | Slack channel (Socket Mode) |
| [`/add-discord`](agent/add-discord) | Discord channel |
| [`/add-update-check`](agent/add-update-check) | Check for new GhostClaw versions |
| [`/update-ghostclaw`](agent/update-ghostclaw) | Safe update with backup and rollback |

### Marketing & Growth (`marketing/`)
Full marketing toolkit. CRO, copywriting, SEO, outreach, strategy.

| Skill | What it does |
|-------|-------------|
| [`/page-cro`](marketing/page-cro) | Conversion rate audit for any page |
| [`/copywriting`](marketing/copywriting) | Write/rewrite marketing copy |
| [`/seo-audit`](marketing/seo-audit) | Technical and on-page SEO audit |
| [`/content-strategy`](marketing/content-strategy) | Topic clusters, editorial calendar |
| [`/competitor-alternatives`](marketing/competitor-alternatives) | Comparison and alternative pages |
| [`/email-sequence`](marketing/email-sequence) | Drip campaigns, onboarding flows |
| [`/cold-email`](marketing/cold-email) | B2B outreach that gets replies |
| [`/launch-strategy`](marketing/launch-strategy) | Product Hunt, go-to-market planning |
| [`/pricing-strategy`](marketing/pricing-strategy) | Tiers, packaging, monetization |
| [`/social-content`](marketing/social-content) | LinkedIn, Twitter/X, Instagram content |
| [`/ad-creative`](marketing/ad-creative) | Ad copy at scale |
| [`/site-architecture`](marketing/site-architecture) | Page hierarchy, navigation, URLs |
| [`/programmatic-seo`](marketing/programmatic-seo) | SEO pages at scale with templates |
| [`/signup-flow-cro`](marketing/signup-flow-cro) | Optimise signup/registration flows |
| [`/referral-program`](marketing/referral-program) | Referral and affiliate programs |
| [`/marketing-ideas`](marketing/marketing-ideas) | Brainstorm marketing strategies |
| [`/copy-editing`](marketing/copy-editing) | Edit and improve existing copy |
| [`/paid-ads`](marketing/paid-ads) | Google, Meta, LinkedIn ad campaigns |
| [`/form-cro`](marketing/form-cro) | Lead form optimisation |
| [`/popup-cro`](marketing/popup-cro) | Popup and modal optimisation |
| [`/onboarding-cro`](marketing/onboarding-cro) | Post-signup activation flows |
| [`/paywall-upgrade-cro`](marketing/paywall-upgrade-cro) | In-app upgrade screens |
| [`/churn-prevention`](marketing/churn-prevention) | Cancel flows, save offers, dunning |
| [`/free-tool-strategy`](marketing/free-tool-strategy) | Build free tools for lead gen |
| [`/revops`](marketing/revops) | Revenue operations, lead lifecycle |
| [`/sales-enablement`](marketing/sales-enablement) | Pitch decks, objection handling |
| [`/marketing-psychology`](marketing/marketing-psychology) | Behavioral science for marketing |
| [`/product-marketing-context`](marketing/product-marketing-context) | Set up product/audience context |

### Design & Build (`design/`)
Design systems, structured data, analytics, testing.

| Skill | What it does |
|-------|-------------|
| [`/design`](design/design) | Design system framework |
| [`/schema-markup`](design/schema-markup) | JSON-LD structured data |
| [`/ai-seo`](design/ai-seo) | Optimise for AI search engines |
| [`/analytics-tracking`](design/analytics-tracking) | GA4, event tracking, UTMs |
| [`/ab-test-setup`](design/ab-test-setup) | Plan and implement A/B tests |
| [`/domain-check`](design/domain-check) | Domain availability (600+ TLDs) |

### Community & Custom Skills

Skills built by the GhostClaw community. These live in their own repos — install by cloning into `.claude/skills/`.

| Skill | What it does | Repo |
|-------|-------------|------|
| `/geocities` | Generate authentically terrible 1998 Geocities websites | [b1rdmania/geocities-skill](https://github.com/b1rdmania/geocities-skill) |
| `/lottie` | Search LottieFiles, embed brand-coherent animations | [b1rdmania/claude-lottie-skill](https://github.com/b1rdmania/claude-lottie-skill) |
| `/brand-audit` | Audit online presence for AI readiness. Scored reports | [b1rdmania/brand-audit](https://github.com/b1rdmania/brand-audit) |
| `/brand-skills` | Brand development, image gen, frontend design | [b1rdmania/claude-brand-skills](https://github.com/b1rdmania/claude-brand-skills) |
| `/brand-naming` | Brand naming based on Placek & Roll methodologies | [ziggythebot/brand-naming-skill](https://github.com/ziggythebot/brand-naming-skill) |
| `/variant-workflow` | Preserve design fidelity converting Variant exports to production | [ziggythebot/variant-workflow](https://github.com/ziggythebot/variant-workflow) |
| `/implement-review` | Quality-gated code gen with parallel review loop | [ziggythebot/ghostclaw-implement-review](https://github.com/ziggythebot/ghostclaw-implement-review) |
| `/hinge-optimizer` | Research-backed dating profile optimizer (45+ sources) | [b1rdmania/hinge-profile-optimizer](https://github.com/b1rdmania/hinge-profile-optimizer) |
| `/retro-web` | Transform any website into nostalgic 90s/2000s styles | [ziggythebot/retro-web-generator](https://github.com/ziggythebot/retro-web-generator) |
| `namecheap-mcp` | Domain availability MCP server (600+ TLDs, batch checking) | [ziggythebot/namecheap-mcp](https://github.com/ziggythebot/namecheap-mcp) |

### Shoutouts

Skills we use daily that were built by others in the Claude Code ecosystem:

- [**awesome-claude-code**](https://github.com/b1rdmania/awesome-claude-code) — Curated list of Claude Code skills, hooks, and tools
- [**awesome-llm-skills**](https://github.com/b1rdmania/awesome-llm-skills) — Skills that work across Claude Code, Codex, Gemini CLI and custom agents
- [**context7-mcp**](https://github.com/upstash/context7-mcp) — Up-to-date library docs via MCP
- [**perplexity-mcp**](https://github.com/perplexity-ai/mcp-server) — Web search grounded in real sources

## Write your own

A skill is just a markdown file with instructions. Put it in `.claude/skills/your-skill/SKILL.md` and Claude will follow it when invoked.

The fastest way: ask Claude to build one for you. "Build me a skill that checks Airtable every morning and messages me new leads."

## Contributing

PRs welcome. Add your skill in the right category folder with a `SKILL.md` file. Community skills with their own repos go in the table above — open a PR to add yours.

---

Part of the [GhostClaw](https://ghostclaw.io) ecosystem.
