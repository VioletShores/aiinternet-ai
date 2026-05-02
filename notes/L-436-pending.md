# L-436 (PENDING — to be promoted to athena/self-development/LEARNING-INBOX.md after D-CI-INVESTIGATION resolved)

> **Status:** Captured 2 May 2026 Session 67 final-final-final. Pending promotion to LEARNING-INBOX.md when athena commits unblock (D-CI-INVESTIGATION must resolve first per L-433).

## L-436: Local dev vs production URL handling — clean URLs break python http.server (Session 67 Day 5 night-late, 2 May 2026)

**Trigger:** Phase 4 review of `aiinternet-ai/index.html` migration. Origin nav link rendered as `href="/origin"` (clean URL, no extension). Worked fine on Vercel production deployments. Returned 404 on local `python3 -m http.server 8000`. Founder caught the bug during pre-commit local-server eyeball test.

**Root cause:** Vercel's deployment runtime applies clean-URL rewriting by default — `/origin` automatically resolves to `/origin.html` on the edge. Python's stdlib `http.server` is **strict file-match-only** — looks for a literal file named `origin` (no extension), doesn't find one, returns 404. The two environments diverge silently.

**Rule (front-end / static-site URL discipline):** for any project that may be tested locally with `python3 -m http.server` or any non-Vercel-aware dev server, use **explicit file extensions** in `href` attributes (`href="/origin.html"`, NOT `href="/origin"`). Two exceptions:

1. The project ships with a `vercel.json` `rewrites` config that explicitly maps clean URLs to `.html` files, AND a local dev tool that respects that config (e.g., `vercel dev`, not `python -m http.server`)
2. The project has its own static-site framework with a dev server matching production (e.g., Astro, Next.js)

For plain HTML projects (like `aiinternet-ai`, `vac-web`, `athenapilot-ai`), explicit extensions are the cheapest way to keep local + prod parity.

**Why it matters:** silent breakage where "works in production, breaks local" is one of the worst classes of bug — the local dev cycle is supposed to be the cheap iteration loop. When local 404s on a link that production resolves cleanly, every contributor hits the same false-negative and wastes time investigating "is the link broken in prod too?" Cheap to prevent (just write the extension), expensive to debug.

**Failure modes prevented:**
- Founder eyeball-test finds 404 on Origin nav, has to context-switch to debug
- Future contributor adds a new `<a href="/about">` thinking it'll work, doesn't test locally, ships it, broken on local for everyone forever
- Multi-page PR review where reviewer can't click between pages because every nav link 404s

**Future-Claude operational rule:** when writing or migrating HTML pages that will be tested with `python3 -m http.server`, use `href="/page.html"` not `href="/page"`. When in doubt, grep the project for existing href patterns and match. If the project has a `vercel.json` with rewrites, use clean URLs only if the rewrites cover them AND the local dev tool respects rewrites.

**Cross-ref:** Companion to L-279 family (zombie state — environments that diverge silently). Companion to L-427 (numeric callout sweep — both involve "works in one place, broken in another" verification gap that requires multi-pattern audit).

**Impact:** PROCESS — bounded cost (one 5-minute Edit to fix all instances) when caught locally. Compounding cost when not caught — every contributor hits the same broken link in every branch.

---

## Why this file is in `aiinternet-ai/notes/` and not `athena/self-development/LEARNING-INBOX.md`

D-CI-INVESTIGATION (per L-433) is still blocking athena commits until the pytest workflow is fixed. To honor that block, this learning is captured in the aiinternet-ai repo as a working-tree note. **Promote to LEARNING-INBOX.md after D-CI-INVESTIGATION resolves**, then delete this pending file.

L-436 follows L-435 which is also pending in this directory. Promote together.
