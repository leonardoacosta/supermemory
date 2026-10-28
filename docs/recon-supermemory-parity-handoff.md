# Handoff: Recon ↔ Supermemory local parity

## Goal
Continue testing local Supermemory against the same 1,765 Recon documents accepted by hosted Supermemory. Preserve existing local DB/volume and stable document IDs. Do not delete records or restart/recreate services unnecessarily. User previously approved hosted Recon import and domain split (Personal / Priceless / Brown / Review).

## Current facts (last observed 2026-10-07)
- Local service container: `company-brain-private-supermemory-1`; named `/data` volume preserved.
- Routes returned HTTP 200: `http://127.0.0.1:6767/`, `https://homelab.tail296462.ts.net:16767/`.
- Model route set in `/home/nyaptor/.local/share/supermemory-local/provider.env`: `OPENAI_MODEL=cx/gpt-6-luna`; previous `gh/gpt-6-luna` combo fell back to retired route and returned HTTP 466. API key intentionally omitted here.
- Container health was `healthy`, restart count 75, memory ~1.5GiB/3GiB. Avoid unnecessary restart; inspect current state first.
- Latest count from `/v3/documents/list` for `recon-parity`: 1,765 total; 903 done, 516 failed, 323 indexing, 22 embedding, 1 extracting. Counts are a snapshot and may have changed.
- Retry script `/home/nyaptor/.jcode/scratch/retry_failed_parity.py` was edited to add bounded transport retries. Its run completed; summary 754 accepted, one not accepted (reported transport retry count 1). Acceptance does not imply successful processing.
- Local `/v4/search` using `searchMode: documents`, `containerTag: Brown`, query `Who owns the ADO Fireball project and which codebases belong to it?` returned relevant results: `raw/legacy/codebases/db-document-organization-center.md` (0.820), `raw/legacy/projects/ado-fireball.md` (0.819), `raw/legacy/codebases/b3.md` (0.817), `raw/legacy/codebases/brownandbrown-its-fireball.md` (0.812), then another `ado-fireball.md` record (0.812).
- Hosted comparison not completed: hosted Supermemory MCP `search_memory` timed out connecting after 30s. 1Password item read timed out after 12s. Previous hosted exploratory results were preliminary; do not claim parity.

## Host-local DNS/TLS probe (2026-10-18)

These probes describe only the caller host's current network and resolver vantage; they do not establish general public reachability or application/MCP health.

- For `hyperland.mcp.leonardoacosta.dev`, host NSS/systemd-resolved returned A `192.168.1.101` and no AAAA. The configured Tailscale split route for `leonardoacosta.dev` forwards to `100.73.182.4`; a direct read-only Tailscale DNS query returned the same A (TTL 10) and no AAAA. No differing split-horizon answer was observed between these host-local paths. The systemd-resolved host answer was cached.
- Direct HTTPS to `192.168.1.101` with `curl --resolve` succeeded from this host. TLS 1.3 negotiated `TLS_AES_128_GCM_SHA256`; strict hostname/certificate verification passed (verify return 0). The Let's Encrypt YE1 certificate SAN/subject covers `hyperland.mcp.leonardoacosta.dev`, valid 2026-09-29 through 2026-12-28; SHA-256 fingerprint `D8:DD:FE:F2:D6:A9:1F:5B:E7:BC:E6:A0:B2:07:4C:EE:C9:AD:37:D0:3E:94:17:15:9C:A7:7C:C9:8B:D1:47:6F`. HTTP/2 `GET /` returned 404, which confirms a TLS/HTTP response at that path only, not that an application route or MCP operation works.
- For public DNS, bounded Cloudflare DoH and Google DoH A/AAAA/CNAME queries returned NOERROR with empty answers and SOA authority for `leonardoacosta.dev`. Google DoH NS returned `chip.ns.cloudflare.com` and `uma.ns.cloudflare.com`. In contrast, caller-local `getent` resolved the name to private `192.168.1.101`, consistent with a local/split DNS view; the source/cause is unconfirmed. This is not a verified public-vs-private divergence for the endpoint because the public queries concerned `leonardoacosta.dev` records, and no external authoritative/direct endpoint query was made. HTTP egress IP was not observed.
- Methods were read-only: DoH HTTPS requests, `getent`, `resolvectl`, Tailscale DNS status/query, `openssl s_client` with hostname/return-error verification, and `curl --resolve`. No DNS/config writes, service mutations, credentials, or desktop actions.

### Limits and unresolved questions

Only this host's current resolver/network and one resolved private address were tested. No alternate network, direct authoritative DNS, DNSSEC behavior, other public recursive services beyond the stated two, endpoint routes beyond `/`, application-specific MCP behavior, or endpoint authentication/state was verified. The public DNS view for the endpoint and the source of the local private answer remain unestablished. These probes do not validate the separate Supermemory parity claims above.

## Next actions
1. Check current container health, restart count, and live `recon-parity` statuses through the local API. Keep named volume/data intact.
2. Inspect failed-record errors and current processing stability before any further retry. Retry only recoverable items, idempotently with existing custom IDs and bounded transport handling. Avoid broad duplicate submissions if statuses have changed.
3. When hosted access works, run identical document-only query parameters on hosted and local. Record query, result identifiers/source paths, rank/scores, and overlap; ensure comparable domain/tag and search mode.
4. Keep outcome qualified: local top results show retrieval works, but ingestion was materially incomplete at last observation. Hosted comparison remains pending.

## Interpretation / constraints
The intended outcome is a controlled retrieval comparison over the hosted-accepted 1,765-document set, not just successful local submission. Earlier requeue and route corrections were consistent with preserved-volume/stable-ID instructions. No destructive actions occurred. Hosted credential and MCP access are external blockers.

## Suggested skills
- `/recovery` — resume interrupted work from repo and live evidence.
- `/systematic-debugging` — diagnose ongoing failures/restarts before retrying.
- `/verification-before-completion` — verify end-to-end claims before closeout.
- `/supermemory` — if the next step requires Supermemory API behavior or management.