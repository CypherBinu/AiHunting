name: information-disclosure-hunter
description: Advanced, low-noise information-disclosure hunting for explicitly authorized bug-bounty and VDP assets. Combines Google dorks, Wayback CDX, VirusTotal, urlscan.io, passive asset sources, JavaScript/source-map review, safe verification, deduplication, and evidence-based reporting.
---
# Advanced Information Disclosure Hunter

## 1. Evidence ledger
Track:
`ID | host | path | discovery source | scope | current status | sensitivity | impact | confidence | disposition | evidence reference`

Normalize and deduplicate URLs. Preserve query parameters only when relevant, redact tokens/PII, and group URLs sharing the same root cause.

Statuses: `Confirmed`, `Needs manual review`, `Informational`, `False positive`, `Historical-only`, `Unavailable`, `Out of scope`.

## 2. Google dorks
Replace `example.com` only with a confirmed in-scope domain. Search results are leads, not proof of current exposure.

### Potentially sensitive extensions
```text
site:example.com ext:log
site:example.com ext:txt
site:example.com ext:conf
site:example.com ext:cnf
site:example.com ext:ini
site:example.com ext:env
site:example.com ext:sh
site:example.com ext:bak
site:example.com ext:backup
site:example.com ext:swp
site:example.com ext:old
site:example.com ext:git
site:example.com ext:svn
site:example.com ext:htpasswd
site:example.com ext:htaccess
site:example.com ext:json
site:example.com ext:yaml
site:example.com ext:yml
site:example.com ext:toml
site:example.com ext:xml
site:example.com ext:sql
site:example.com ext:db
site:example.com ext:sqlite
site:example.com ext:zip
site:example.com ext:tar
site:example.com ext:gz
site:example.com ext:7z
site:example.com ext:pdf
site:example.com ext:doc
site:example.com ext:docx
site:example.com ext:xls
site:example.com ext:xlsx
site:example.com ext:csv
site:example.com ext:map
```

### Directory indexes
```text
site:example.com intitle:"index of"
site:example.com intitle:"index of" "parent directory"
site:example.com intitle:"index of" "backup"
site:example.com intitle:"index of" "backups"
site:example.com intitle:"index of" "logs"
site:example.com intitle:"index of" "uploads"
site:example.com intitle:"index of" "files"
site:example.com intitle:"index of" "database"
site:example.com intitle:"index of" "dump"
site:example.com intitle:"index of" "sql"
site:example.com intitle:"index of" ".git"
site:example.com intitle:"index of" ".svn"
```

### Documents, logs, errors, configuration
```text
site:example.com filetype:pdf "confidential"
site:example.com filetype:doc "restricted"
site:example.com filetype:docx "internal use only"
site:example.com filetype:xlsx "password"
site:example.com filetype:csv "email"
site:example.com filetype:log "error"
site:example.com filetype:log "stack trace"
site:example.com filetype:sql "CREATE TABLE"
site:example.com ext:bak OR ext:old OR ext:backup
site:example.com "Traceback (most recent call last)"
site:example.com "Stack trace:"
site:example.com "SQL syntax"
site:example.com "Fatal error"
site:example.com "Warning:" " on line "
site:example.com "BEGIN PRIVATE KEY"
site:example.com "client_secret"
site:example.com "access_token"
```
Notes: `ext:~` is not a reliable backup-file query. Common words create false positives. Public docs, sample configs, client-side identifiers, and intentionally public files are not automatically vulnerabilities. Never copy full secrets into logs or reports.

## 3. Wayback Machine CDX
Use CDX to discover historical paths and captures. Example query patterns:
```text
https://web.archive.org/cdx/search/cdx?url=example.com/*&output=json&fl=timestamp,original,statuscode,mimetype,digest&filter=statuscode:200&collapse=urlkey
https://web.archive.org/cdx/search/cdx?url=*.example.com/*&output=json&fl=timestamp,original,statuscode,mimetype,digest&collapse=urlkey
```
For candidate extension filtering, retrieve a limited URL inventory and filter locally for `.env`, `.log`, `.conf`, `.ini`, `.bak`, `.backup`, `.old`, `.sql`, `.zip`, `.json`, `.yml`, `.yaml`, `.map`, `.git`, and `.svn`. CDX query/filter syntax and wildcard behavior may vary; consult current Internet Archive documentation.

