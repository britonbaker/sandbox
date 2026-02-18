# CLAUDE.md — Wallet Memo Frontend

## What This Is

Frontend for **Wallet Memo** (walletmemo.com) — create sticky-note passes for Apple Wallet.

Single-page app: `index.html`, `style.css`, inline JS. Zero build step, zero dependencies. Vanilla JS + CDN libs (Three.js for changelog animations).

## Deployment

- **Platform:** Cloudflare Pages (`walletmemo` project)
- **Branch:** `main` (auto-deploys on push)
- **Live:** https://walletmemo.com

## Backend (Separate Repo)

The backend is **NOT in this repo**. It's a Cloudflare Worker in `clawdbrit/wallet-worker`.

- **Live:** https://wallet-api.britonbaker.com
- **Source:** `~/repos/wallet-worker/`
- **Deploy:** `npx wrangler deploy` from that repo's `master` branch

The `BACKEND_URL` constant at the top of `index.html` controls which backend the frontend talks to.

### Key Endpoints (called by frontend)

- `POST /api/pass-redirect` — mobile flow (direct redirect to pass)
- `POST /api/prepare-pass` — desktop flow (returns token)
- `GET /api/download-pass/:token` — desktop flow (QR code download)
- `GET /api/test-pass?text=hello&color=yellow` — quick test

### Pass Signing

- Apple WWDR G4 certificate + `pass.com.walletmemo.note` signer cert
- Certs stored as Cloudflare Worker secrets (P12_BASE64, WWDR_PEM)

## Test Site

- **URL:** https://clawdbrit.github.io/playground/
- **Repo:** `clawdbrit/playground` (gh-pages branch)
- Use for previewing changes before pushing to production

## ⚠️ Critical Rule

**NEVER redeploy wallet-worker without testing pass output at:**
https://wallet-api.britonbaker.com/test-pass?text=hello&color=yellow

## Git Config

- **User:** clawdbrit
- **Push:** `git push origin main`
- **Remote `origin`:** britonbaker/sandbox (production)
- **Remote `test`:** clawdbrit/playground (test site)

## File Structure

```
index.html          # The entire app
style.css           # Styles
changelog.html      # Production changelog
changelog-test.html # Test changelog
assets/             # Images, icons
arrow.json          # Lottie animation data
```
