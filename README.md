# Adroit Agent

Adroit for coding agents. Launch White Box and Black Box pentests, fix the findings in your repo
and verify each fix with a retest, all from Claude Code, Codex or any MCP client. No dashboard, no
exported PDF passed around for someone to fix.

```
You:    /adroit:findings
Claude: 3 open findings in acme/api. Start with the CRITICAL SQL injection in /v1/orders.
You:    /adroit:fix 7f3c…
Claude: Wrote a failing test, fixed the query, opened PR #142 and commented it on the finding.
You:    (after merging) /adroit:retest 7f3c…
Claude: REMEDIATED. The finding is now SOLVED in Adroit.
```

## What's inside

| Piece | What it does |
|---|---|
| MCP server `adroit` | Connects the agent to your Adroit organization (projects, pentests, findings, retests) |
| Skill `adroit-remediation` | The workflow: map the repo to its project, fix findings test-first, record them in Adroit, retest |
| `/adroit:findings` | Open findings for the current repository |
| `/adroit:fix <id>` | Fix one finding and record the PR in Adroit |
| `/adroit:retest <id>` | Retest one finding and report the verdict |
| `/adroit:pentest` | Launch a White Box or Black Box pentest after you confirm target and scope |

## Install in Claude Code

```
/plugin marketplace add Adroit-Security/Agent
/plugin install adroit@adroit
```

Then open `/mcp`, pick `adroit` and log in with your Adroit account. Nothing to copy or paste.

### Using an API key instead

For CI or headless use, or if your Adroit instance doesn't offer the OAuth login yet, create an
API key in Adroit (Settings > API Keys) and register the server with it:

```
claude mcp add --transport http adroit-key https://platform.adroit-security.net/api/mcp \
  --header "Authorization: Bearer $ADROIT_API_KEY"
```

Keep the key in an environment variable or your secret manager, never in a file you commit. A key
with read access plus "update vulnerabilities" is enough to fix findings; launching pentests also
needs "create pentests".

## Install in Codex

Register the server and log in:

```
codex mcp add adroit --url https://platform.adroit-security.net/api/mcp
codex mcp login adroit
```

Then copy the skill so Codex picks it up:

```
git clone https://github.com/Adroit-Security/Agent
mkdir -p ~/.codex/skills && cp -R Agent/skills/adroit-remediation ~/.codex/skills/
```

Ask Codex "what does Adroit say about this repo?" to start.

## Other MCP clients

Cursor, Windsurf, VS Code and any client that speaks Streamable HTTP can use the server at
`https://platform.adroit-security.net/api/mcp`. The skill is plain Markdown
(`skills/adroit-remediation/SKILL.md`), so you can paste it into your client's rules or
instructions.

## Safety

- **Pentest evidence is treated as untrusted.** Findings are written from content the tested
  target controls, so the skill never runs commands or follows instructions found inside them,
  and never copies credentials from evidence into code or comments.
- **Humans decide the judgment calls.** The agent proposes `FALSE_POSITIVE` or `ACCEPTED_RISK` but
  only applies them after you confirm, and asks before launching a pentest.
- **`SOLVED` comes from a retest**, not from a merged PR.
- **The agent can do what your Adroit role allows and nothing more.** Permissions are enforced by
  Adroit on every call.
