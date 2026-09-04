# Site Cleanup Report — S185

**Date:** 2026-09-04
**Scope:** aiinternet-ai (umbrella), athenapilot-ai (product), and "vacprotocol.org/Schemo512" (identity/dev) per
the task brief — plus `VioletShores/vac-web`, discovered mid-task to be the actual live source for
vacprotocol.org (see Finding 1 below).
**Branch used in every repo:** `site-cleanup-s185` (pushed, **not** merged to `main` — see Authorization note).

**Self-review:** ran a multi-angle diff review (line-by-line, removed-behavior, cross-file consistency,
cleanup/conventions) across all four repos' diffs before calling this done. Found and fixed 5 real issues in
follow-up commits on the same branches: a stale "Athena OS" link left in aiinternet-ai's footer after the
athenapilot-ai rebrand, the same stale label in athenapilot-ai's own README, a `color: ... !important` in the
vac-web/Schemo512 stack-nav that silently prevented any hover/current text-color change, and the illustrative
receipt-panel example lacking a clear "example, not live" eyebrow next to genuinely-live demo cards.

---

## 0. Blockers / discrepancies found before doing the work

**Missing strategic docs.** `docs/strategic/SITE-ARCHITECTURE-S185.md` and
`docs/strategic/PLAIN-ENGLISH-PRODUCT-BLOCK-S185.md` do not exist in any of the three named repos, in their
full git history, or in any discoverable "athena" repo (searched GitHub for repos matching `athena` under the
same accounts and via public search — none matched). The dedupe/refocus work in §2 was done from the existing
site copy and cross-repo research (see §2) instead of those two docs. If they exist somewhere I don't have
access to, the athenapilot-ai product copy in this branch should be checked against them before merging.

**Finding 1 — `Schemo512/vacprotocol.org` is a stale mirror; `VioletShores/vac-web` is the live site.**
The task named `vacprotocol.org/Schemo512` as the identity/dev repo. Live-content diffing shows otherwise:
- `https://vacprotocol.org/demo` and `/developers` resolve live (200) to files (`control-demo.html`,
  `developers.html`, byte-for-byte content-length match) that exist in `VioletShores/vac-web` but **do not
  exist anywhere in `Schemo512/vacprotocol.org`'s history** (checked `git log --all`).
- `Schemo512/vacprotocol.org`'s `vercel.json` has no rewrite for `/demo` or `/developers` at all.
- `Schemo512/vacprotocol.org` was last pushed 2026-08-02; `VioletShores/vac-web` was last pushed 2026-09-02 (2
  days before this report) and its `CLAUDE.md` states "Push directly to main. Vercel auto-deploys from main."
- `Schemo512/vacprotocol.org`'s homepage still advertises "285 patent claims across 6 filings" — `vac-web`'s
  says 1,068 across 17. The Schemo512 repo is behind by a large margin.

Net effect: fixes made only in `Schemo512/vacprotocol.org` would not reach the live site. I made the real fix
in `vac-web` and, for completeness against the task's literal repo list, mirrored the safe/additive changes
(teal footer nav) into `Schemo512/vacprotocol.org` too. **Recommendation:** confirm with whoever owns Vercel
project config which repo is actually wired to the `vacprotocol.org` custom domain, and either archive/relink
`Schemo512/vacprotocol.org` or resync it from `vac-web` — right now it's a live landmine for anyone who edits
it expecting it to affect production.

**Authorization.** `ATHENA_PERMIT_JSON` was absent/empty for this run. Per the AUTHORIZATION contract, that
means no authority to merge or push to `main` on any repo. All work below is committed and pushed to a
`site-cleanup-s185` branch in each repo; **no repo's `main` was touched.** Someone with a valid permit needs to
review and merge these branches.

---

## 1. Dead-link audit — every href checked, resolved status, and action taken

### aiinternet-ai (`index.html`)
| Link | Status | Action |
|---|---|---|
| `#demo`, `#ip`, `#problem`, `#stack` | anchors exist | none needed |
| `/` | 200 | none |
| `/athena-dna.css` | 200 | none |
| `/favicon.ico` | **404** (no file in repo) | **fixed** — added `favicon.ico` (teal dot on canonical bg, generated) |
| `/origin.html` | 200 | none |
| `https://athenapilot.ai` | 200 | none |
| `https://vacprotocol.org` | 200 | none |
| `https://vacprotocol.org/demo` | 200 (via vac-web rewrite) | none |
| `https://vacprotocol.org/developers` | 200 (via vac-web rewrite) | none |
| `https://vacprotocol.org/vac-whitepaper-v5.pdf` | 200 | none |
| `mailto:admin@violetshores.com` | not HTTP-checkable | none |
| Google Fonts preconnect/stylesheet links | 200 (trusted CDN) | none |

