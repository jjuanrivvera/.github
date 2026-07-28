# jjuanrivvera

Open-source developer tools: agent-ready CLIs for real-world APIs, Canvas LMS libraries, and the factory that builds them.

## Star Projects

### CLI Factory

- **[cliwright](https://github.com/jjuanrivvera/cliwright)** — a spec-gated CLI factory. Point it at any REST API and it forges a complete Go + Cobra CLI — keyring auth, named profiles, MCP server, agent guard, CI/CD, signed releases — looping until a deterministic acceptance gate (`make verify`) passes.
- **[cliwright-showcase](https://github.com/jjuanrivvera/cliwright-showcase)** — registry + showcase site for CLIs built with cliwright. Add yours with a PR.

CLIs built or hardened with the playbook:

| CLI | API | Highlights |
|---|---|---|
| [tgctl](https://github.com/jjuanrivvera/tgctl) | Telegram Bot API | messages, chats, webhooks, command menu, MCP server, agent guard |
| [slackctl](https://github.com/jjuanrivvera/slackctl) | Slack | live event stream (Socket Mode/RTM), local SQLite store + search, MCP server, agent guard |
| [n8nctl](https://github.com/jjuanrivvera/n8n-cli) | n8n | multi-instance profiles, GitOps apply/lint/diff, cross-instance promotion |
| [lsqueezy](https://github.com/jjuanrivvera/lemon-squeezy-cli) | Lemon Squeezy | stores, orders, subscriptions, license keys; JSON:API flattened to flat records |
| [alegra-cli](https://github.com/jjuanrivvera/alegra-cli) | Alegra accounting | invoices, payments, DIAN/SAT e-invoicing, `--dry-run` on every command |
| [canvas-cli](https://github.com/jjuanrivvera/canvas-cli) | Canvas LMS | 280+ commands, OAuth 2.0 + PKCE, interactive REPL |
| [adguard-cli](https://github.com/jjuanrivvera/adguard-cli) | AdGuard Home | 90%+ of the 81 API operations; the missing CLI for AdGuard Home |
| [garminctl](https://github.com/jjuanrivvera/garminctl) | Garmin Connect | health + activity data, keyring auth with automatic OAuth2 refresh |
| [wootctl](https://github.com/jjuanrivvera/wootctl) | Chatwoot | 144/144 API operations, profiles, keyring auth, agent guardrails |
| [atlassian](https://github.com/jjuanrivvera/atlassian-cli) | Jira, Confluence, JSM, Jira Software | all 1,143 documented operations, Cloud and Data Center, Markdown instead of ADF |
| [ms365](https://github.com/jjuanrivvera/ms365-cli) | Microsoft 365 (Graph) | Outlook mail, calendar, profile; multi-account device-code auth |
| [remoteok](https://github.com/jjuanrivvera/remoteok-cli) | RemoteOK | remote-job search with attribution built in |
| [torre](https://github.com/jjuanrivvera/torre-cli) | Torre.ai | job/people search, public genomes |
| [linkedin](https://github.com/jjuanrivvera/linkedin-cli) | LinkedIn Voyager (unofficial) | read-only job search, borrow-the-browser-session auth, ban-safety pacing |

They ship with Homebrew taps and Scoop buckets, cosign-signed releases with SBOMs, and MCP servers so AI agents can drive them safely.

### Agent Infrastructure

- **[edc](https://github.com/jjuanrivvera/edc)** — a transport-agnostic event injector for Claude Code, Codex & OpenCode, and half of [Plexus](https://github.com/jjuanrivvera/plexus). It opens a local, secret-gated `/inject` endpoint, so any process (a cron, a file watcher, another agent) can push an event that shows up as a turn in a running session. The session sleeps for free and wakes only when something real happens, no polling.
- **[tgctl-claude-channel](https://github.com/jjuanrivvera/tgctl-claude-channel)** — drive a Claude Code agent from Telegram: text, polls, buttons, media, and tool-permission approvals from your phone. Every Bot API call goes through `tgctl`.

### Canvas LMS Ecosystem

- **[canvas-lms-kit](https://github.com/jjuanrivvera/canvas-lms-kit)** — PHP SDK with 45 APIs, 95% coverage, 964 tests. [Packagist](https://packagist.org/packages/jjuanrivvera/canvas-lms-kit)
- **[canvas-cli](https://github.com/jjuanrivvera/canvas-cli)** — Go CLI with 280+ commands, OAuth 2.0 + PKCE. `brew install canvas-cli`
- **[canvas-lms-kit-laravel](https://github.com/jjuanrivvera/canvas-lms-kit-laravel)** — Laravel wrapper for the SDK

### Web & Portfolio

- **[website](https://github.com/jjuanrivvera/website)** — Astro portfolio with i18n (EN/ES/PT), blog, GA4

## All Repositories

<details>
<summary>CLIs & agent tooling (18 repos)</summary>

- cliwright, cliwright-showcase
- tgctl, slackctl, n8n-cli, lemon-squeezy-cli, alegra-cli, canvas-cli, adguard-cli, garminctl, wootctl, atlassian-cli, ms365-cli, remoteok-cli, torre-cli, linkedin-cli
- edc, tgctl-claude-channel
- Plus Homebrew taps and Scoop buckets for each distributed CLI
</details>

<details>
<summary>Canvas LMS (5 repos)</summary>

- canvas-lms-kit, canvas-cli, canvas-lms-kit-laravel
- canvas-lms-mcp, canvas-lms-analytics-dashboard
</details>

<details>
<summary>Web Applications (2 repos)</summary>

- website, design-house
</details>

<details>
<summary>Discord Bots (4 repos) ⚠️ Legacy - Discord.js v12</summary>

- jw-discord-bot (+ api)
- open-translator-discord-bot, tf-critics-discord-bot
</details>

<details>
<summary>Frameworks (1 repo) ⚠️ Experimental</summary>

- awesome-framework-core
</details>

## Tech Stack

**Languages**: Go, PHP, TypeScript, Python, JavaScript
**Frameworks**: Cobra, Laravel, FastAPI, Django, Astro, React, Vue.js
**Cloud**: GCP, AWS, Cloudflare
**Databases**: PostgreSQL, MySQL, MongoDB, Redis

## Contact

- **GitHub**: [@jjuanrivvera99](https://github.com/jjuanrivvera99)
- **LinkedIn**: [jjuanrivvera99](https://linkedin.com/in/jjuanrivvera99)
- **Website**: [jjuanrivvera.com](https://jjuanrivvera.com)
- **Email**: jjuanrivvera@gmail.com

---

*Most projects are MIT licensed. Check individual repos for details.*
