# Brackish Key — RSTCON CTF 2026

**Category:** Cryptography  
**Solved by:** `0xAyat` (Rubaya Hasan)  
**Team:** Red Subversion Force (0xRSF)

## Challenge overview

The supplied `cipher.txt` contained a long hexadecimal string. The solution was to identify the encoding and recover a repeating XOR key using a likely plaintext prefix.

## 1. Inspect the ciphertext

The file was ASCII text containing hex-encoded bytes. An initial attempt to decode the bytes directly as text produced unreadable control characters, suggesting another layer of transformation.

```python
from pathlib import Path

hex_cipher = Path('cipher.txt').read_text().strip()
ciphertext = bytes.fromhex(hex_cipher)
print(ciphertext[:12].hex(' '))
```

The initial bytes documented in the notes were:

```text
31 17 52 1b 17 0b 06 48 3d 0d 10 1b
```

## 2. Estimate the repeating-key length

The solver used `xortool` to look for probable key lengths:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install xortool
# The file used below must contain raw bytes, not printable hexadecimal characters.
xxd -r -p cipher.txt > cipher.bin
xortool cipher.bin
```

The notes' exploratory `xortool -x note.hex` run suggested a likely key length of **5** (or multiples of 5). That run used printable hex text, so it should be treated as an exploratory clue rather than a definitive raw-ciphertext analysis.

## 3. Recover the key from a plaintext guess

The solver tested the prefix `Secur` against the first five ciphertext bytes:

```python
cipher_prefix = bytes.fromhex('3117521b17')
known_plaintext = b'Secur'
key = bytes(c ^ p for c, p in zip(cipher_prefix, known_plaintext))
print(key)
```

Output:

```text
b'br1ne'
```

The five-byte repeating XOR key was therefore `br1ne`.

## 4. Decrypt the full ciphertext

```python
from pathlib import Path

cipher = bytes.fromhex(Path('cipher.txt').read_text().strip())
key = b'br1ne'
plaintext = bytes(c ^ key[i % len(key)] for i, c in enumerate(cipher))
print(plaintext.decode('utf-8'))
```

The recovered message in the supplied notes was:

```text
SecurityShrimp field note: rotated the engagement creds tonight.
maintenance token is rstcon{cr1b_dragging_the_brackish_key} do not leave it sitting on the wire next time.
```

## Flag

```text
rstcon{cr1b_dragging_the_brackish_key}
```

## Takeaway

A repeating-key XOR cipher is vulnerable when predictable plaintext provides enough bytes to recover the repeating key. Encoding ciphertext as hexadecimal does not add cryptographic security.

> The original terminal session included several failed decoding attempts. These have been summarized while preserving the successful approach.
