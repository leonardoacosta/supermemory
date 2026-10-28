# Hosted vs local Supermemory parity check (2026-10-16)

## Scope
Read-only comparison requested against the existing hosted Supermemory corpus. No hosted records, credentials, server config, permissions, or account state changed. No hosted query ran because the existing MCP server failed during initialization.

## Evidence
- Prior handoff: `docs/recon-supermemory-parity-handoff.md` records the hosted comparison as pending, a prior `search_memory` connection timeout, and last known local submission status (now superseded by the current session's report that all 1,765 local documents completed).
- Existing `Supermemory` MCP tool discovery returned `who_am_i`; invocation failed before tool execution with: `MCP server 'Supermemory' failed to connect: Failed to connect to MCP server 'Supermemory': MCP server 'Supermemory' failed to initialize: MCP server 'Supermemory' exited (connection closed): channel closed`.
- Jcode log on 2026-10-15 records the server failing to read the existing 1Password reference `op://JCode CLI/rl7rfs2jypk3gwxt5hzdaabjyu/credential` because the 1Password authorization prompt was dismissed; subsequent registration errors likewise report channel closed. Secret values intentionally not accessed or recorded.
- Current failure is before MCP identity/corpus calls. It is not evidence of hosted corpus absence, permissions, or data parity.
- No application/source modification is clearly required. No retry loops, reauthentication, secret rotation, hosted query, or global configuration change attempted.

## Next safe step
Human restores/unlocks the already-configured 1Password desktop authorization in the normal local workflow; then restart/reload only the existing MCP connection and call `who_am_i` once. If it connects, verify namespace corpus count/completion first, then run identical document-only Fireball query and compare source IDs/ranks/scores against local API results. Do not use local API credentials as hosted credentials.

## Limits
No hosted corpus coverage, query relevance, latency, or overlap measurement can be reported. The local completion state was supplied in session context; this task did not re-query local API. Prior preliminary retrieval evidence in the handoff is not comparable parity evidence.
