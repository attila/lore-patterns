---
tags: [nono, sandbox, network, audit, egress, bulk, rate, pacing]
applies_when:
  tools: [Bash]
---

# nono: audit buffer overflows under bursty egress

nono's network audit buffer holds 4,096 events. Bursty egress from inside the
sandbox overflows it and logs "Network audit buffer full (4096 events); dropping
event". The requests themselves still go through; what is lost is the audit
record, and the warning is a sign of over-rate.

**How to apply:** run bulk live jobs inside nono sequentially and paced, about
200 ms between calls, and slow down if the warning returns. A pace setting on
the script makes this easy to tune.
