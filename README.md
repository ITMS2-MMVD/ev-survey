# EV Charging Feasibility Survey — hosting on GitHub Pages

Three apps, **one shared Firebase database** (`ev-survey-mh`). Because all three already
point at the same Firestore project, they are **automatically in sync** — admin publishes
a worklist, Part 1 triages it, Dy.RTO/ARTO check it, Part 2 surveys it, and the admin
monitor reads it all back. Hosting them together changes nothing about the data; it just
gives every app an HTTPS web address (which the camera and GPS need).

## Files to upload (put all in the repo root)
- `index.html` ............... landing page linking the three apps
- `ev-survey-admin-panel-preloaded.html`  — Admin control panel
- `ev-online-survey-part1.html`           — Part 1 online survey + checking
- `ev-inspector-app.html`                 — Part 2 field survey
- `firestore.rules` ......... security rules to paste into Firebase (see below)

## Steps
1. Create a free GitHub account, then a **new public repository** (e.g. `ev-survey`).
   (GitHub Pages needs a public repo on the free plan. The Firebase config inside the
   HTML is fine to be public — see "Security" below.)
2. Click **Add file -> Upload files**, drag in all the files above, **Commit**.
3. Open **Settings -> Pages**. Under "Build and deployment" set **Source = Deploy from a
   branch**, **Branch = main**, **Folder = / (root)**, **Save**.
4. Wait ~1 minute. Your site goes live at:
   - `https://<your-username>.github.io/<repo>/`              (landing page)
   - `https://<your-username>.github.io/<repo>/ev-survey-admin-panel-preloaded.html`
   - `https://<your-username>.github.io/<repo>/ev-online-survey-part1.html`
   - `https://<your-username>.github.io/<repo>/ev-inspector-app.html`
5. Share the relevant link with each role. Done — they all read/write the same database.

## IMPORTANT — Firestore rules (do this or the apps will stop working)
Your database is currently in **test mode**, which **auto-expires ~30 days** after creation.
When it expires, every read/write is denied and ALL three apps break.

To fix: Firebase console -> **Firestore Database -> Rules** -> paste the contents of
`firestore.rules` -> **Publish**. These rules keep the apps working and do **not** expire.

## Security — please read
- Putting the Firebase **apiKey** in the HTML is normal and safe; it is an identifier, not
  a password. Real protection comes from Firestore **rules**, not from hiding the config.
- BUT these apps have **no real login** (they identify users only by mobile number). With
  the open rules above, anyone who has an app URL can read/write/delete the database —
  including the admin "wipe all" action. For a public URL that is a real risk.
- Hardening options (in order of effort):
  1. **Keep the URLs private** — only share with officers; do not post publicly.
  2. **Restrict the API key** — Google Cloud Console -> APIs & Services -> Credentials ->
     your browser key -> Application restrictions -> **HTTP referrers** -> add only
     `https://<your-username>.github.io/*`. Stops the key working from other sites.
  3. **Firebase App Check (reCAPTCHA)** — strongest; ensures requests come only from your
     app. Needs a small code addition; ask and it can be wired in.

## Notes
- HTTPS (which GitHub Pages gives you) is required for the **camera** and **GPS** features
  in Part 1 / Part 2. Opening the files from disk (file://) blocks geolocation.
- To update an app later, just upload the new HTML file over the old one in the repo.
