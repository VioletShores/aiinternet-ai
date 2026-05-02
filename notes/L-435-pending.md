# L-435 (PENDING — to be promoted to athena/self-development/LEARNING-INBOX.md after D-CI-INVESTIGATION resolved)

> **Status:** Captured 2 May 2026 Session 67 final-final. Pending promotion to LEARNING-INBOX.md when athena commits unblock (D-CI-INVESTIGATION must resolve first per L-433).

## L-435: Verify design system source-of-truth from actual built artifacts, not from cached/remembered descriptions (Session 67 Day 5 night-late, 2 May 2026)

**Trigger:** 2 May 2026 Session 67 final-final — strategic site architecture refresh kicked off with chat-Claude prompting Phase 1 with the description "Plus Jakarta Sans weight 800 + JetBrains Mono / teal/emerald/blue/gold palette / fractalNoise grain overlay" as the Athena DNA spec. Mini read the actual `vac-web/athena-about.html` source file before drafting `athena-dna.css` and caught that the file's actual implementation uses **Cormorant Garamond + DM Sans + JetBrains Mono / Purple #7C6BF0 (primary) + Teal #2DD4BF + Gold #E8B84B / radial-gradient ambient (no SVG noise)**. Three substantive discrepancies between the prompted description and the actual file. Mini surfaced the discrepancy, awaited founder confirmation, founder confirmed the file is canonical and the prompt was wrong, locked the actual tokens. Cost: one round-trip review cycle. Bounded but unnecessary if chat-Claude had read the file before drafting Phase 1.

**Rule:** When prompted to extract or reference design tokens (or any structural artifact) from a specific file, **read the file FIRST, THEN write or evaluate the prompt**. Do not rely on userMemory descriptions or cached impressions. Source-of-truth principle: the artifact is canonical; descriptions of the artifact are derived and may have drifted.

**The asymmetry:** in code review, this principle is well-established — review the actual diff, not the PR description. In design / token / spec extraction work the same principle is easy to overlook because the prompt itself reads as authoritative. It's not. The file is.

**Failure modes prevented:**
- Producing a "DNA" CSS file that doesn't actually match the page it claims to abstract from — every downstream consumer (origin.html, athenapilot.ai/index.html, future pages) would inherit a divergence
- Migrating aiinternet.ai (Phase 4) toward a phantom target rather than the actual existing brand
- Future-Claude reading the prompt history later and treating the prompted description as canonical

**Future-Claude operational rule:** when handed a prompt of the form "extract X from file Y" or "match the design of file Y", first action is `Read` the file. If the file's content disagrees with the prompt, surface the discrepancy and confirm before producing output. The 60-second read cost is always cheaper than a round-trip review cycle that catches the drift later.

**Cross-ref:** Companion to L-279 family (zombie state — descriptions can persist even after the underlying artifact has changed). Companion to L-427 (numeric callout three-pattern audit — same shape: don't trust the surface-level description, verify against the implementation).

**Impact:** PROCESS — bounded cost (one review cycle) when caught early. Compounding cost when not caught — every artifact built on top of the wrong tokens inherits the divergence and needs rework.

---

## Why this file is in `aiinternet-ai/notes/` and not `athena/self-development/LEARNING-INBOX.md`

D-CI-INVESTIGATION (per L-433) is blocking athena commits until tomorrow morning's CI workflow fix. To honor that block, this learning is captured in the aiinternet-ai repo (which has no CI dependency) as a working-tree note. **Promote to LEARNING-INBOX.md after D-CI-INVESTIGATION resolves**, then delete this pending file.
