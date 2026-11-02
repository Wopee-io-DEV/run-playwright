# run-playwright

## Kerberos (Windows SSO)

For an app whose identity provider answers only `401 WWW-Authenticate:
Negotiate`, set `KERBEROS_PRINCIPAL` (`user@REALM`) and `KERBEROS_PASSWORD` as
Commander project variables (optional: `KERBEROS_AUTH_SERVERS`,
`KERBEROS_REALM`, `KERBEROS_KDC`). The step "Kerberos sign-in" runs the runtime
image's `wopee-kerberos login` before the tests: one `kinit`, then
`KRB5_CONFIG`, `KRB5CCNAME` and `--auth-server-allowlist` (appended to
`WOPEE_BROWSER_ARGS`) for the test step. The project's `playwright.config.ts`
must pass `WOPEE_BROWSER_ARGS` to Chromium's launch args (playwright-template
does). Works with Playwright's default headless Chromium; a headed full
Chromium starts on a fresh profile and fails its first Negotiate on Linux. The
runner host needs TCP 88 to the realm's KDCs.

This job runs the whole Playwright project in one browser, so it signs in once
per job: `KERBEROS_SSO=off` switches it off, and of a `KERBEROS_ACCOUNTS` map
(per-test accounts, see run-agent) only the `*` entry applies here.

Values of variables whose names look secret (`PASS`, `PWD`, `SECRET`, `TOKEN`,
`CREDENTIAL`, `PRIVATE`, `KEYTAB`, `…_KEY`) are masked before they enter the
job environment.

