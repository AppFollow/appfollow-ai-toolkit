# Security

## Reporting

Report suspected vulnerabilities to `makariy.vasyuta@appfollow.io`.

## Public-mirror posture

This repository is a **curated public mirror**. Only an explicit path allowlist is published, as a
single snapshot commit — everything else stays internal. The mirror ships no secrets and no API
client; it references public AppFollow MCP tools only. All data flows through the user's own OAuth
MCP session.

## Credentials

The toolkit reads no credential — not from your environment, not from a file on disk, not from a
prompt. It has no credential surface to read one with:

- The only MCP server it ships is the remote `https://mcp.appfollow.io/mcp`, declared in
  `.mcp.json` with a `type` and a `url` and nothing else. There is no `env`, no `headers`, no
  `headersHelper`, and no local `command` whose process would inherit your environment.
- No `${...}` interpolation appears in any shipped MCP configuration, so neither an environment
  value nor a `${user_config.KEY}` can be substituted into a request.
- The plugin declares no `userConfig`, so your agent host never prompts you for a token or an
  API key on this plugin's behalf.
- Authentication is your agent host's own OAuth flow against the AppFollow MCP server. The
  resulting session belongs to the host, never to this plugin.

Each of these points is pinned as a test assertion over the shipped files, so adding a credential
surface fails the build instead of passing review unnoticed.