### athenapilot-ai (`index.html`)
| Link | Status | Action |
|---|---|---|
| `#architecture`, `#patents`, `#roadmap`, `#standards`, `#token-economics`, `#working-today` | anchors existed pre-cleanup | **removed with the sections they pointed to** (see §2) — replaced with `#channels`, `#governed`, `#receipts`, `#working-today`, all now valid |
| `/athena-dna.css` | 200 | synced with aiinternet-ai's copy (added the new `.stack-nav` rules) |
| `/favicon.ico` | **404** (no file in repo) | **fixed** — same generated favicon.ico added |
| `/index.html` | 200 | none |
| `https://aiinternet.ai`, `https://aiinternet.ai/origin.html` | 200 | none |
| `https://vacprotocol.org` | 200 | none |
| `https://vacprotocol.org/compare` | **404 live** (no such route in vac-web's `vercel.json`; this was the CTA link, used 3×: nav CTA, hero CTA, "Try SignalRank Compare") | **fixed** — all 3 repointed to `https://vacprotocol.org/demo` (confirmed live) |
| `https://vacprotocol.org/demo` | 200 | none |
| `mailto:admin@violetshores.com` | not HTTP-checkable | none |

Note: the prior commit on `aiinternet-ai` main (`7d51a30`, "dead /compare CTA -> live /demo") only fixed
`/compare` on the aiinternet-ai side. athenapilot-ai still had 3 live `/compare` links pointing at the same
404 — this was the actual highest-traffic broken CTA (product page's primary "Try Working Demos" button).

### `Schemo512/vacprotocol.org` (`index.html`) — see Finding 1, not the live source
| Link | Status | Action |
|---|---|---|
| `#`, `#alignment`, `#architecture`, `#capabilities`, `#contact`, `#how`, `#ip` | anchors exist | `href="#"` (logo mark) changed to `href="/"` for cleanliness, rest untouched |
| `/apple-touch-icon.png`, `/favicon.ico`, `/simulation.html`, `/vac-testing-framework-v5.pdf`, `/vac-whitepaper-v4.pdf`, `/auth` | 200 (served live via vac-web, not this repo) | none — this repo's own files happen to match what's live, but see Finding 1 |
| `mailto:admin@violetshores.com` | not HTTP-checkable | none |

### `VioletShores/vac-web` (`index.html`) — actual production source for vacprotocol.org
All links resolved 200 already (this is the live, actively-maintained repo). The only gap: its footer linked
to `aiinternet.ai` but not `athenapilot.ai` — see §3.

---

## 2. Dedupe — athenapilot-ai refocused on product

**Removed** (duplicated aiinternet-ai's umbrella-level "orchestration layer" thesis): the Token Economics
section, the Kernel/Scheduler/Registry architecture diagram, the F-133 substrate roadmap, the Skill Registry
Protocol section, the full Patent Portfolio section, and the Standards Engagement section. All of this restated
material that already lives on aiinternet.ai's "Five Infrastructure Layers" section (layer 05 = "Orchestration
/ Athena") and IP section (1,068 claims / 17 filings, of which Athena's slice is a sub-count) — it was the
duplication the task asked to strip.

**Added** — refocused on the actual product, structured around the four things named in the task
(WA/Chrome/governed answers/provenance-receipts):
- **Channels** section: WhatsApp + Chrome extension as the two entry points.
- **Governed Answers** section: SignalRank-scored model choice, honest refusal instead of guessing.
- **Receipts** section: a provenance receipt per answer (model, confidence, refusal, VAC-signed attestation),
  with an illustrative example panel, explicitly labeled illustrative rather than a live data pull.
- Kept **Working Today**, repointed at the two demos that are actually live (SignalRank Compare, Control
  Loop demo), reframed as evidence of the governance layer rather than "kernel primitive" language.
- Added a single up-link: *"part of the [AI Internet stack →]"* in the hero, linking to aiinternet.ai — the one
  cross-reference the task asked for, replacing the removed sections' worth of restated umbrella thesis.
- Trimmed the IP/patent framing to one footer line linking to `aiinternet.ai/origin.html` instead of a full
  section, since the numbers were a duplicate sub-count of the umbrella IP total.

**Caveat:** `docs/strategic/PLAIN-ENGLISH-PRODUCT-BLOCK-S185.md` doesn't exist (see §0), so this copy was
written from scratch based on the task's WA/Chrome/governed-answers/receipts brief, VAC Protocol's existing
"signed receipts" vision language (`vac-web/content/blocks/vision.md`), and the SignalRank trust-scoring
mechanism already documented elsewhere in the stack — not copied from an authoritative product-copy source,
because none was found. **This should get a product-owner read before it ships** — it's my best-faith
reconstruction of "governed answers + receipts" messaging, not verified copy.

Synced `athena-dna.css` between aiinternet-ai and athenapilot-ai (README-documented requirement — "keep in
lock-step") to carry the new `.stack-nav` rules into both.

---

## 3. Teal cross-link footer nav

Added a `.stack-nav` footer component — a small pill-row cross-linking aiinternet.ai / athenapilot.ai /
vacprotocol.org, styled with the canonical `#2DD4BF → #10B981 → #3B82F6` gradient — to all four touched repos
(aiinternet-ai, athenapilot-ai, vac-web, and Schemo512/vacprotocol.org for consistency per Finding 1).

**What I did *not* do: a full site repaint to teal-primary.** The task asked to "ensure all three use the
canonical teal palette [...] registered constellation geometry." I looked for that canon and didn't find it:
- `aiinternet-ai`/`athenapilot-ai`'s shared `athena-dna.css` explicitly documents purple-primary/teal-secondary/
  gold-tertiary as the intentional design ("Purple is the primary accent, teal is the secondary, gold is
  tertiary" — file header comment).
- `vac-web`'s own `assets/brand/BRAND.md` (source of truth for its brand rails) defines a *gold*-on-ink palette
  as canonical for one page family, documents purple/green/navy as the existing inline hexagon-mark colors on
  product surfaces, and explicitly flags a full palette swap as "a separate, riskier swap tracked as future
  work, not this lane" — i.e., the same call I'm making here, already precedented in that repo.
- vacprotocol.org's actual homepage is a *light* theme (paper `#f8f7f4` / navy ink, DM Serif Display) — a third,
  distinct visual system from the dark teal/purple/gold theme and from the dark teal/emerald pages
  (`control-demo.html`, `developers.html`) that most closely match the task's hex codes.
- No "registered constellation geometry" asset exists in any repo (searched all four for `constellation`,
  `.svg` logo marks, and vac-web's brand doc) — the closest match is `vac-web/athena-owl.svg`, a *gold*-rail
  mark documented for a completely different page family (internal "briefs"), not a teal constellation.

Repainting three separate, already-shipped, differently-themed sites to a "canonical" palette that isn't
implemented or documented anywhere would be a large, high-blast-radius design decision on a live pre-customer
surface, made unilaterally with no visual QA loop available in this environment. I scoped item 3 down to the
concrete, safe, literally-requested piece — the cross-link footer nav in the specified gradient — and flagged
the rest here for a deliberate design pass with sign-off, consistent with how `vac-web`'s own brand doc already
treats full-palette swaps.

---

## 4. Static site / no backend

No backend changes made. All edits are HTML/CSS in four static site repos, deployed via Vercel per each repo's
existing `vercel.json`.

---

## 5. Per-repo changes summary

| Repo | Branch | Files changed |
|---|---|---|
| `aiinternet-ai` | `site-cleanup-s185` | `index.html` (footer stack-nav), `athena-dna.css` (+`.stack-nav`), `favicon.ico` (new) |
| `athenapilot-ai` | `site-cleanup-s185` | `index.html` (full refocus per §2), `athena-dna.css` (synced), `favicon.ico` (new), `README.md` (updated positioning) |
| `Schemo512/vacprotocol.org` | `site-cleanup-s185` | `index.html` (footer stack-nav, `href="#"` → `/`) |
| `VioletShores/vac-web` | `site-cleanup-s185` | `index.html` (footer stack-nav, added athenapilot.ai link) |

All four pushed as branches only — **not merged to `main`** (no `ATHENA_PERMIT_JSON`). Someone with a valid
permit should review each branch and merge.

## 6. Not done / follow-ups for a human

1. Full teal/emerald/blue "canonical palette" + "registered constellation geometry" rollout — needs a design
   decision + owner, not a unilateral static-site-cleanup call (§3).
2. Resolve the `Schemo512/vacprotocol.org` vs `vac-web` duplicate-repo situation (§0, Finding 1) — one of them
   should be archived or resynced so future edits don't land in the wrong place.
3. Locate or (re)write `docs/strategic/SITE-ARCHITECTURE-S185.md` and
   `docs/strategic/PLAIN-ENGLISH-PRODUCT-BLOCK-S185.md` — the athenapilot-ai product copy in this branch was
   written without them and should get a product-owner read against the real source if/when it turns up.
4. No real-browser visual QA was performed on these changes (no GUI browser available in this execution
   environment) — recommend a manual pass on all four branches' preview deploys before merging.
