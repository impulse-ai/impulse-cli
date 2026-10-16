# Impulse CLI

`impulse` is the command-line client for a hosted Impulse AI (Ordinant)
deployment: browser-based login, job management, and an MCP integration
for AI coding assistants like Claude and Codex.

This repository distributes prebuilt releases of the CLI. It does not
contain the CLI's source, which lives in Impulse AI's private engineering
repository.

## Installing

Download the archive for your platform from the
[latest release](https://github.com/impulse-ai/impulse-cli/releases/latest):

```sh
curl -LO https://github.com/impulse-ai/impulse-cli/releases/latest/download/impulse-vX.Y.Z-<platform>.tar.gz
curl -LO https://github.com/impulse-ai/impulse-cli/releases/latest/download/checksums.txt
shasum -a 256 -c checksums.txt --ignore-missing
tar xzf impulse-vX.Y.Z-<platform>.tar.gz
sudo mv impulse /usr/local/bin/impulse   # or anywhere already on PATH
impulse version
```

Replace `vX.Y.Z` with the actual release version and `<platform>` with
`macos-arm64` or `linux-amd64` — the only two supported today.

This repository is public, so no authentication is needed to download a
release.

## Logging in

```sh
impulse auth login --url https://<your-impulse-deployment>
```

This opens a browser to approve the connection. Use the URL your Impulse
AI account team gave you; `impulse` has no built-in default production
endpoint. The account you approve with must be a workspace administrator.

```sh
impulse auth status     # confirm login and see token expiry
impulse status          # target and live reachability
impulse auth logout
```

Once logged in, every other command below defaults to that same target —
you don't need to repeat `--url` or `--env`.

## Working with jobs

```sh
impulse jobs list
impulse jobs status  --run-id RUN_ID
impulse jobs events  --run-id RUN_ID

impulse jobs start \
  --subject "Describe the job" \
  --objective "Describe the intended outcome" \
  --execution cloud \
  --deadline 6h

impulse jobs pause     --run-id RUN_ID
impulse jobs resume    --run-id RUN_ID
impulse jobs cancel    --run-id RUN_ID --reason "..."
impulse jobs archive   --run-id RUN_ID
impulse jobs unarchive --run-id RUN_ID
impulse jobs configure --run-id RUN_ID --effort deep --compute accelerated
```

`--effort` is one of `focused`, `balanced`, or `deep`; `--compute` is one
of `economy`, `balanced`, or `accelerated`. Every mutating command accepts
`--idempotency-key` — reuse the same value to safely retry after an
uncertain response, rather than risk a duplicate action.

## Using it from Claude or Codex (MCP)

`impulse mcp` speaks MCP over stdio, backed by whatever credentials
`impulse auth login` already saved on your machine. No token goes in the
client's config.

**Claude Desktop / Claude Code** (`claude_desktop_config.json` or `.mcp.json`):
```json
{
  "mcpServers": {
    "impulse": {
      "command": "/absolute/path/to/impulse",
      "args": ["mcp"]
    }
  }
}
```

**Codex** (`~/.codex/config.toml`):
```toml
[mcp_servers.impulse]
command = "/absolute/path/to/impulse"
args = ["mcp"]
```

Restart the client after editing its config. The target is resolved once
at startup from your saved login and pinned for that process's life —
changing it means logging into a different target and restarting the
client.

It exposes six tools: `impulse_health`, `impulse_jobs`, `impulse_status`,
`impulse_events`, `impulse_start`, `impulse_job_control`. No
authentication tool is ever exposed — if a call fails with "not logged
in," run `impulse auth login` yourself in a terminal; never share a token
with the agent.

Teach an agent to use these tools without being told, in either client:

```sh
impulse skills install claude   # or: impulse skills install codex
```

Reinstalling is safe: an unchanged copy is left alone, and a local edit
is never overwritten — the new version is written alongside it instead,
for you to compare and merge by hand.

## Getting help

File an issue in this repository, or contact your Impulse AI account team.
