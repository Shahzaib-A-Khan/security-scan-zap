# Findings: ZAP baseline scan of OWASP Juice Shop

Target: OWASP Juice Shop, an intentionally vulnerable practice app, running in a local Docker container.
Scan type: ZAP baseline (passive).
Result: the scan summary reported 0 failures, 7 warnings and 61 rules passed. The report lists 9 alerts: 2 Medium, 4 Low and 3 Informational.

| # | Finding | Risk | URLs | Meaning | Recommended fix |

| 1 | Content Security Policy (CSP) Header Not Set | Medium | 5 | No policy restricts which sources may load scripts, which makes cross-site scripting easier | Send a Content-Security-Policy header that allows only trusted sources |
| 2 | Cross-Domain Misconfiguration | Medium | 5 | CORS settings are too permissive, so other sites may read responses | Allow only specific trusted origins, never a wildcard |
| 3 | Cross-Origin-Embedder-Policy Header Missing or Invalid | Low | 5 | A browser isolation header is missing | Add the header if cross-origin isolation is needed |
| 4 | Cross-Origin-Opener-Policy Header Missing or Invalid | Low | 5 | Another browser isolation header is missing | Add the header, commonly set to same-origin |
| 5 | Dangerous JS Functions | Low | 2 | JavaScript contains functions such as eval that can enable code injection | Review where they are used and avoid them with untrusted input |
| 6 | Deprecated Feature Policy Header Set | Low | 5 | The deprecated Feature-Policy header is set | Replace it with Permissions-Policy |
| 7 | Modern Web Application | Informational | 5 | A JavaScript app the basic crawler cannot fully explore. Not a vulnerability | Add the AJAX spider to see more of the app |
| 8 | Storable and Cacheable Content | Informational | 1 | Responses may be stored by shared caches | If a response is sensitive, send Cache-Control: no-store |
| 9 | Storable but Non-Cacheable Content | Informational | 5 | Responses can be stored but are marked non-cacheable | Review the cache headers |

## Notes
- The scan output listed the Cross-Origin-Embedder-Policy alert on 10 URLs while the report lists 5. I did not investigate the difference.
- All Medium and Low findings here are missing or loose HTTP headers, which are configuration issues and not code flaws.

## Limits of this scan
A passive baseline scan only sees what the crawler loads. A clean or mild baseline does not mean the app is secure. Juice Shop contains many flaws this scan cannot find, and finding them needs active scanning or manual testing, which I did not run.