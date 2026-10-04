# MNO-Prayer-Clock-Firmware

Public **firmware distribution** for the ProjectPrayerClock wall clock. The clock updates itself over
the air from this repository.

The source code lives in a separate **private** repository. This one is public because the device has no
credentials: it can only fetch anonymous public HTTPS URLs, so the update channel has to be readable
without authentication.

## Layout

| Path | Purpose |
|---|---|
| `manifest.json` | what the clock polls. Its URL is stable and compiled into the firmware. |
| Releases | one per firmware version, each carrying `firmware.bin` as an immutable release asset. |

## Rules

1. **Do not edit `manifest.json` by hand.** It is generated and pushed by `scripts/make_release.py` in
   the firmware source repository, together with the matching GitHub Release. A manifest that disagrees
   with its asset is how devices get bricked.
2. **Never replace a release asset.** Each `firmware.bin` is addressed by its version tag and by a
   SHA-256 recorded in the manifest. The clock refuses to boot an image whose hash does not match, and
   the bootloader rolls back automatically if a new image fails to start.
3. **The binary is not committed to git** — it lives in Releases. A 2.5 MB blob per version would live
   in every clone and fetch forever.

## Status

Repository created 2026-10-04. **No firmware has been published yet** — the manifest and the first
release will appear when the first OTA-enabled build is ready.

## How the clock behaves

- It checks for updates every 6 hours.
- It installs an update automatically in its nightly quiet window (03:00–04:30 local) only while idle
  and only if automatic updates are enabled.
- Otherwise it offers the update on screen: **Install now / Later / Skip this version**.
- A manifest may mark a release `mandatory` (or set `min_version`), which makes it install even when
  automatic updates are off — but never over a prayer call.
