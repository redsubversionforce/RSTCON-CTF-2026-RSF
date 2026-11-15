# Hey Listen — RSTCON CTF 2026

> **Category:** OSINT  
> **Solver:** Fuad Hasan (`t0r3n079`)  
> **Team:** Red Subversion Force (0xRSF)  
> **Investigation note:** 24 October 2026

## Objective

Find the hidden flag in a social-media post announcing RSTCON CTF.

## Investigation

### 1. Find the announcement

I searched for RSTCON's CTF announcement and found a two-post thread from **`@rst_con`**. A copy appeared on **TwStalker** (`community.twstalker.com`).

### 2. Identify the clue

The announcement contained unusual capitalization, opening and closing braces, and wording that encouraged readers to examine the message carefully.

Search snippets flattened the original line breaks, so I inspected the post's structure instead of relying only on the search preview.

### 3. Read the line initials

The lines began with these words:

| Line | First word | Initial |
|---|---|---|
| 1 | RSTCON | R |
| 2 | Available | A |
| 3 | Do | D |
| 4 | If | I |
| 5 | UTC | U |
| 6 | Maybe | M |

Reading the first letters vertically gives:

```text
R
A
D
I
U
M
```

**Recovered clue:** `RADIUM`

## Interpretation

The layout suggests an **acrostic**: an intentional message encoded in the initial letters of successive lines. The brace characters and unusual text styling may provide further context, but the notes supplied here do not establish how `RADIUM` combines with those elements.

> **Status: Partial documented solution.** The source notes confirm the extracted clue `RADIUM`, but they do not include a complete flag or an accepted submission. Do not fabricate a flag from this clue.

## Lessons learned

Social-media OSINT challenges may encode clues in formatting rather than ordinary sentences. Preserve line breaks, compare archived copies against search snippets, and examine initial letters before assuming the text is simply prose.

## Evidence to add

For a complete write-up, add screenshots or a link to the original announcement thread and the verified final flag if available.

---

*Written by Fuad Hasan (`t0r3n079`) · Red Subversion Force (0xRSF).*
