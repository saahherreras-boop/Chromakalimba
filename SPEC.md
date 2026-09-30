# ChromaKalimba — App Specification v1

**Owner:** Thomas Herreras (Tucson, AZ)
**Purpose:** Mobile chromatic tuner for the 17-note kalimba, monetized with ads to generate revenue for the Starving Artists Against Hunger (SAAH) fundraiser.
**Status:** Spec drafted Sep 21, 2026. Prior prototype work was lost to a platform update; this document preserves the concept independently of any code.

---

## 1. The problem

Guitar players have Guitar Tuna and a dozen other polished tuners. Kalimba players have almost nothing purpose-built. The kalimba (also called thumb piano or thumb harp) is one of the fastest-growing beginner instruments in the world, and it **needs** tuning more often than a guitar, not less — the tines shift with use, temperature, and humidity, and each one is tuned by physically tapping it up or down the bridge with a small hammer.

Most kalimba players today either use a generic chromatic tuner app that knows nothing about kalimba layout, or they use a guitar tuner that mislabels the notes entirely. There is a real, unserved need here.

## 2. The instrument (why layout matters)

The target instrument is the standard **17-key kalimba in the key of C**. Its defining quirk is the tine layout: notes do not run left-to-right in pitch order. They alternate outward from the center tine.

This matters because a generic tuner shows you "F#" with no idea which physical tine that is. ChromaKalimba should show the player **the actual instrument** — a picture of the tine layout — and light up the tine being struck.

Standard 17-key C layout, center outward:

| Tine # (physical, left to right) | Note |
|---|---|
| 1 | D5 |
| 2 | B4 |
| 3 | G4 |
| 4 | E4 |
| 5 | C4 |
| 6 | A4 |
| 7 | F4 |
| 8 | D4 |
| 9 | C5 (center — longest tine) |
| 10 | E5 |
| 11 | G5 |
| 12 | B5 |
| 13 | D6 |
| 14 | F5 |
| 15 | A5 |
| 16 | C6 |
| 17 | E6 |

> **UNCONFIRMED.** The exact numbering convention must be verified against Thomas's physical instrument before build. The principle — center-out alternation, not linear — is the part that must be right. See Issue: "Confirm tine layout against physical instrument."

## 3. Core user flow

1. Player opens the app.
2. **Ad plays** (pre-roll).
3. App requests microphone permission (first launch only) and explains plainly why it needs it.
4. Tuner screen appears: kalimba tine diagram + large pitch readout.
5. Player strikes tine 1. App detects the pitch, identifies the nearest target note, and shows:
   - Note name (e.g. "D5")
   - Cents deviation, with a needle or arc
   - Plain-language direction: **"Slightly flat — tap the tine down"**
6. Player adjusts with the tuning hammer, strikes again, repeats until green.
7. App marks tine 1 as tuned and advances to tine 2.
8. **Ad plays** at roughly the halfway point (around tine 8 or 9).
9. Player finishes all 17 tines.
10. **Ad plays** (post-roll), followed by a "Fully tuned" confirmation screen.

### Design principles

- **Plain language over jargon.** "Tap it down a little" beats "-14 cents."
- **Hands are busy.** The player is holding an instrument and a hammer. Big targets, no fiddly controls, auto-advance between tines.
- **Forgiving detection.** Kalimba tines have a strong fundamental but ring and decay fast. Detection must lock on quickly and hold the reading long enough to read it.

## 4. Feature list

### Must-have (v1)

- Microphone access with clear permission explanation
- Real-time pitch detection
- 17-tine guided walkthrough, in order, with auto-advance
- Visual tine diagram showing which tine is active and which are done
- Cents-accurate readout with plain-language direction
- "In tune" confirmation per tine and for the full instrument
- Three ad placements: pre-roll, mid-session, post-roll
- Free-play chromatic mode (strike any tine, app identifies it) for players who don't want the guided walkthrough

### Should-have (v2)

- **Tablature library** — users upload and browse kalimba tabs for popular songs. Kalimba tab is typically written as note letters or numbers, which is simple to store and display.
- Alternate tunings and key support (kalimbas also come in B, D, G, and 21-key versions)
- Reference tone playback (hear the target note)
- Calibration (A4 = 440 Hz default, adjustable)

### Nice-to-have (later)

- Tab upload moderation and voting
- Practice tools: metronome, loop, tempo slowdown
- SAAH tie-in: a small, honest in-app note about the fundraiser the app supports

## 5. Monetization

Ad-supported, free download. Three placements per tuning session as described above.

**Open questions to decide before build:**
- Ad network — Google AdMob is the standard choice for mobile and the easiest to integrate.
- Ad format — interstitial (full-screen, skippable after a few seconds) for all three slots, or rewarded video for the mid/post slots.
- Whether to offer a paid ad-free tier later.

**A candid note on revenue:** ad revenue per user is small — typically fractions of a cent to a few cents per impression. This app makes money at volume, not at launch. That's not a reason to skip it; it's a reason to build it right, get it into the stores, and let it compound. It also does something the fundraiser needs badly: it gives donors visible, tangible proof of work.

## 6. Technical approach

### Platform

Mobile, both iOS and Android. **Decision: Flutter** — one codebase ships to both stores, and it handles a real-time audio pipeline cleanly. This roughly halves the work versus building native twice.

### Pitch detection

This is the technical heart of the app. Options:

- **YIN or pYIN algorithm** — well-documented, good accuracy on monophonic sources, widely used in tuner apps
- **Autocorrelation / FFT with peak interpolation** — simpler, adequate for a tuner
- **Existing library** — several open-source pitch detection packages exist for Flutter, which is the fastest honest path to a working prototype

Target accuracy: within 1-2 cents, with a lock-on time under about 200ms.

### Stack sketch

| Layer | Choice |
|---|---|
| App framework | Flutter |
| Audio input | Platform mic APIs via framework bridge |
| Pitch detection | Open-source pitch library, or custom YIN implementation |
| Ads | Google AdMob SDK |
| Tab library backend (v2) | Lightweight backend — Supabase or Firebase |
| Source control | **This repository** |

### On not losing the work again

Every piece of this gets committed here. Progress lives in version history, not in a chat session. No platform update, purge, or outage can take it.

## 7. Build order

1. **Repo setup** — repository, README, spec, project scaffold
2. **Mic permission + raw audio capture** — get sound into the app and prove it works
3. **Pitch detection** — turn audio into a stable frequency reading
4. **Note mapping** — frequency to nearest kalimba note, plus cents deviation
5. **Tuner UI** — readout, needle, plain-language direction
6. **Guided 17-tine walkthrough** — tine diagram, active-tine highlight, auto-advance
7. **Ad integration** — AdMob, three placements
8. **Polish and store prep** — icon, screenshots, privacy policy, store listings
9. **Ship v1**
10. **v2: tablature library**

Steps 1 through 6 produce a genuinely useful app. Everything after that is monetization and reach.

## 8. Decisions still needed

- Confirm the exact tine numbering and note layout on the physical instrument
- iOS first, Android first, or both together
- Google Play / Apple Developer accounts — are they set up?
- Licence for this repository

---

## Appendix: why this idea is good

It is narrow, it is real, and it is underserved. It solves a problem the owner personally has, for an instrument he plays, with a monetization model already proven by the app he uses himself. The scope is small enough for one person to finish and specific enough that no one has bothered to do it well yet.

That combination is rare. The idea was worth protecting, which is what this document does.
