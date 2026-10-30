# Installing RepoMemory (for Cline and other agents)

RepoMemory is a hosted MCP server, so there's nothing to clone or build.

1. Ask the user for a RepoMemory API key. They can make one at
   https://repomemory.dev/dashboard/keys (the free plan needs no card).
2. Add this to `cline_mcp_settings.json` under `mcpServers`, with the key in
   place of `<YOUR_API_KEY>`:

```json
{
  "mcpServers": {
    "repomemory": {
      "type": "streamableHttp",
      "url": "https://api.repomemory.dev/mcp",
      "headers": { "X-API-Key": "<YOUR_API_KEY>" }
    }
  }
}
```

3. Check it works by calling the `brief` tool with no arguments. It lists the
   account's projects.

Its tools are `brief`, `recall`, `record_entry`, `claim_task` and
`retire_entry`. Name the project `owner/repo` after the git remote. Docs:
https://repomemory.dev/docs
