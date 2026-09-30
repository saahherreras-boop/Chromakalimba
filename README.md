# ChromaKalimba

A chromatic tuner built specifically for the 17-key kalimba.

Guitar players have a dozen polished tuner apps. Kalimba players have almost nothing purpose-built — and the kalimba needs tuning *more* often than a guitar, not less. The tines shift with use, temperature, and humidity, and each one is tuned by physically tapping it along the bridge with a small hammer.

Generic chromatic tuners tell you a note name. They have no idea which physical tine that is, because kalimba tines don't run left to right in pitch order — they alternate outward from the centre. ChromaKalimba shows you the actual instrument, lights up the tine you just struck, and tells you in plain words whether to tap it up or down.

## Status

Pre-build. The specification is complete and lives in [`SPEC.md`](./SPEC.md). Build order and open decisions are tracked in this repository's Issues.

## Why this exists

ChromaKalimba is built to generate ad revenue for **Starving Artists Against Hunger (SAAH)**, a fundraiser getting food and basic supplies to homeless communities in the Tucson desert.

- Fundraiser: https://gofund.me/922b24d9b

## Planned stack

| Layer | Choice |
|---|---|
| App framework | Flutter (single codebase, iOS + Android) |
| Audio input | Platform mic APIs via framework bridge |
| Pitch detection | YIN / pYIN, or an established open-source pitch library |
| Ads | Google AdMob |
| Tab library backend (v2) | Supabase or Firebase |

## Roadmap

**v1** — guided 17-tine tuning walkthrough, real-time pitch detection, plain-language tuning direction, free-play chromatic mode, three ad placements.

**v2** — kalimba tablature library, alternate tunings and keys, reference tone playback, calibration.

## Author

Thomas Herreras — Tucson, Arizona.

## Licence

To be decided before v1 ships.
