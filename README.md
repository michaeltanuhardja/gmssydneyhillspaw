# GMS Hills Ministry Roster – v15

Static page (GitHub Pages) + private Google Sheet + Apps Script publisher.

```
Google Sheet (private) ──Apps Script──► data/roster.json, data/swaps.csv  (committed to this repo)
                                              │
              GitHub Pages: index.html reads data/roster.json  (public, read-only)
                                              │
        Unlock + PIN ──► Apps Script web app (PIN checked on the SERVER) ──► sheet ──► republish
```

## What is public / private
| Item | Where | Public? |
|---|---|---|
| Schedule, names, team numbers, song list, swap log | `data/roster.json`, `data/swaps.csv` | Yes |
| Leave register, volunteer roles/partners, PIN | Google Sheet only | **No** |
| GitHub token, repo name | Apps Script *Script Properties* | **No** |
| Apps Script web-app URL (`API_URL` in index.html) | index.html | Yes – but it returns **no data** and rejects any write without the PIN |

The PIN is never in the page. It is verified by the Apps Script (value of "Unlock Pin" in the
*Schedule Rule* tab). 5 wrong attempts lock all editing for 15 minutes. **Use a 6-digit PIN.**

## One-time setup
1. **GitHub**: create the repo, upload the contents of this folder (index.html, data/, README.md, .gitignore).
   Settings → Pages → Deploy from branch → `main` / root.
2. **Token**: GitHub → Settings → Developer settings → Fine-grained tokens → this repo only,
   permission *Contents: Read and write*, set an expiry (e.g. 1 year, calendar a reminder).
3. **Apps Script**: open the Sheet → Extensions → Apps Script → replace the code with `apps_script/Code.gs`.
4. Reload the Sheet. In the new menu **Ministry Roster**:
   - *GitHub settings…* → repo (`owner/name`), branch, token.
   - *Publish to GitHub now* → check `data/roster.json` appears in the repo.
   - *Turn on hourly auto-publish*.
5. **Schedule Rule** tab → set *Unlock Pin* to 6 digits.
6. Apps Script → **Deploy → New deployment → Web app** (Execute as: Me, Access: Anyone).
   Copy the `/exec` URL into `API_URL` in `index.html` and commit.
7. **Retire the old v14 deployment** (Deploy → Manage deployments → archive it). The URL
   baked into v14 gave anyone write access, so treat it as compromised.

## Day-to-day
- Generate a schedule from the sheet menu → it publishes automatically.
- Swaps / crew names made on the page (after unlocking) write to the sheet, append to the
  Swaps tab and republish. The swapper sees the change immediately; other people see it after
  GitHub Pages refreshes (usually within a few minutes).
- `data/swaps.csv` in the repo is the swap history.

## Volunteer sheet columns
Add optional columns `Soundman`, `Slides`, `Photographer`, `Dress Code QC` and put `Y` against
people. The generator auto-fills those roles (no back-to-back weeks); if nobody is flagged the
slot stays blank for manual entry.

## Notes
- `data/roster.json` in this folder is a preview generated from your xlsx; the publisher overwrites it.
- Song links and artists are published too (they were visible in v14). Remove columns in
  `buildPublicPayload()` if you don't want them public.
