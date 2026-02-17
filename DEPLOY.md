# DEPLOY.md — Wallet Memo Push Checklist

## Architecture

- **Frontend:** Cloudflare Pages (`walletmemo` project), auto-deploys from `britonbaker/sandbox` `main` branch
- **Backend:** Cloudflare Worker (`wallet-worker` → `wallet-api.britonbaker.com`)
- **Test site:** GitHub Pages via `clawdbrit/playground` (gh-pages branch)
- **Live URL:** https://walletmemo.com

## Before Every Push, Ask:

### 1. Where does this go?
- **Testing/experimental** → push to `test` remote only (`git push test main`, then update `gh-pages`)
- **Production-ready** → push to `origin` (`git push origin main`)
- **Both** → push to both (only when test changes are verified and approved)

### 2. Test site rules (`clawdbrit/playground`)
- Always bump `test v{N}` in the footer version (only shows on test hostname)
- Always bump `style.css?v={N}` cache buster
- After pushing `main`, also update `gh-pages`: `git checkout gh-pages && git merge main --no-edit && git push test gh-pages && git checkout main`
- Live at: https://clawdbrit.github.io/playground/

### 3. Production rules (`britonbaker/sandbox`)
- **Never push experimental/untested changes**
- **Ask briton first** unless he explicitly said "push it live"
- No test version numbers in footer (the JS only shows them on `clawdbrit` hostname, but double-check)
- Frontend deploys via **Cloudflare Pages** (auto ~1 min after push to main)
- Live at: https://walletmemo.com

### 4. Pre-push checks
- [ ] CSS cache bust incremented? (style.css AND lang-picker.css if changed)
- [ ] Test version bumped? (test only)
- [ ] No `console.log` debug spam left in?
- [ ] Changes work on mobile? (if UI change)
- [ ] Does the Apple Wallet pass still generate? (if backend change)
- [ ] **Changelog updated?**
  - Test push → add entry to `changelog-test.html` with new version number
  - Production push → add entry to `changelog.html` if it's a notable change
  - Include translations for all 7 languages in the entry
  - Keep entries grouped by category (🆕 New, 🐛 Fix, 🎨 Design, 🔧 Improvement)

### 5. After pushing
- Verify deploy succeeded in Cloudflare Pages dashboard (or check `gh run list -R britonbaker/sandbox -L 1`)
- For test: check https://clawdbrit.github.io/playground/?v={N}
- For production: check https://walletmemo.com
- If backend (wallet-worker) changed: deploy separately with `cd ~/wallet-worker && npm run deploy`

### 6. Changelog safety
- `changelog.html` = **production** changelog (linked from walletmemo.com footer)
- `changelog-test.html` = **test** changelog (linked from test site footer)
- These are **separate files** — they cannot overwrite each other
- Both files exist in both repos, but each site only links to its own
- When pushing to production, test changelog comes along but is harmless (not linked)
- **Always add English first**, then translate to all 7 languages
- Production entries should be high-level summaries (v1.0, v1.1, etc.)
- Test entries should be granular per-build (v1, v2, v3, etc.)

## Git Remotes (local repo: ~/sandbox or C:\Users\38bri\sandbox)
- `origin` = `britonbaker/sandbox` (PRODUCTION)
- `test` = `clawdbrit/playground` (TEST)

## Quick Reference
```bash
# Test only
git push test main
git checkout gh-pages && git merge main --no-edit && git push test gh-pages && git checkout main

# Production
git push origin main

# Both
git push origin main && git push test main

# Backend (wallet-worker) — separate deploy
cd ~/wallet-worker && npm run deploy
```

## Backend (wallet-worker)
- Source: `~/wallet-worker/` (separate directory, not part of this repo)
- Deploy: `npm run deploy` from that directory
- Live at: https://wallet-api.britonbaker.com
- Dashboard: Cloudflare Workers → wallet-worker
- See `HOSTING.md` for full backend documentation
