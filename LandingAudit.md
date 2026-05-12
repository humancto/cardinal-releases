# Cardinal landing-page audit checklist

_Per Slice E of `humancto/cardinal` ROADMAP.md. The landing page (`index.html`) makes specific claims about Cardinal. Some are durable (offline, no tracking); others rot under product evolution (chapter count, platform list, pricing). This checklist is the rot-detection contract — run it before every release and again 30 days before any v1.0 launch._

> **Last audit:** 2026-05-12 — v0.10.15 footer bump + v0.10.14 yank. **Next mandatory:** before v1.0 launch.

---

## How to run the checklist

1. Check out `humancto/cardinal` and `humancto/cardinal-releases` side by side.
2. In the codebase repo, run `cd prototype && ./scripts/build-app.sh install` (or any `build-app.sh` invocation) to refresh the drift artifacts.
3. Walk every section below in order. Mark each line PASS / FAIL / N/A in a fresh dated section at the bottom of this file.
4. If any item FAILs, **fix the landing page before the next release goes out.** Aggregate fixes in a single PR-B-style commit to `cardinal-releases`.

---

## 1. Platform claims

- [ ] `<meta name="description">` lists ONLY "Mac" (NOT "iPad", NOT "iPad & Mac"). iPad-platform claims unlock at v2.0 per PRD canonical-user revision.
- [ ] `<meta name="description">` matches the canonical user copy in the hero (Mac-first).
- [ ] CTA download button shows "Download for Mac" + macOS-version metadata (`macOS 14+ · Universal …`).
- [ ] No `aria-label` or hidden text mentions iPad.

**Verification:**

```sh
grep -i "ipad" index.html             # must return 0 matches
grep -E "macOS 14\+" index.html       # must return ≥ 1 match (CTA meta)
```

---

## 2. Chapter count

The landing page hard-codes the chapter count ("One hundred and forty-three chapters"). The catalog can grow. The drift gate is a manual diff against the codebase artifact:

- [ ] Read `humancto/cardinal/prototype/.build/chapter-count.txt`. **Current expected: 143.**
- [ ] Find every hard-coded count in `index.html`:

  ```sh
  rg -E "[Oo]ne hundred and forty-three chapters|143 chapters" index.html
  ```

- [ ] If the artifact ≠ landing-page count, the landing page is stale. Update both the H2 heading and the `og:description` meta tag in the same PR.

---

## 3. Privacy claims

Identifiers to keep verbatim on the page:

- [ ] "No signup. No tracking. No telemetry. No analytics." — must appear under the hero.
- [ ] "Your child's work stays on your Mac." — must appear under the hero.
- [ ] "The only time Cardinal connects to the internet is to check for updates — and only if you say yes." — must appear under the hero.

**Sub-claim verification (`PrivacyInfo.xcprivacy` drift gate):**

- [ ] Read `humancto/cardinal/prototype/.build/privacy-api-count.txt`. **Current expected: 1.**
- [ ] Bumping that count means a new required-reason API is declared — typically a new dep. Audit the dep change and update this checklist's "expected" value in the same PR.

```sh
# In the codebase repo:
grep -cE '<key>NSPrivacyAccessedAPIType</key>$' prototype/Sources/CardinalDemo/Resources/PrivacyInfo.xcprivacy
# Must equal the number recorded above.
```

---

## 4. Identity-first language sentinel

Cardinal commits to identity-first language ("autistic kids" / "autistic children") per the autistic community's strong, near-unanimous preference. NOT "with autism", NOT "special needs", NOT euphemisms.

- [ ] Forbidden-phrase grep returns empty:

  ```sh
  grep -iE "children with autism|kids with autism|with autism|special needs|differently abled" index.html
  # Must return 0 matches.
  ```

- [ ] Required-phrase grep returns ≥ 1 match:

  ```sh
  grep -iE "autistic (kids|children)" index.html
  # Must return ≥ 1 match.
  ```

A future contributor or AI-copyediting pass may "correct" identity-first to person-first phrasing as a style normalization. This sentinel catches that drift.

---

## 5. Accessibility-claim coverage

Cardinal shipped a verifiable accessibility-audit framework in Slice D-1. The landing page should reflect it concretely — not vague "we care about accessibility" copy.

