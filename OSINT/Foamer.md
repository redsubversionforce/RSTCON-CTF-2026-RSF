# Foamer — RSTCON CTF 2026

> **Category:** OSINT / Geolocation  
> **Solver:** Fuad Hasan (`t0r3n079`)  
> **Team:** Red Subversion Force (0xRSF)  
> **Investigation note:** 24 October 2026

## Objective

Identify where the photograph `cool_bridge.jpg` was taken, within a **5 km radius**.

## Investigation

### 1. Examine the image

I inspected the original photograph. The viewpoint appeared to be from **inside a passenger train**. Railway buildings were visible in the foreground, with a large **steel cantilever bridge** beyond them.

The rail-side vantage point and the bridge's structural silhouette were the key visual clues.

### 2. Check image metadata

I examined the image metadata for location clues. The photograph did **not** contain usable EXIF GPS coordinates, so I continued with visual geolocation.

### 3. Compare bridge candidates

I compared the bridge's shape with prominent bridges close to passenger rail corridors, focusing on the combination of the steel superstructure and the nearby railway infrastructure.

### 4. Narrow down the likely location

The **Commodore Barry Bridge**, near **Chester, Pennsylvania, USA**, emerged as a possible match.

I then considered the nearby **Northeast Corridor railway**, particularly the area around **Lamokin Street**, as an estimated camera location.

## Proposed location

- **Bridge candidate:** Commodore Barry Bridge, Chester, Pennsylvania, USA
- **Estimated viewpoint:** Northeast Corridor railway near Lamokin Street
- **Required precision:** Within 5 km of the original camera position

> **Verification note:** This is the location hypothesis documented in my investigation. The provided notes do not include a confirmed challenge submission, exact GPS coordinates, or the server's acceptance response. The estimate should therefore not be presented as a verified final solution without that evidence.

## What this challenge demonstrates

When GPS metadata is absent, useful geolocation clues can still come from a photograph's viewpoint, bridge architecture, and nearby transport infrastructure. Cross-referencing these observations can narrow down a plausible capture area.

## Evidence to add

If available, include the original `cool_bridge.jpg`, an annotated comparison of bridge structures, and the accepted coordinates or challenge result.

---

*Written by Fuad Hasan (`t0r3n079`) · Red Subversion Force (0xRSF).*
