# Function 0x41 — RSTCON CTF 2026

**Category:** Network / Protocol Analysis  
**Challenge:** Function 0x41  
**Solved by:** `0|)|-|0|2@`  
**Event:** RSTCON CTF 2026

## Objective

Understand the challenge server's custom function and construct a correctly formatted request to obtain the flag.

## 1. Analyze the server source

I started by reviewing the supplied `server.py` source code. It defined the custom function code:

```python
CUSTOM_FC = 0x41
```

The server also provided a transformation function:

```python
def transform(seed: int) -> int:
    return (((seed ^ 0x1F3D) * 0x0101) + 0x2027) & 0xFFFF
```

This revealed the relationship between the two values expected by the server:

- **`seed`** — an input integer.
- **`guess`** — the value produced by `transform(seed)`.

The transformation first XORs `seed` with `0x1F3D`, multiplies the result by `0x0101`, adds `0x2027`, and finally masks the result to 16 bits using `& 0xFFFF`.

The server's validation logic compared the submitted guess with the computed value. A correct guess was necessary to receive the flag.

<!-- Add your original screenshot here after uploading it to images/ -->
<!-- ![Custom function and transformation in server.py](images/function-0x41.1.png) -->

## 2. Construct the request

To communicate with the server, I used Python's `socket` and `struct` modules:

```python
import socket
import struct
```

- `socket` provides the TCP connection to the challenge server.
- `struct` serializes integers into the byte representation required by the protocol.

The key payload construction was:

```python
pdu = struct.pack(">BHH", 0x41, seed, guess)
```

The format string `>BHH` means:

| Format | Meaning | Size |
|---|---|---|
| `>` | Big-endian byte order | — |
| `B` | Function code (`0x41`) | 1 byte |
| `H` | `seed` as an unsigned integer | 2 bytes |
| `H` | `guess` as an unsigned integer | 2 bytes |

Therefore, the **Protocol Data Unit (PDU)** is five bytes long. The PDU was combined with a Modbus/TCP header to form the complete request, which was then transmitted over TCP.

The source excerpt provided here does not specify the exact transaction identifier, unit identifier, or seed used in the successful run, so those values are not presented as recovered facts.

<!-- ![Python request construction](images/function-0x41.2.png) -->
<!-- ![Modbus TCP header and PDU](images/function-0x41.3.png) -->

## 3. Send the request

After preparing the request in `solve.py`, I ran:

```bash
python3 solve.py
```

The script connected to the challenge service on **TCP port 1502**, sent the Modbus/TCP request containing the custom function code, seed, and computed guess, and received the server's response.

<!-- ![Successful response from the challenge server](images/function-0x41.4.png) -->

## 4. Result

The challenge server returned:

```text
rstcon{user_defined_fc_reversed}
```

## 5. What this challenge demonstrates

The challenge required reading a custom protocol implementation, understanding a 16-bit transformation, and serializing fields in the correct byte order. The important step was not guessing a secret value blindly: the supplied source code showed exactly how `guess` should be computed from `seed`.

**Key takeaways:** source-code analysis, custom Modbus function codes, big-endian binary serialization, and constructing valid TCP protocol requests.

---

> **Scope:** This write-up documents a CTF challenge in an authorized competition environment.

## Screenshots

To include the original screenshots on GitHub, create an `images/` directory beside this Markdown file, add the four images with the filenames below, and uncomment the image lines in the sections above:

```text
images/
├── function-0x41.1.png
├── function-0x41.2.png
├── function-0x41.3.png
└── function-0x41.4.png
```
