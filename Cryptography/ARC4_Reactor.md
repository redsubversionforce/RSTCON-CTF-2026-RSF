# ARC4 Reactor — RSTCON CTF 2026

**Category:** Cryptography  
**Solved by:** `0xAyat` (Rubaya Hasan)  
**Team:** Red Subversion Force (0xRSF)

## Challenge overview

The provided `reactor.zip` contained three files: a plaintext PNG, an encrypted copy of that same PNG, and an encrypted ZIP archive. The goal was to recover the hidden message in the encrypted archive.

## 1. Examine the supplied files

After downloading and extracting the challenge archive, the directory contained:

```text
arc_reactor.png
arc_reactor.png.enc
documents.zip.enc
```

The plaintext and encrypted PNG were approximately the same size (2.3 MB); the encrypted document archive was approximately 1.3 MB.

## 2. Recover the keystream using known plaintext

For XOR-based stream encryption, if a plaintext `P` and its ciphertext `C` are both known, the matching keystream bytes are recovered as:

```text
K = P XOR C
```

The recovered keystream was applied to the encrypted ZIP archive:

```python
from pathlib import Path

plain_png = Path('arc_reactor.png').read_bytes()
encrypted_png = Path('arc_reactor.png.enc').read_bytes()
encrypted_zip = Path('documents.zip.enc').read_bytes()

keystream = bytes(a ^ b for a, b in zip(plain_png, encrypted_png))
if len(encrypted_zip) > len(keystream):
    raise ValueError('Not enough recovered keystream bytes')

recovered_zip = bytes(c ^ keystream[i] for i, c in enumerate(encrypted_zip))
Path('documents.zip').write_bytes(recovered_zip)
```

This works for the challenge's reused keystream portion. It is not a general method for decrypting arbitrary RC4 ciphertexts without reused keystream material.

## 3. Extract and inspect the recovered document

```bash
unzip -o documents.zip
pdftotext 'split funny atoms haha.pdf' -
```

The extracted PDF was titled **Nuclear Power and the Energy Grid**. The flag was embedded in the extracted text.

## Flag

```text
rstcon{n3utronz_Go_BRRRR}
```

## Takeaway

Reusing a stream-cipher keystream across different files can disclose data. Known plaintext and its matching ciphertext expose the keystream for those positions, potentially allowing decryption of another ciphertext encrypted with the same keystream.

> Reconstructed from the solver's supplied terminal notes; the original challenge files are not bundled in this write-up.
