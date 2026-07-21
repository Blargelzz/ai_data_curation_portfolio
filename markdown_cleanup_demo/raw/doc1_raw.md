#Incident Response Notes for Q2
This doc contains notes from incident response exercises and simulated logs.  It was written fast and contains many formatting issues, broken links, and mixed content types.  Use this file to practice cleaning, normalizing, and redacting sensitive items.

##Summary
- incident: suspected data exfiltration
- affected systems: webserver, db-server, auth
- timeline
  - 2024-06-01 10:23:45 - alert triggered
  - 2024-06-01 10:24 - analyst triaged
- status: open

###Observations
1. Alerts were noisy and many false positives
2. Some logs were truncated
3. unclear ownership for some assets
4. missing correlation rules

####Raw Logs (partial)
[LOGS START]
2024-06-01T10:23:45Z GET /api/v1/data?id=12345 200 192.0.2.1 "Mozilla/5.0"
2024-06-01T10:23:46Z POST /login 302 198.51.100.23 "curl/7.68.0"
2024-06-01T10:24:01Z ERROR db-server: connection timeout user=admin password=admin123
2024-06-01T10:24:05Z WARNING auth: token expired for user: jdoe@example.com
[LOGS END]

> Note: some entries above may contain PII or credentials and should be redacted before sharing externally

###Investigation Steps
- pulled logs from /var/logs/* and from cloud provider
- ran quick grep for suspicious patterns
- attempted to reproduce the request sequence in staging
- escalated to infra team but no response for 2 hours

###Findings
- evidence of repeated POSTs from same IP
- suspicious user agent strings
- database connection attempts with default creds
- some config files exposed in repo (see below)

####Config Snippet
```yaml
db:
  host: db-server.internal
  port: 5432
  user: admin
  password: admin123
  replicas:
    - db-replica-1
    - db-replica-2

####Repo Links

repo: github.com/company/internal-configs

file: /configs/prod/db.yml

note: link above is broken or private

###Recommendations

rotate credentials immediately

add rate limiting

implement correlation rules for repeated POSTs

redact PII in logs

add owner tags to assets

###Appendix A - Evidence

screenshot_001.png

packet_capture.pcap

export.csv

###Appendix B - Timeline (expanded)
2024-06-01 10:23:45 - IDS alert: possible exfiltration
2024-06-01 10:23:46 - webserver returned 200 for /api/v1/data?id=12345
2024-06-01 10:24:01 - DB connection timeout (see logs)
2024-06-01 10:24:05 - auth token expired for jdoe@example.com

###Formatting problems

headings inconsistent

lists mixed (bullets + numbers)

code fences not always closed

inline HTML used below

<h4>Quick note</h4>
Please check the "Config Snippet" above and the repo link.  Also see the TODOs below.

TODO:

[ ] confirm whether the POSTs are automated

[ ] check cloud storage buckets for public access

[ ] contact legal

###Broken links and markdown errors
Internal runbook
[Packet capture](file://pcap/packet_capture.pcap
[Missing bracket](https://example.com

**Unclosed bold
*Unclosed italic

######tiny heading with too many hashes
Text immediately after heading with no blank line
More text  with   extra   spaces.

End of document - no formal conclusion
