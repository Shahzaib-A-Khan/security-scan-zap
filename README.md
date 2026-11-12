# Security Scan with OWASP ZAP

ZAP baseline scan of OWASP Juice Shop, a deliberately insecure practice app, run in Docker Compose locally and in GitHub Actions.

## What it does
- Starts Juice Shop in a container on 127.0.0.1:3000
- Runs ZAP's baseline (passive) scan from the official ZAP Docker image against it
- Writes an HTML report and a JSON report to `reports/`
- Runs the same scan in GitHub Actions on pushes, on pull requests and weekly, and uploads the report as an artifact

## Findings
See FINDINGS.md. The first scan reported 0 failures, 7 warnings and 61 passes. The report lists 9 alerts (2 Medium, 4 Low, 3 Informational), mostly missing or loose HTTP security headers.

## Security gate
`zap/rules.conf` marks two rules as FAIL: Content Security Policy (CSP) Header Not Set and Cross-Domain Misconfiguration. Locally, the scan reported FAIL-NEW: 2 and exited with code 1. In CI, the job passes only when ZAP exits with exactly 1 and both report files exist, which proves the gate fires on the deliberately vulnerable Juice Shop. On a real application the check would be inverted: the job would require exit code 0.

## Ground rules
- Only the Juice Shop container on my own computer or on the CI runner is scanned, never anyone else's site.
- Passive baseline scan only, with no active attack scan.
- Juice Shop is bound to 127.0.0.1 so other machines on the network cannot reach it.

## Run it locally
docker compose up -d juice-shop
docker compose run --rm zap
docker compose down

Then open reports/report.html.

## Notes
- Counts vary slightly between runs because the crawler does not visit exactly the same URLs each time.
- A passive scan only sees what the crawler loads, so it cannot find most of Juice Shop's flaws.

## Project structure
- docker-compose.yml: the Juice Shop and ZAP services
- reports/: scan output
- FINDINGS.md: triage of the alerts
- .github/workflows/zap-baseline.yml: CI workflow