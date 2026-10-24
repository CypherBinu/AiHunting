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
site:example[.]com ext:log | ext:txt | ext:conf | ext:cnf | ext:ini | ext:env | ext:sh | ext:bak | ext:backup | ext:swp | ext:old | ext:~ | ext:git | ext:svn | ext:htpasswd | ext:htaccess | ext:json
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
site:example.com ext:pdf
site:*.example.com ext:pdf

site:example.com ext:csv
site:*.example.com ext:csv

site:example.com (ext:pdf OR ext:csv) "invoice"
site:*.example.com (ext:pdf OR ext:csv) "invoice"

site:example.com intitle:"index of"
site:*.example.com intitle:"index of"
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
site:target.com filetype:pdf "confidential"
site:target.com filetype:doc "restricted"
filetype:sql site:target.com
ext:dbf OR ext:mdb
filetype:log "error"
filetype:log "password"
ext:log "Full stack trace"
site:target.com ext:bak OR ext:old OR ext:backup
intitle:"index of" site:target.com
intitle:"index of" "backup"
intitle:"index of" "parent directory"
intitle:"index of" "backups"
intitle:"index of" ".git"
intitle:"index of" ".env"
intitle:"index of" "database" OR "dump" OR "sql"
intitle:"index of" "logs"
intitle:"index of" "uploads"
intitle:"index of" "files"
intitle:"index of" "passwd" OR "password"
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
Sometime wayback cdx show 500 server issue if its show try untail you fetch the file.
For candidate extension filtering, retrieve a limited URL inventory and filter locally for `.env`, `.log`, `.conf`, `.ini`, `.bak`, `.backup`, `.old`, `.sql`, `.zip`, `.json`, `.yml`, `.yaml`, `.map`,`.pdf`, `.git`, and `.svn`. CDX query/filter syntax and wildcard behavior may vary; consult current Internet Archive documentation.

Process:
1. Start with narrow queries and sensible limits/pagination.
2. Extract URL, timestamp, status, MIME type, and digest before fetching archived content.
3. Deduplicate, normalize, and remove out-of-scope/third-party hosts.
4. Flag paths containing `backup`, `old`, `debug`, `config`, `logs`, `dump`, `export`, `internal`, `staging`, `test`, `error`, `trace`, `upload`, `private`, `archive`, `database`, `swagger`, `openapi`, `graphql`, or `sourceMappingURL`.
5. Mark results `Historical-only` until current exposure is separately verified under policy.
6. An archived 200 is not proof of current exposure or sensitivity. Never republish archived secrets.

## 4. GitHub and public repository discovery
###  General secret indicators

``` text
org:TARGET_ORG "api_key"
org:TARGET_ORG "api_secret"
org:TARGET_ORG "api_token"
org:TARGET_ORG "client_secret"
org:TARGET_ORG "access_token"
org:TARGET_ORG "refresh_token"
org:TARGET_ORG "private_key"
org:TARGET_ORG "secret_key"
org:TARGET_ORG "secret_access_key"
org:TARGET_ORG "credentials"
org:TARGET_ORG "x-api-key"
org:TARGET_ORG "Authorization: Bearer"
```

###  Cloud and provider-specific indicators

``` text
org:TARGET_ORG "AWS_ACCESS_KEY_ID"
org:TARGET_ORG "AWS_SECRET_ACCESS_KEY"
org:TARGET_ORG "aws_access_key"
org:TARGET_ORG "aws_secret_key"
org:TARGET_ORG "S3_ACCESS_KEY_ID"
org:TARGET_ORG "S3_SECRET_ACCESS_KEY"
org:TARGET_ORG "S3_BUCKET"
org:TARGET_ORG "GEMINI_API_KEY"
org:TARGET_ORG "GOOGLE_API_KEY"
org:TARGET_ORG "AIza"
org:TARGET_ORG "HEROKU_API_KEY"
org:TARGET_ORG "shodan_api_key"
org:TARGET_ORG "codecov_token"
org:TARGET_ORG "slack_token"
org:TARGET_ORG "xoxb-"
org:TARGET_ORG "xoxp-"
org:TARGET_ORG "HOMEBREW_GITHUB_API_TOKEN"
org:TARGET_ORG "ghp_"
org:TARGET_ORG "github_pat_"
```

