# Nora Intake — Distribution

Public distribution for the **Nora Intake** desktop app. The application **source is
private**; this repo only hosts what has to be public:

- **Landing page** (`landing/`) → served via GitHub Pages
- **Installers + auto-update manifest** → published as GitHub Releases here

Why a separate public repo: a private repo's release assets aren't anonymously
downloadable, so the landing downloads and the in-app updater (`latest.json`) need a
public home. Keeping this split lets the app source stay private.

## How it works
```
[private: Nora-Intake]  ──deploy key──▶  [release.yml here]  ──▶  Release (installers + latest.json)
   tauri.conf.json version                  builds mac + win              │
                                                                          ▼
                                          installed app ◀── auto-update ── latest.json (public URLs)
   landing/index.html ──pages.yml──▶ GitHub Pages ──download──▶ Releases
```

## Cut a release (frequent updates)
1. In the **private** repo, bump `version` in `src-tauri/tauri.conf.json`, push to `main`.
2. Here → **Actions → release → Run workflow** → set `tag` (e.g. `v0.2.0`).
   It pulls the private source, builds signed macOS + Windows, and creates a **draft** release.
3. Review the draft, then **Publish**. Installed apps pick up the update on next launch
   (the user clicks "Download & install" — bots aren't interrupted).

First install is manual (auto-update only updates already-installed apps); send users to the
landing page / the latest release.

## Secrets (Settings → Secrets and variables → Actions)
- `SRC_DEPLOY_KEY` — read-only deploy key for the private source repo
- `TAURI_SIGNING_PRIVATE_KEY` (+ `_PASSWORD`) — the updater key baked into installed apps (never rotate/lose it)
- Apple (optional, for notarization): `APPLE_CERTIFICATE`, `APPLE_CERTIFICATE_PASSWORD`,
  `APPLE_SIGNING_IDENTITY`, `APPLE_ID`, `APPLE_PASSWORD`, `APPLE_TEAM_ID`
