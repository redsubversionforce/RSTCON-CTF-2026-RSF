# Bad Tide — RSTCON CTF 2026

> **Author:** Fuad Hasan (`t0r3n079`)  
> **Team:** [Red Subversion Force (0xRSF)](https://github.com/redsubversionforce)  
> **Event:** RSTCON CTF 2026  
> **Category:** Web Exploitation  
> **Flag:** Intentionally omitted

**Scope:** This article documents an authorized CTF challenge. Commands and payloads are for the competition lab only.

---

## Bad Tide — Server-Side Template Injection

**Vulnerability:** Server-side template injection (SSTI)  
**Entry point:** **Personalize this report** input

### Overview

The report-personalization feature appeared to interpret user-supplied text as part of a server-side template. By evaluating a simple expression first, I could distinguish template execution from ordinary text display.

### Step 1 — Check whether input is evaluated

I entered the following expression into the **Personalize this report** field:

```jinja2
{{7*7}}
```

The page rendered **`49`** instead of the literal expression. That result indicated that template evaluation was occurring, consistent with a Jinja2-style SSTI vulnerability.

### Step 2 — Inspect the application directory

In the authorized challenge mirror, the next payload used an accessible template object to reach Python globals and inspect `/app`:

```jinja2
{{cycler.__init__.__globals__.os.popen('ls -la /app').read()}}
```

This demonstrated the security impact beyond arithmetic evaluation: if the template environment exposes such objects, untrusted template expressions can reach operating-system functionality.

### Step 3 — Read the challenge flag file

The intended challenge path exposed `/app/flag.txt`. I used the same template expression mechanism to read it:

```jinja2
{{cycler.__init__.__globals__.os.popen('cat /app/flag.txt').read()}}
```

The flag value is omitted from this public write-up.

### Root cause and lesson

User-controlled text was interpreted as a template expression rather than treated as plain data. In an insufficiently restricted environment, SSTI can escalate from simple expression evaluation to server-side file access or command execution.

**Mitigation:** Never compile or render untrusted input as a template; pass user content as data into trusted templates instead. Apply appropriate sandboxing and least-privilege execution as defense in depth.

---

---

**Written by:** Fuad Hasan (`t0r3n079`) · **Team:** [0xRSF](https://github.com/redsubversionforce)