- [ ] The "What makes Cardinal different" section contains a feature card naming the audit framework with at least four concrete commitments (e.g. "VoiceOver summary", "Switch Control path", "Reduce Motion behaviour", "target-size floor"). Verbiage like "verifiable", "artifact", or "contract" should appear.
- [ ] The card explicitly references that the audit is in-codebase + source-scanned (so a parent who clicks through to GitHub can verify it).
- [ ] Forbidden phrasing: "fully accessible", "accessible to all", "accessibility-first" without specifics. These are unfalsifiable marketing claims and trigger sophisticated readers' skepticism.

---

## 6. Pricing copy

Pricing decision per `humancto/cardinal/ROADMAP.md` §176 lands ≥ 30 days before v1.0. Until then, the landing page must NOT make pricing commitments.

- [ ] No "buy once, own forever" copy on the page.
- [ ] No "$N" / "one-time purchase" / "lifetime updates" copy.
- [ ] CTA metadata says "Free preview" (during closed beta) or "Free during closed beta" — not "Free forever".
- [ ] Once pricing lands, replace the "Free during closed beta" copy with the committed price and update this checklist.

---

## 7. Build / install / update flow

- [ ] "How to install" section accurately describes the current DMG path (`Cardinal-latest.dmg` → drag to Applications).
- [ ] If `Cardinal-latest.dmg` is renamed or relocated, both the CTA URL and the install-step text must update.
- [ ] Sparkle auto-update claim ("Cardinal checks for updates — and only if you say yes") matches the actual `CalmLandingView` default (Sparkle consent toggle defaults OFF per PRD §4.2 and §12.3).

---

## 8. Date-stamp comment block

- [ ] HTML-comment audit block at the top of `<body>` is current (within 90 days). Format:

  ```html
  <!-- LandingAudit: passed YYYY-MM-DD against PRD vX.Y.Z, ROADMAP vX.Y.Z.
       ... claims summary ...
       Next mandatory re-audit: before v1.0 launch. See LandingAudit.md. -->
  ```

- [ ] On every audit pass, update the date + the PRD/ROADMAP versions referenced.

---

## 9. Cloudflare analytics token

- [ ] If `data-cf-beacon` still holds `REPLACE_WITH_CF_TOKEN`, analytics aren't firing — page traffic is invisible. This is OK pre-launch but should be replaced before v1.0 launch.

---

## Audit log

### 2026-05-12 — v0.10.15 footer bump (re-release after v0.10.14 stale-binary yank) (Archith)

- **Codebase ships**: `humancto/cardinal#34` (GreetingStrip tap target) merged at `740b6dc`, but v0.10.14 packaging shipped a stale binary. `humancto/cardinal#35` (`bb22fff`) closed the `build-dmg.sh` regression with forced rebuild + two-layer version-pin; v0.10.15 re-released with the actual binary inside (independently verified via `plutil -extract CFBundleShortVersionString` on the in-DMG Info.plist).
- **`cardinal-releases#5`** merged — `appcast.xml` v0.10.14 `<item>` yanked. Comment in the XML documents the yank for the next maintainer.
- **Footer version** v0.10.14 → v0.10.15.
- **Audit-block** PRD v0.10.14 → v0.10.15.
- §1 platform sentinel `grep -ciE "ipad"` = 0. PASS.
- §2 chapter count: 146 unchanged. PASS.
- §3 privacy claims: all three identifiers present. PASS.
- §4 identity-first sentinel: PASS.
- §6 pricing copy: still "Free during closed beta". PASS.

**Next audit due:** before v1.0 launch.

### 2026-05-12 — v0.10.14 home-screen tap-target fix (Archith)

- **Codebase ship**: `humancto/cardinal#34` merged squash — `HomeView` greeting strip now opens `ProfileEditorSheet` on tap. New source-scan invariant `HomeGreetingTapTargetChecks` (7 assertions) pins the wiring against regression. Apple-expert APPROVE on the final diff after one fold-in cycle (M1 source-scan + M2 a11y label/hint).
- **Landing copy** unchanged content-wise — no chapter-count change, no platform claim change, no privacy claim change. Only the footer version + audit-block date/version bump.
- **Footer version** v0.10.13 → v0.10.14.
- **Audit-block date** 2026-05-11 / PRD v0.10.13 → 2026-05-12 / PRD v0.10.14.
- §1 platform sentinel `grep -ciE "ipad"` = 0. PASS.
- §2 chapter count: 146 unchanged. PASS.
- §3 privacy claims: all three identifiers present. PASS.
- §4 identity-first sentinel: PASS.
- §6 pricing copy: still "Free during closed beta". PASS.
- §8 date stamp: refreshed to 2026-05-12 / v0.10.14. PASS.

