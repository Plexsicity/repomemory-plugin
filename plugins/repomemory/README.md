# RepoMemory plugin for Claude Code

Gives Claude Code the project's memory without any setup in the repository:

- **The RepoMemory MCP server** (`https://api.repomemory.dev/mcp`): `brief`,
  `recall`, `record_entry`, `claim_task` and `retire_entry`.
- **The brief at the start of every session**: a SessionStart hook loads the
  project's guardrails, open tasks and latest decisions, lessons and notes into
  the context at startup, after `/clear` and after compaction. The agent
  starts where the last session stopped, and never misses a guardrail because
  it forgot to call `brief`.

## Install

```text
/plugin marketplace add Plexsicity/repomemory-plugin
/plugin install repomemory@repomemory
```

Claude Code asks for your API key when you enable the plugin. Create one at
https://repomemory.dev/dashboard/keys. It is kept in your system's credential
store. If it doesn't ask, or to change it later, run
`/plugin configure repomemory@repomemory`.

## Which project

The hook loads the brief for the first of:

1. `REPOMEMORY_PROJECT`, if set.
2. The project named in the repository's `CLAUDE.md` or `AGENTS.md` memory
   block (`## Project memory (RepoMemory, project "owner/repo")`, which
   `repomemory init` writes).
3. The git remote's `owner/repo`.
4. The folder's name.

If no ledger has that name yet, the context lists the account's projects
instead, so the agent can pick the right one rather than start a second
ledger.

## Requirements and failures

The hook needs Node 18 or later on `PATH`. Without it, or without a key, or if
the API can't be reached, the session starts as usual: you see a one-line
notice. With a key, the MCP tools' calls are charged to the key's account;
without one, Claude Code asks you to sign in with your Plexsicity account the
first time a tool is used (once the server treats an empty `X-API-Key` as
absent). The brief is kept under 9,000 characters
(Claude Code shows only a preview of anything over 10,000).

`REPOMEMORY_KEY` works in place of the plugin setting, and `REPOMEMORY_URL`
points the hook at another API (for tests).

## Files

| File | What it is |
|---|---|
| `.claude-plugin/plugin.json` | The manifest, with the `repomemory_api_key` setting |
| `.mcp.json` | The MCP server, with that key as its `X-API-Key` header |
| `hooks/hooks.json` | The SessionStart hook (`startup`, `clear`, `compact`) |
| `hooks/session-start.mjs` | Finds the project, calls `POST /v1/brief`, formats the context |
| `../../.claude-plugin/marketplace.json` | The marketplace that lists the plugin |

Check them with `claude plugin validate plugins/repomemory` and
`claude plugin validate .`. The tests (`npm test` at the repository root) run
in CI. Try local changes with `claude --plugin-dir plugins/repomemory`.
