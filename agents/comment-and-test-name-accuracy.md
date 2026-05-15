---
tags: [code-quality, testing, review, documentation]
---

# Comment and test name accuracy

Before committing, re-read every inline comment and test description for factual
accuracy and grammatical correctness. Stale or wrong text in the codebase erodes
trust faster than missing tests.

Concretely:

- Verify each comment matches what the code actually does — not a copy-paste
  left over from a previous version
- Verify each test name describes what the test exercises (a name like "encode
  wins when inner data has a version" must actually test that case, not pass
  with input that has no version)
- Verify grammar and spelling in JSDoc, comments, and test names

Static analysis usually misses this. Manual re-read is the only reliable check.
