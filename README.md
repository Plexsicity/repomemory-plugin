# RepoMemory plugin for Claude Code

[RepoMemory](https://repomemory.dev) is shared project memory for AI coding
agents: the decisions, lessons, guardrails and tasks of a codebase, which
Claude Code, Cursor, Codex and other agents read before they change code.

This plugin connects Claude Code to it in one step:

- **The RepoMemory MCP server**: `brief`, `recall`, `record_entry`,
  `claim_task` and `retire_entry`.
- **The project's brief at the start of every session**: its guardrails, open
  tasks and latest decisions are in the context before your first prompt, at
  startup, after `/clear` and after compaction. No rules to paste, and no
  guardrail missed because the agent forgot to look.

## Install

In Claude Code:

```text
/plugin marketplace add Plexsicity/repomemory-plugin
/plugin install repomemory@repomemory
```

Or from a terminal:

```bash
claude plugin marketplace add Plexsicity/repomemory-plugin
claude plugin install repomemory@repomemory
```

Claude Code asks for your RepoMemory API key when you enable the plugin.
Create one at <https://repomemory.dev/dashboard/keys> (the free plan needs no
card). If it doesn't ask, or to change the key later, run
`/plugin configure repomemory@repomemory`. The key is kept in your system's
credential store.

## How it picks the project

The brief is for the first of:

1. `REPOMEMORY_PROJECT`, if set.
2. The project named in the repository's `CLAUDE.md` or `AGENTS.md`
   (`## Project memory (RepoMemory, project "owner/repo")`).
3. The git remote's `owner/repo`.
4. The folder's name.

If no ledger has that name yet, the agent is shown the account's projects
instead, so it can use the right one rather than start a second.

## What it sends

The hook sends your API key and the project name to
`https://api.repomemory.dev/v1/brief`. Reading the brief is free. Nothing else
leaves your machine unless the agent calls a RepoMemory tool. See the
[privacy policy](https://repomemory.dev/privacy).

## Requirements

Node 18 or later on `PATH`, for the session-start hook. Without Node, without
a key, or if the API can't be reached, sessions start as usual with a one-line
notice, and the MCP tools still work.

## Development

```bash
npm test                                   # the hook's tests
claude plugin validate plugins/repomemory  # the plugin
claude plugin validate .                   # the marketplace
claude --plugin-dir plugins/repomemory     # try local changes
```

`REPOMEMORY_URL` points the hook at another API. Details are in
[plugins/repomemory/README.md](plugins/repomemory/README.md).

## License

MIT
