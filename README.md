# TAMI test site

A tiny web app so your team can play with this version of TAMI. It's a single
static page plus one Netlify Function that holds your Anthropic API key and
proxies chat to Claude. The key never reaches the browser — teammates just open
the link and start a session.

**Live:** https://better-half-tami.netlify.app
**Access code:** `betterhalf2026` (set via the `TAMI_ACCESS_CODE` env var; change it
with `netlify env:set TAMI_ACCESS_CODE "newcode"` then redeploy, or remove the var
to make the site open).
**Remaining step:** set `ANTHROPIC_API_KEY` (see Deploy below) — until then the
function returns a "missing ANTHROPIC_API_KEY" error.

```
tami-site/
├─ index.html                  # the chat UI (no build step)
├─ netlify.toml                # Netlify config
├─ netlify/functions/chat.js   # serverless proxy; holds the TAMI system prompt + calls Claude
└─ README.md
```

- **Model:** `claude-opus-4-8`
- **Backend:** one Netlify Function calling the Anthropic Messages API
- **Behavior:** the same six-phase Socratic investigator (intake → exploring → deepening → diagnosing → intervening → closing). To change TAMI's behavior, edit `TAMI_SYSTEM_PROMPT` in `netlify/functions/chat.js`.

---

## Deploy (about 5 minutes)

You need: a Netlify account (free) and an Anthropic API key with billing enabled.

### Option A — Netlify web UI (drag & drop)

1. Go to [app.netlify.com](https://app.netlify.com) → **Add new site** → **Deploy manually**.
2. Drag the whole `tami-site` folder onto the upload area.
3. After it deploys, open **Site configuration → Environment variables → Add a variable**:
   - Key: `ANTHROPIC_API_KEY`
   - Value: your Anthropic API key (`sk-ant-...`)
4. Go to **Deploys → Trigger deploy → Deploy site** so the function picks up the key.
5. Open the site URL and share it with your team.

### Option B — Netlify CLI

```sh
npm install -g netlify-cli
cd tami-site
netlify deploy --prod
# When prompted, set the publish directory to "." and create a new site.

# Set the key, then redeploy:
netlify env:set ANTHROPIC_API_KEY "sk-ant-your-key-here"
netlify deploy --prod
```

### Option C — connect a Git repo

Push `tami-site` to GitHub, then in Netlify **Add new site → Import from Git**,
pick the repo, set **Publish directory** to `tami-site` (or `.` if the repo root
*is* the site), and add the `ANTHROPIC_API_KEY` environment variable. Every push
redeploys.

---

## Run it locally

```sh
npm install -g netlify-cli
cd tami-site
export ANTHROPIC_API_KEY="sk-ant-your-key-here"
netlify dev          # serves the page + function at http://localhost:8888
```

---

## Notes

- **Cost:** every message is one Claude API call against your key. The system
  prompt is prompt-cached, so repeat turns in a session are cheaper. Conversation
  history is capped at the last 40 messages per request to bound cost.
- **No accounts / no storage:** conversations live only in the browser tab and
  are gone on refresh. Nothing is logged server-side. This is a throwaway test
  harness, not the production MVP.
- **Anyone with the link can use it** (and spend your key). For an internal team
  test that's usually fine; if you need to lock it down, add Netlify
  password protection (Site configuration → Access control) or Netlify Identity.
- **Safety:** TAMI escalates self-harm / danger-to-life disclosures to emergency
  resources, but this is an experiment — the page footer says so. Don't position
  it as a clinical tool.
