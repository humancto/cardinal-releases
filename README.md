# Cardinal — Releases

Public binary releases of **Cardinal** — an interactive handbook of early math, designed for autistic children and the families who learn alongside them.

→ **Landing page:** <https://humancto.github.io/cardinal-releases/>

## Download

The current release is always at:

→ **[Cardinal-latest.dmg](https://github.com/humancto/cardinal-releases/releases/latest/download/Cardinal-latest.dmg)**

That URL auto-resolves to whatever the most recent tag is, so it never goes stale.

Older releases are kept under [Releases](https://github.com/humancto/cardinal-releases/releases) by version (e.g. `Cardinal-v0.10.6.dmg`).

## Install

1. Download `Cardinal-latest.dmg`.
2. Open the DMG.
3. Drag `Cardinal.app` onto the **Applications** shortcut.
4. Open Applications and double-click Cardinal.

That's it. Cardinal is signed with an Apple Developer ID and notarized by Apple, so macOS opens it directly with no Gatekeeper warnings.

If you have an older `CardinalDemo.app` from v0.9.3 or earlier, drag it to the Trash — the app was renamed to `Cardinal.app` in v0.9.3.1 and the bundle ID is unchanged, so your profile, favorites, and progress carry over.

## Requirements

- macOS 14 (Sonoma) or later
- Apple Silicon **or** Intel — the binary is universal
- ~13 MB DMG, ~35 MB on disk after install

## Auto-updates

Cardinal v0.9.5+ includes [Sparkle](https://sparkle-project.org)-based auto-updates, signed with EdDSA. The first time you launch a new install, you're asked once whether Cardinal may check for updates — opt-in only. The check runs at most once a day. If you opt out, the app never connects to the internet at runtime.

## Privacy

- **No signup, no account, no analytics, no telemetry, no tracking.**
- The only time Cardinal connects to the internet is to check for updates — and only if you say yes.
- All progress (opened chapters, profile, favorites, practice rewards) is stored locally on your Mac in standard `UserDefaults` under `cardinal.*` keys, and the profile photo (if any) in `~/Library/Application Support/Cardinal/`.
- Cardinal has no third-party SDKs other than Sparkle (the auto-update framework, opt-in).

## What's inside

- **146 chapters across 8 bands** (Foundations through Fractions, Time, Money & World — ages 3–11).
- **v0.11 Cluster A** (shipped through v0.10.13): adding & subtracting fractions with same and unlike denominators (animated re-cut visualization), fraction multiplication (× whole and × fraction with area model), decimal addition / subtraction (with carry / borrow indicators), decimal × 10 / × 100 / × 1000 (with the decimal point physically sliding via `matchedGeometryEffect`).
- Errorless, kid-paced, no scoring, no streaks, no time pressure.
- **Pure integer arithmetic** for fractions and decimals — 0.1 + 0.2 = 0.3 exactly. No floating-point rounding visible anywhere in the math.
- **Reduce Motion respected** throughout — kids with motion sensitivity turn the System Settings flag and Cardinal honors it.
- **Per-chapter accessibility audit** as a verifiable artifact (VoiceOver summary, Switch Control path, Voice Control targets, Dynamic Type behavior, Reduce Motion behavior, target sizes). Source-scanned on every build.
- **Region toggles** for currency (USD / GBP / INR / EUR / AUD), units (metric / US imperial), and date format.
- **Practice tab** with a deck per chapter — drag, tap, drop. Prompts read aloud automatically when Sound is on. The dot returns home if it's not where it should be.
- Calm onboarding screen, profile (name + photo), favorites, recently-opened lane, custom themes (dot ↔ star / heart / leaf / planet).

## Verifying the download

For the security-minded:

```sh
# Confirm Apple's notarization (with browser-quarantine xattr)
spctl --assess --type open --context context:primary-signature --verbose Cardinal-latest.dmg
# expected: accepted; source=Notarized Developer ID

# Confirm the binary is universal (runs on both Apple Silicon + Intel)
hdiutil attach Cardinal-latest.dmg -nobrowse -mountpoint /tmp/cardinal-mount
lipo -archs /tmp/cardinal-mount/Cardinal.app/Contents/MacOS/Cardinal
# expected: x86_64 arm64
hdiutil detach /tmp/cardinal-mount
```

## License

This repository contains **binary releases only**. The Cardinal source code is private; pricing and source-availability decisions are not yet finalized.

## Feedback channels

Cardinal is free and maintained by one parent. Telling us what worked is the only signal we get.

- **Public**: [Open an Issue](https://github.com/humancto/cardinal-releases/issues/new/choose) (feature request or bug report — privacy-gated templates) or [Join the Discussion](https://github.com/humancto/cardinal-releases/discussions) (parent-to-parent stories, kid wins, general questions).
- **Private**: [GitHub Private Vulnerability Reporting](https://github.com/humancto/cardinal-releases/security/advisories/new) — maintainer-only visibility, works for any private-disclosure note (specific kid profile, beta inquiries, consent-deletion requests). See [SECURITY.md](./SECURITY.md).
- **In-app**: Settings → Parent area → Send feedback — three rows opening these channels in your system browser. Zero personal email exposed in the app.

Read the full [FEEDBACK.md](./FEEDBACK.md) guide for what gets read, what gets shipped, and what the privacy floor is.

---

_Cardinal — made with care for the children who learn at their own pace._
