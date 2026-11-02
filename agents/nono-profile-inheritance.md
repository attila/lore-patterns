---
tags: [nono, sandbox, profile, extends, inheritance, merge, environment, allow_vars, open_urls, credentials]
---

# nono: how profile inheritance merges

Verified on nono 0.81.0. A profile's `extends` lists parent profiles by name and
merges all of them in order. A file path works for `--profile` and
`nono profile show`, but `extends` rejects one as an invalid base profile name.

- **Lists append and drop duplicates.** `filesystem` grants from every parent
  and the child add up, and an entry listed twice appears once.
- **`open_urls` replaces, and the last parent listed wins.** To keep shared
  open-URL defaults in one base profile, list that base last in `extends` and
  leave `open_urls` out of the others.
- **An `environment.allow_vars` anywhere in the chain governs the whole chain.**
  A child that sets none still strips every variable the allowlist omits, so a
  curated allowlist that leaves out secrets strips them from every profile built
  on it.
- **Credential injection passes the allowlist.** `network.custom_credentials`
  with an `env_var` puts that variable, a phantom token, into the sandbox along
  with a base URL named after the credential entry (`anthropic_api` gives
  `ANTHROPIC_API_BASE_URL`), though no allowlist names either. Secrets can reach
  the sandbox through the proxy while host passthrough stays closed.
- **nono sets its own variables**, so `allow_vars` need not list them: the proxy
  ones (`HTTP_PROXY`, `HTTPS_PROXY`, `NO_PROXY`, `NODE_USE_ENV_PROXY`), the CA
  bundle ones (`SSL_CERT_FILE`, `NODE_EXTRA_CA_CERTS`, `CURL_CA_BUNDLE`,
  `REQUESTS_CA_BUNDLE`, `AWS_CA_BUNDLE`), `BROWSER` and `NONO_*`.

**How to check:** `nono profile show --json <profile>` resolves the merge but
leaves the `environment` section out. See what actually reaches the sandbox with
a real run, from a plain shell: inside a nono session, `nono run` fails unless
its state directory `~/.local/state/nono` is granted.

```bash
nono run -s --allow-cwd --profile <profile> -- printenv | cut -d= -f1
```