###  Database and application configuration

``` text
org:TARGET_ORG "DATABASE_URL"
org:TARGET_ORG "database_password"
org:TARGET_ORG "DB_PASSWORD"
org:TARGET_ORG "DB_USERNAME"
org:TARGET_ORG "WORDPRESS_DB_PASSWORD"
org:TARGET_ORG "connectionstring"
org:TARGET_ORG "mongodb+srv://"
org:TARGET_ORG "postgresql://"
org:TARGET_ORG "redis_password"
org:TARGET_ORG "ROOT PASSWORD"
org:TARGET_ORG "ADMIN PASSWORD"
org:TARGET_ORG "SECRET_KEY_BASE"
org:TARGET_ORG filename:config.json
org:TARGET_ORG filename:settings.py
org:TARGET_ORG filename:application.properties
org:TARGET_ORG filename:docker-compose.yml
org:TARGET_ORG filename:terraform.tfvars
```

###  Potentially sensitive file names

Use these only within the authorized organization/repository. A filename
match alone does not establish exposure.

``` text
org:TARGET_ORG filename:.env
org:TARGET_ORG filename:.npmrc
org:TARGET_ORG filename:.git-credentials
org:TARGET_ORG filename:.netrc
org:TARGET_ORG filename:.pgpass
org:TARGET_ORG filename:.s3cfg
org:TARGET_ORG filename:sftp-config.json
org:TARGET_ORG filename:credentials
org:TARGET_ORG filename:secrets.yml
org:TARGET_ORG filename:deployment-config.json
org:TARGET_ORG filename:terraform.tfstate
org:TARGET_ORG filename:backup.sql
org:TARGET_ORG filename:debug.log
org:TARGET_ORG filename:wp-config.php
org:TARGET_ORG filename:prod.secret.exs
```

### Internal endpoints and environment references

``` text
org:TARGET_ORG "internal-api"
org:TARGET_ORG "staging"
org:TARGET_ORG "preprod"
org:TARGET_ORG "localhost:8080"
org:TARGET_ORG "swagger.json"
org:TARGET_ORG "openapi.json"
org:TARGET_ORG "DEBUG = True"
org:TARGET_ORG "corp" "https://"
org:TARGET_ORG "internal" "ssh://"
```

###  GitHub Actions and CI/CD review

These queries help identify workflow patterns for **configuration
review**, not automatic proof of exploitation.

``` text
org:TARGET_ORG path:.github/workflows "pull_request_target"
org:TARGET_ORG path:.github/workflows "workflow_run"
org:TARGET_ORG path:.github/workflows "workflow_call"
org:TARGET_ORG path:.github/workflows "secrets: inherit"
org:TARGET_ORG path:.github/workflows "id-token: write"
org:TARGET_ORG path:.github/workflows "self-hosted"
org:TARGET_ORG path:.github/workflows "ACTIONS_STEP_DEBUG"
org:TARGET_ORG path:.devcontainer "remoteEnv"
org:TARGET_ORG path:.devcontainer "containerEnv"
org:TARGET_ORG path:.devcontainer "postCreateCommand"
```



###  Target-domain references

``` text
org:TARGET_ORG "TARGET_DOMAIN"
org:TARGET_ORG "TARGET_DOMAIN" "api_key"
org:TARGET_ORG "TARGET_DOMAIN" "client_secret"
org:TARGET_ORG "TARGET_DOMAIN" "DATABASE_URL"
org:TARGET_ORG "TARGET_DOMAIN" "internal-api"
repo:TARGET_ORG/TARGET_REPO "TARGET_DOMAIN"
```
## How to run

