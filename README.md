# Plane Frame firmware

Release files for Plane Frame, a mantelpiece screen that shows the planes passing overhead. Only finished firmware lives here; the source code is in a private repository.

## How a frame uses this

Every frame checks this address once a night, between 03:00 and 04:00:

```
https://github.com/drewsypoo/planeframe-firmware/releases/latest/download/manifest.json
```

If the manifest's version is newer than the one the frame is running, the frame downloads `firmware.bin`, checks its size and MD5, writes it to its spare slot and restarts into it. If the new firmware fails to start properly three times in a row, the frame switches back to the version it had before.

Frames download from here without logging in, which is why this repository is public.

## What a release contains

| File | What it is |
|---|---|
| `firmware.bin` | The board firmware for the Waveshare ESP32-S3-Touch-LCD-5B |
| `manifest.json` | What the frame reads to decide whether to update |

`manifest.json`:

```json
{
  "version": "2.8.0",
  "firmware": "https://github.com/drewsypoo/planeframe-firmware/releases/download/v2.8.0/firmware.bin",
  "md5": "32 hex characters",
  "sha256": "64 hex characters"
}
```

| Field | Meaning |
|---|---|
| `version` | The firmware version, `major.minor.patch`. A frame only installs a version higher than its own. |
| `firmware` | Where to download `firmware.bin` for this release. |
| `md5` | Checked by the frame before it installs. |
| `sha256` | Recorded for the stronger check coming in a later firmware. |

The frame also understands `aircraftUrl`, `callsignUrl` and `routeUrl`, which move every frame to a different data source without a firmware update. Leave them out unless a data source has moved.

## Rules

- **Nothing secret goes in a release.** Everything here is public. In particular, never put a `flightAwareKey` in a manifest or build it into the firmware. The FlightAware key is typed into each frame once, by the builder, on the Wi-Fi setup page, and lives in the frame's own memory from then on. The person who receives the frame never needs an account.
- **Releases are published by CI** from the source repository, using the *Publish board firmware* workflow. It refuses to publish if the FlightAware key is compiled into the firmware, or if the version already exists here.
- **Versions only go up.** To withdraw a bad release, publish a fixed one with a higher version. Deleting a release makes `latest` point at the previous one, but frames that already installed the bad version won't go back down to it.