**Next audit due:** before v1.0 launch.

### 2026-05-11 — v0.10.13 chapter-count + Cluster A copy + email scrub (Archith)

- **Chapter count bumped** 143 → 146 in `og:description`, hero H2 ("One hundred and forty-six chapters"), chapter-count placeholder comment, README "What's inside". Reflects v0.11 Cluster A landing.
- **Fractions band description rewritten** to name the Cluster A additions explicitly: adding/subtracting (same + unlike), fraction multiplication, decimal arithmetic, × 10 / × 100 / × 1000.
- **New feature card "Exact math. No floating-point hiccups."** — calls out the 0.1 + 0.2 = 0.3 invariant. Pedagogically the most distinct claim Cardinal can make vs typical ed-tech.
- **Feedback section gains "Private report" CTA** — third row pointing at GitHub Private Vulnerability Reporting (SECURITY.md). Replaces the "or" connector with `·` separator.
- **Footer version** bumped v0.10.9 → v0.10.13.
- **README.md** rewritten "What's inside" to lead with v0.11 Cluster A, added Reduce Motion / pure-integer-arithmetic / audit-as-artifact bullets, rewrote "Bug reports" section into "Feedback channels" with public/private/in-app paths.
- **Maintainer-email scrub** — `FEEDBACK.md` private-channel re-routed to GitHub Private Vulnerability Reporting; `ISSUE_TEMPLATE/config.yml` contact-link similarly; new `SECURITY.md` landing. Zero personal email exposure in public-facing files.
- §1 sentinel `grep -ciE "ipad"` = 0. PASS.
- §4 identity-first sentinel: PASS.
- §6 pricing copy: still "Free during closed beta" — no pricing commitment. PASS.
- New sentinel needed: SECURITY.md exists at repo root + linked from FEEDBACK.md + index.html. PASS (manually verified).

**Next audit due:** before v1.0 launch.

### 2026-05-11 — v0.10.9 feedback infrastructure (Archith)

- New "Tell me what helps" section added with GitHub Issues + Discussions + FEEDBACK.md links.
- Footer version bumped v0.10.7 → v0.10.9.
- Date-stamp comment refreshed to PRD v0.10.9 / ROADMAP v0.10.9.
- §1 Platform sentinel `grep -ciE "ipad"` = 0. PASS.
- §4 Identity-first sentinel: 2 identity-first matches, 0 forbidden phrases. PASS.
- §6 Pricing copy: still "Free during closed beta" — no pricing commitment. PASS.
- §9 Cloudflare token: still placeholder. Acceptable pre-launch.
- New surfaces: GitHub Discussions enabled at github.com/humancto/cardinal-releases/discussions; two issue templates published (feature-request.yml, bug-report.yml) with privacy-acknowledgement checkboxes; FEEDBACK.md committed with the full feedback-channel guide.

### 2026-05-06 — Slice E PR-B (Archith)

- §1 Platform claims: corrected `<meta description>` to remove "iPad" (PRD now Mac-first).
- §2 Chapter count: 143 matches `prototype/.build/chapter-count.txt`. PASS.
- §3 Privacy claims: PASS. All three identifiers present.
- §3 `PrivacyInfo.xcprivacy` API count: 1, matches checklist baseline. PASS.
- §4 Identity-first sentinel: PASS (no forbidden phrases, "autistic kids" + "autistic children" both present).
- §5 Accessibility claim coverage: added new feature card naming the Slice D-1 audit framework with VoiceOver/Switch Control/Voice Control/Dynamic Type/Reduce Motion/target-size enumeration + "verifiable artifact / contract" verbiage. PASS.
- §6 Pricing: removed "Buy once, own forever / Lifetime updates included / Family Sharing eligible" feature card. Replaced with "No account. Free during closed beta." PASS.
- §7 Install flow: unchanged from prior audit. PASS.
- §8 Date-stamp: comment block added at top of `<body>`. PASS.
- §9 Cloudflare token: still `REPLACE_WITH_CF_TOKEN` placeholder — pre-v1.0, acceptable.

**Next audit due:** 2026-08-04 (90 days) or before v1.0 launch, whichever is sooner.
