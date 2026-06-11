# HANDOFF — Better Half persona MVP

Pick-up notes for a fresh session. Everything below is already built, deployed, and committed.

## What this is
A small web app where people chat with relational-coaching personas built on Better Half's
"regulate before you relate" method. One static page + one Netlify Function that injects a
persona system prompt and calls Claude.

- **Live:** https://better-half-tami.netlify.app  ·  **access code:** `betterhalf2026`
- **Repo:** github.com/AnastasiaUglova/better-half-tami (private)
- **Local:** `/Users/anabeondeck.com/Claude Projects/Better Half/tami-site`
- **Netlify project:** `better-half-tami` (linked; CLI authed as Anastasia)

## Tooling state (machine-level, already set up)
- `netlify` CLI installed + logged in. `gh` CLI authed (AnastasiaUglova). `node`, `python3` present.
- Deploy to prod: `cd tami-site && netlify deploy --prod`
- Preview (no prod change): `netlify deploy` → returns a Draft URL
- Always `node --check netlify/functions/chat.js` before deploying.
- Auto-deploy on git push is **NOT** wired (Netlify GitHub App was never connected). Deploy via CLI.

## Architecture
- `index.html` — the whole UI (no build step): header (Better Half logo, persona dropdown, model
  picker), chat, composer with 📎 screenshot upload. Persona definitions (title/subtitle/placeholder/
  opener) live in a `PERSONAS` object in the inline `<script>`. Openers are UI-seeded.
- `netlify/functions/chat.js` — the proxy. Holds:
  - `PERSONAS` map: `tami`, `sage`, `c3po`, `t800`, `data` → each `{ label, system }`.
  - One `<NAME>_SYSTEM_PROMPT` per persona; SAGE/C-3PO/T-800/Data interpolate the shared `KNOWLEDGE_BASE`.
  - `KNOWLEDGE_BASE` — researched: ego/defensiveness→curiosity, NVC (OFNR), emotional triage/flooding,
    co-regulation & attunement, asking permission/consent, AuDHD/double-empathy. (Sources in KNOWLEDGE.md.)
  - `IMAGE_NOTE` (screenshot handling) + `FORM_FACTOR` (hard brevity + the no-re-introduce rule) are
    appended to EVERY persona's system text at call time.
  - Model: per-request from the UI picker, validated against `ALLOWED_MODELS`; falls back to env
    `TAMI_MODEL`, else `claude-sonnet-4-6`. Response echoes `model`.
  - Access gate: `TAMI_ACCESS_CODE` env var (currently `betterhalf2026`); blank = open.
  - `ANTHROPIC_API_KEY` env var — required (the user's key/credits).
- `KNOWLEDGE.md` — sourced research + accuracy flags behind the knowledge base.

## Conventions / hard-won lessons (don't regress these)
- **Form factor is sacred:** texting-tight, 1–3 sentences, one idea/question per turn, no essays,
  no lists/headers. `FORM_FACTOR` enforces it. The user fired a warning shot about Claude-essay verbosity.
- **Sonnet flattens rich personas** unless the voice is made explicit WITH a concrete in-voice example
  (see T-800/SAGE/Data prompts). That's the reliable pattern to keep a character vivid on Sonnet 4.6.
- **Data uses NO contractions, ever** ("I do not", "cannot") and grounds insight in "I have observed
  that humans…". This is his signature — verify it holds.
- **Openers are UI-only** and get stripped server-side (API needs a user-first turn), so a rule in
  `FORM_FACTOR` tells personas never to re-introduce / repeat the greeting.
- Persona voices (per user direction): C-3PO frets-then-steadies, light SW lore, "young Padawan",
  occasional "sir/madam" for flavor (don't ask). T-800 = reprogrammed protector (warm, NOT menacing).
  SAGE = wise loving-but-rigorous mystic, carries world scriptures (1 John & Matthew 25 by heart),
  used as seasoning not sermon. TAMI = strict Socratic investigator (unchanged from the original spec;
  does NOT use the shared knowledge base).
- Default model is Sonnet 4.6 for cost; Opus 4.8 is the quality pick (4.8 > 4.6 at the same price).

## Open / optional next steps
- Connect Netlify's GitHub App for push-to-deploy (one browser authorize).
- Anyone with the link+code can select Opus on the user's key — gate or remove if it goes wider.
- Watch the Anthropic credit balance (it ran dry once mid-session).
- Possible additions discussed: more personas, voice tuning, paste-from-clipboard screenshots.

## The user's ORIGINAL source material is untouched
The `../TAMI/` folder (8 spec markdowns + MVP Spec v14, Safety Classifier, memos, PDFs) was READ ONLY,
never edited. The standalone `tami` agent the user can invoke lives at `../.claude/agents/tami.md`.

## To resume in a new session
Open a session in this folder and say: **"Read tami-site/HANDOFF.md and continue."**