Process:
1. Start with narrow queries and sensible limits/pagination.
2. Extract URL, timestamp, status, MIME type, and digest before fetching archived content.
3. Deduplicate, normalize, and remove out-of-scope/third-party hosts.
4. Flag paths containing `backup`, `old`, `debug`, `config`, `logs`, `dump`, `export`, `internal`, `staging`, `test`, `error`, `trace`, `upload`, `private`, `archive`, `database`, `swagger`, `openapi`, `graphql`, or `sourceMappingURL`.
5. Mark results `Historical-only` until current exposure is separately verified under policy.
6. An archived 200 is not proof of current exposure or sensitivity. Never republish archived secrets.

## 4. VirusTotal passive enrichment
Use the account's permitted API plan and quotas. Store keys in environment variables/secret storage; never put keys in prompts, source code, terminal logs, or reports. If the API is unavailable, skip it rather than seeking a bypass.

Potential sources, subject to account access:
- Domain object metadata.
- Direct subdomain relationships (not necessarily recursive).
- Historical SSL certificates/certificate-associated names.
- Related URL objects and available domain relationships.
- Existing reputation and scan metadata.

Workflow:
1. Query the exact in-scope domain.
2. Retrieve only relationships available to the account and relevant to asset inventory.
3. Normalize hostnames and independently compare each against the written scope.
4. Use results to identify forgotten/legacy hosts or candidate URLs, not to presume authorization.
5. Do not upload private URLs, credentials, target files, or sensitive artifacts.
6. A reputation verdict is not proof of information disclosure.

Example API request pattern:
```bash
# Set this locally using a secret manager or environment, not a literal key in a shared script.
export VT_API_KEY="YOUR_KEY_FROM_LOCAL_SECRET_STORE"
curl -sS -H "x-apikey: ${VT_API_KEY}" \
  "https://www.virustotal.com/api/v3/domains/example.com"
```
Do not commit `.env` files or shell history containing keys.

## 5. urlscan.io historical search
Search existing scans before considering any new submission. Consult current API documentation for fields, quotas, access tiers, and visibility.

Example:
```text
https://urlscan.io/api/v1/search/?q=domain:example.com
```
Review, where available:
- Page URL, hostname, scan date, redirects.
- Requests to same-domain and third-party hosts.
- JavaScript assets, source-map references, API routes, static assets.
- Publicly available response metadata, DOM/screenshot evidence.
- Unexpected staging/development hosts and historical paths.
- Certificate/hostname datasets when supported by the account.

Process:
1. Search existing scans for the in-scope domain.
2. Filter by date and relevant host/path terms; paginate using documented parameters.
3. Record scan timestamp and visibility; old results are historical clues only.
4. Review only minimum necessary data; do not dump sensitive response bodies.
5. Do not submit new scans by default. Public submissions may expose URLs/results. Obtain authorization and select the least-exposing supported visibility first.
6. A scan entry does not prove current reachability or vulnerability.

## 6. Other passive and local sources
Use only sources allowed by policy; prefer existing local output over repeated collection.

- **Certificate Transparency:** discover certificate names; verify ownership and scope independently.
- **Passive DNS:** review historical resolutions, CNAMEs, MX/TXT records, and nameservers when relevant. DNS relationships do not grant authorization; do not change DNS or test takeover claims without permission.
- **Public code search:** review clearly organization-owned repositories, commit history, CI logs, package artifacts, and attachments. Never test discovered credentials; report redacted locations.
- **URL inventories:** if installed and permitted, use low-noise passive sources such as `gau` or `waybackurls` for confirmed hosts. Deduplicate and scope-filter before requests.
- **JavaScript/source maps:** inspect in-scope assets for accidental secrets, internal endpoints, debug flags, source paths, and test configuration. Source maps alone are not automatically a vulnerability.
- **Public artifacts/storage:** check policy-permitted `.env`, config/backup files, `.git`/`.svn`, logs, crash reports, build outputs, archives, SQL dumps, directory listings, storage objects, CI output, and documents. Verify ownership before following third-party links.
- **Application/API:** inspect routes discovered through authorized app use, docs, or supplied traffic. Look for sensitive fields returned without expected authorization, verbose diagnostics, private tenant data, internal IDs, and debug fields. Use only own authorized accounts or program fixtures; never enumerate other users.
- **Document metadata:** inspect minimum metadata for GPS, usernames, authors, internal paths, comments, revision history, and embedded properties. Contextualize impact; metadata alone may be informational.

