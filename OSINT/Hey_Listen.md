# Hey Listen — RSTCON CTF 2026

**Category:** OSINT  
**Solved by:** `0xAyat` (Rubaya Hasan) — attribution supplied by the team  
**Team:** Red Subversion Force (0xRSF)  
**Write-up status:** Partial notes; final solution not documented in the supplied source

## Available clues

The provided notes identify the challenge as **Hey Listen (OSINT)** and show the following words and extracted initials:

| Word | Initial |
|---|:---:|
| RSTCON | R |
| Available | A |
| Do | D |
| If | I |
| UTC | U |
| Maybe | M |

Reading the initials in order yields:

```text
RADIUM
```

## Analysis

The sequence suggests an **acrostic**, where the initial letters of selected words form a hidden clue. Here, the visible output is `RADIUM`.

The supplied notes do not describe the source page or the next investigative step, nor do they contain a verified final flag. Therefore this write-up does **not** claim that `RADIUM` alone is the flag or that the full challenge solution has been reproduced.

## Flag

**Not present in the submitted notes.**

## Takeaway

In OSINT challenges, deliberately selected text can encode a clue through first letters, ordering, or other simple transformations. Preserve the original context when documenting why those words were selected.

> Add the original screenshot, source URL and remaining solution steps before labeling this a complete public write-up.