1.  Open https://github.com/search and select **Code**.
2.  Paste one query from the sections below.
3.  Review the repository, path, commit, and surrounding context.
4.  Classify each result as `potential secret`,
    `configuration exposure`, `internal endpoint`, `sensitive data`, or
    `false positive`.

5.  Replace `TARGET_DOMAIN` with the exact in-scope domain. These are GitHub
Code Search queries, not Google `site:` dorks. 
6.  ignore the test and demo results.

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


## 6.1 Sensitive Directory & File Fuzzing

Use authorized, low-noise content discovery to identify potentially exposed sensitive directories and files on explicitly in-scope assets. This method extends the existing passive discovery workflow; it does not replace any existing method.

### Candidate path categories

Use small, relevant wordlists informed by existing recon results, historical URLs, observed technologies, and authorized application traffic.

**Configuration and environment files**
```text
.env
.env.bak
.env.old
config.json
config.yml
settings.py
application.properties
web.config
```

**Backups and database artifacts**
```text
backup.zip
backup.tar.gz
database.sql
database.sql.gz
db.bak
dump.sql
site-backup.zip
```

**Logs and diagnostics**
```text
error.log
debug.log
access.log
app.log
debug
trace
```

**Version control and source artifacts**
```text
.git/HEAD
.git/config
.svn/entries
package.json
composer.json
webpack.config.js
```

**API documentation and development paths**
```text
swagger.json
openapi.json
api-docs
graphql
debug
staging
test
dev
```

**Exports and build artifacts**
```text
export.csv
report.pdf
manifest.json
*.map
```

These are candidate paths, not confirmed vulnerabilities. Choose only categories relevant to the target and permitted by the program.

### Candidate sources and integration

Prioritize candidate paths found in:
- Wayback CDX historical URL inventories.
- Google Dorks and other permitted public search results.
- Existing `gau`, `waybackurls`, and other local URL inventories.
- In-scope JavaScript files and source-map references.
- Authorized application traffic and public API documentation.
- Technology-specific observations from passive recon.

Integrate this stage after passive discovery and JavaScript/source-map review, before manual validation and reporting. Deduplicate candidates and verify each hostname against the written scope before sending requests.

### Content discovery with ffuf

If `ffuf` or another content-discovery tool is already installed and the program permits automated requests:
1. Confirm the target hostname and testing permissions.
2. Select a small, category-specific wordlist.
3. Explain the purpose of the planned command before execution.
4. Use conservative concurrency, timeouts, and a fixed request budget.
5. Avoid unnecessary recursion, repeated requests, or indiscriminate large wordlists.
6. Stop if the activity exceeds program limits or exposes unexpected sensitive data.

Do not install tools, change system configuration, or launch active scans without the required explicit approval.

### Response analysis and false-positive controls

For each candidate, review:
- HTTP status code.
- Response length and body fingerprint.
- MIME type.
- Redirect destination.
- Wildcard responses and soft-404 behavior.
- Authentication requirements.
- Minimal evidence of genuinely sensitive content.

A `200` response, `403` response, unusual content length, filename, or directory name alone does not establish a vulnerability. Compare candidate responses with suitable baseline responses and review promising results manually using minimal, read-only requests.

### Safe verification

- Verify that the resource is current and in scope.
- Distinguish public or synthetic examples from sensitive information.
- Do not bypass authentication or access controls.
- Do not test discovered credentials or enumerate unrelated users.
- Do not download databases or sensitive files in bulk.
- Redact tokens, credentials, and personal information from evidence.
- Keep unverified candidates under `Needs manual review`; use `Historical-only` for archive-only evidence.

### Evidence ledger

Record the result in the existing evidence ledger rather than creating a separate tracking system:

`ID | Host | Path | Source | Scope | Status | Sensitivity | Impact | Confidence | Disposition | Evidence`

Group paths that share the same root cause. Mark a finding `Confirmed` only when current, in-scope evidence establishes actual security impact.

## 7. False-positive reduction
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