## 7. Safe verification
For each candidate:
1. Confirm exact host/path is in scope.
2. Distinguish search-only, archive-only, and current evidence.
3. If allowed, make one minimal read-only request initially; record status, content type, redirects, and relevant headers.
4. Inspect only a small necessary excerpt.
5. Determine whether content is non-public and materially sensitive.
6. Check whether access is intended; do not bypass controls beyond policy.
7. Explain realistic impact without using secrets or accessing additional data.
8. Keep evidence reproducible and sanitized.
9. Deduplicate by root cause.
10. Assign status and explain the reasoning.

Do not open/read a secret-bearing file just to prove it. Prefer headers, metadata, minimal redacted excerpts, or access behavior. If even a small excerpt risks exposing personal/highly sensitive data, stop and report only location and access behavior.

## 8. False-positive reduction
Reject or downgrade candidates when:
- Only a search snippet or archived capture exists.
- Content is public documentation, synthetic test data, examples, or intentionally public.
- A value is a public client identifier, not a secret.
- A hostname, version, path, filename, stack trace, or identifier has no credible impact by itself.
- Expected authentication/authorization is enforced.
- A file is gone/blocked or only available via stale cache.
- Ownership is uncertain, the asset is third-party, or policy excludes it.
- Multiple URLs share one root cause.

Confidence:
- **High:** current in-scope evidence, sensitivity confirmed with minimal redacted proof, impact clear.
- **Medium:** plausible disclosure but sensitivity/impact needs manual review.
- **Low:** passive clue only, stale archive, ambiguous content, or no concrete impact.

## 9. Severity and reporting
Follow the program rubric. Otherwise consider sensitivity, authentication/privilege, affected scope without bulk enumeration, realistic misuse, compensating controls, and whether data is already public.

Report template:
- Title
- Affected in-scope asset/path
- Summary
- Preconditions/authentication
- Minimal read-only reproduction steps
- Observed result (redacted)
- Expected result
- Evidence (timestamp, status, sanitized excerpt)
- Evidence-backed impact
- Severity rationale
- Remediation (remove public artifact, enforce authorization, minimize response fields, disable verbose errors, rotate exposed secrets, review logs)

Never include full credentials, private keys, tokens, personal data, or unrelated users' records. Never claim account takeover, breach, or credential validity without safe authorized proof.

## 10. Execution discipline
- Inspect installed tools/docs before assuming availability.
- Explain command purpose before execution.
- Prefer offline parsing before network requests.
- Use low concurrency, timeouts, backoff, and documented API quotas.
- Do not install packages, change system configuration, or launch active scans without explicit approval.
- Save results only when requested and redact every output.
- If API keys/access are missing, mark the source as unavailable and continue with other sources.
- Never send sensitive data to third-party services.

## 11. Final deliverable
Return:
1. Scope and policy summary.
2. Sources checked: Google, CDX, VirusTotal, urlscan.io, CT/DNS, URL inventories, JS/maps, app/API, documents.
3. Deduplicated table: `ID | URL/path | source | current status | sensitivity | impact | confidence | disposition`
4. Confirmed report-ready findings.
5. False positives and rejection reasons.
6. Historical-only/unverified leads separated from confirmed issues.
7. Coverage gaps and unavailable sources.
8. Safe next steps.

If no confirmed vulnerability is found, say so plainly. Never manufacture findings or inflate severity.
