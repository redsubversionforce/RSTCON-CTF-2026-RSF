# Intercepted — RSTCON CTF 2026

**Category:** Cryptography  
**Solved by:** `0xAyat` (Rubaya Hasan)  
**Team:** Red Subversion Force (0xRSF)

## Challenge overview

The provided `transmission.txt` held hexadecimal text. Multiple encoding layers concealed the flag, which could be recovered by decoding them in sequence.

## 1. Inspect the transmission

The solver downloaded `transmission.txt` and checked its content using `file` and `strings`. It contained a single hexadecimal-looking string beginning with:

```text
5054303950543039...
```

## 2. Decode hexadecimal to text

```python
from pathlib import Path

hex_text = Path('transmission.txt').read_text().strip()
first_layer = bytes.fromhex(hex_text).decode('ascii')
print(first_layer)
```

The supplied notes show a Base64-looking result:

```text
PT09PT09VVBPRE1TR1hCRzcyWjRXVVJNUDNZNFdaNExGRDJJWFBSTlRaTUFYNVpOUDNZSVhaSk8=
```

## 3. Decode Base64

```python
import base64

second_layer = base64.b64decode(first_layer).decode('ascii')
print(second_layer)
```

Output recorded in the notes:

```text
======UPODMSGXBG72Z4WURMP3Y4WZ4LFD2IXPRNTZMAX5ZNP3YIXZJO
```

## 4. Reverse the text and decode Base32

The Base32 padding appeared at the beginning rather than the end, suggesting that the text was reversed. Reversing it restored the expected orientation:

```python
final_plaintext = base64.b32decode(second_layer[::-1]).decode('utf-8')
print(final_plaintext)
```

The result was the flag.

## Flag

```text
rstcon{p33l_the_3ncoding_0ni0n}
```

## Takeaway

Repeated encodings can make content appear cryptic without providing encryption. Recognizing hexadecimal, Base64, reversed text and Base32 in the correct order reveals the underlying data.
