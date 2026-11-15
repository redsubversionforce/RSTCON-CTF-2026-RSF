# React2Shell — RSTCON CTF 2026

> **Author:** Fuad Hasan (`t0r3n079`)  
> **Team:** [Red Subversion Force (0xRSF)](https://github.com/redsubversionforce)  
> **Event:** RSTCON CTF 2026  
> **Category:** Web Exploitation  
> **Flag:** Intentionally omitted

**Scope:** This article documents an authorized CTF challenge. Commands and payloads are for the competition lab only.

---

## React2Shell — React Server Components Security Challenge

**Focus:** React Server Components (RSC), server actions, and a challenge associated with **CVE-2025-55182**  
**Observed stack:** Next.js 15 / React 19, as described by the challenge page

### Overview

This challenge presented a web application whose footer and frontend bundles provided clues about its framework and build era. The investigation connected those clues to the React Server Components security advisory published on **December 3, 2025**.

Unlike the two challenges above, my available notes do **not** contain a tested exploit request or a captured response from the final flag-read step. The following therefore documents the investigation and intended validation path, without inventing a complete exploit transcript.

### Step 1 — Identify the technology stack

I inspected the challenge mirror and noted the framework/build information exposed in the page footer. The application described itself as using **Next.js 15** and **React 19**.

### Step 2 — Inspect client-side JavaScript

I used browser Developer Tools (**Network** and **Sources**) to examine the scripts loaded by the page and look for React Server Components and server-action-related indicators.

A simple command can help list JavaScript script URLs from an authorized mirror:

```bash
BASE='https://<authorized-mirror>'

curl -k -sS "$BASE/" | \
  grep -oE 'src="[^"]+\.js[^"]*"'
```

This is a discovery step, not proof of server-side vulnerability.

### Step 3 — Correlate the clues with the advisory

The challenge's stated build date preceded the **December 3, 2025** React advisory associated with **CVE-2025-55182**. That context suggested an investigation of the server-side RSC deserialization path.

**Reference:** [React: Critical Security Vulnerability in React Server Components (December 3, 2025)](https://react.dev/blog/2025/12/03/critical-security-vulnerability-in-react-server-components)

A version banner or old build date alone cannot establish that the deployed server is vulnerable. Actual affected package versions, patch state, and behavior must be verified.

### Step 4 — Validate within the CTF environment

The next step was to use the challenge's intended validation method or a non-destructive proof of concept against the **authorized challenge mirror only**.

My notes identify `/flag.txt` as the intended challenge target, but they do not record the precise validation payload or the resulting server response. I am leaving those details unfilled rather than claiming a specific execution path that is not documented.

### Root cause and lesson

Server-side component protocols and deserialization boundaries deserve the same security scrutiny as conventional API endpoints. Static client-bundle inspection is useful for reconnaissance, but **server behavior and patch state** determine whether an issue is actually exploitable.

**Mitigation:** Follow React and Next.js security advisories, upgrade affected dependencies to patched releases, and validate deployed versions rather than trusting an application banner.

---

---

**Written by:** Fuad Hasan (`t0r3n079`) · **Team:** [0xRSF](https://github.com/redsubversionforce)
