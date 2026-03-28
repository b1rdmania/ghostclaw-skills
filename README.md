# GhostClaw Skills

Curated skills for [GhostClaw](https://github.com/b1rdmania/ghostclaw). Each skill is a markdown file — drop it into `.claude/skills/` and it works.

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
| `/add-gmail-agent` | Read, search, send email via Gmail |
| `/add-voice-transcription` | Transcribe voice notes (ElevenLabs Scribe) |
| `/add-voice-reply` | Reply with voice notes (ElevenLabs TTS) |
| `/add-morning-briefing` | Scheduled daily/weekly summaries |
| `/add-heartbeat` | Health checks, alert only when needed |
| `/add-telegram-swarm` | Multi-agent teams in Telegram groups |
| `/pr-babysitter` | Monitor PRs, fix CI, resolve reviews |
| `/run-ralph` | Autonomous overnight task loop |
| `/add-slack` | Slack channel (Socket Mode) |
| `/add-discord` | Discord channel |
| `/add-update-check` | Check for new GhostClaw versions |
| `/update-ghostclaw` | Safe update with backup and rollback |

### Marketing & Growth (`marketing/`)
Full marketing toolkit. CRO, copywriting, SEO, outreach, strategy.

| Skill | What it does |
|-------|-------------|
| `/page-cro` | Conversion rate audit for any page |
| `/copywriting` | Write/rewrite marketing copy |
| `/seo-audit` | Technical and on-page SEO audit |
| `/content-strategy` | Topic clusters, editorial calendar |
| `/competitor-alternatives` | Comparison and alternative pages |
| `/email-sequence` | Drip campaigns, onboarding flows |
| `/cold-email` | B2B outreach that gets replies |
| `/launch-strategy` | Product Hunt, go-to-market planning |
| `/pricing-strategy` | Tiers, packaging, monetization |
| `/social-content` | LinkedIn, Twitter/X, Instagram content |
| `/ad-creative` | Ad copy at scale |
| `/site-architecture` | Page hierarchy, navigation, URLs |
| `/programmatic-seo` | SEO pages at scale with templates |
| `/signup-flow-cro` | Optimise signup/registration flows |
| `/referral-program` | Referral and affiliate programs |
| `/marketing-ideas` | Brainstorm marketing strategies |
| `/copy-editing` | Edit and improve existing copy |
| `/paid-ads` | Google, Meta, LinkedIn ad campaigns |
| `/form-cro` | Lead form optimisation |
| `/popup-cro` | Popup and modal optimisation |
| `/onboarding-cro` | Post-signup activation flows |
| `/paywall-upgrade-cro` | In-app upgrade screens |
| `/churn-prevention` | Cancel flows, save offers, dunning |
| `/free-tool-strategy` | Build free tools for lead gen |
| `/revops` | Revenue operations, lead lifecycle |
| `/sales-enablement` | Pitch decks, objection handling |
| `/marketing-psychology` | Behavioral science for marketing |
| `/product-marketing-context` | Set up product/audience context |

### Design & Build (`design/`)
Design systems, structured data, analytics, testing.

| Skill | What it does |
|-------|-------------|
| `/design` | Design system framework |
| `/schema-markup` | JSON-LD structured data |
| `/ai-seo` | Optimise for AI search engines |
| `/analytics-tracking` | GA4, event tracking, UTMs |
| `/ab-test-setup` | Plan and implement A/B tests |
| `/domain-check` | Domain availability (600+ TLDs) |

## Write your own

A skill is just a markdown file with instructions. Put it in `.claude/skills/your-skill/SKILL.md` and Claude will follow it when invoked.

The fastest way: ask Claude to build one for you. "Build me a skill that checks Airtable every morning and messages me new leads."

## Contributing

PRs welcome. Add your skill in the right category folder with a `SKILL.md` file.

---

Part of the [GhostClaw](https://ghostclaw.io) ecosystem.
