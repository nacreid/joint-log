# Joint Log — Ion & Brit

A single-file workout logger for two people (Ion & Brit) running the 3-week peak plan.
It works the instant you open it. Data saves in your browser (localStorage) and, once you
add a Firebase config, also syncs live across every device.

The whole app is `index.html` — nothing to build or install.

---

## Publish to GitHub Pages

You just need this folder's `index.html` in a GitHub repo with Pages turned on.
Pick whichever path is easier for you.

### Option A — Web upload (no git, ~3 min)

1. Go to https://github.com/new → name it e.g. `joint-log` → **Public** (Pages is free on public repos) → **Create repository**.
2. On the empty repo page, click **uploading an existing file**.
3. Drag in **`index.html`** (and optionally `README.md`, `firestore.rules`). Click **Commit changes**.
4. Repo → **Settings** → **Pages** (left sidebar).
5. Under **Build and deployment → Source**, pick **Deploy from a branch**. Branch = **main**, folder = **/ (root)** → **Save**.
6. Wait ~1 minute, refresh. Your live URL appears at the top:
   `https://<your-username>.github.io/joint-log/`
7. Open that on your phone → **Share → Add to Home Screen** for an app-like icon.

### Option B — Git command line

From inside this `joint-log-site` folder:

```bash
git init
git add index.html README.md firestore.rules
git commit -m "Joint Log app"
git branch -M main
git remote add origin https://github.com/<your-username>/joint-log.git
git push -u origin main
```

Then do steps 4–6 from Option A to turn on Pages.

> **Note:** I couldn't push this to GitHub for you from here — that needs your GitHub
> login. The steps above are the whole job; it's genuinely a few clicks.

---

## Updating later

Editing the app = replace `index.html` in the repo (web UI: open the file → pencil icon →
paste new contents → commit; or `git add . && git commit && git push`). Pages redeploys in
about a minute.

**If you don't see the update**, it's browser cache, not a bug — hard-refresh
(iPhone: close the tab and reopen; desktop: Cmd/Ctrl+Shift+R). Your logged data is safe;
it lives in localStorage + Firebase, not in the HTML file.

---

## Local vs. cloud sync — how they coexist (no conflicts)

The app is built to run **both at once**, and they don't fight:

- **localStorage** is the instant local cache. It's what makes the app open immediately and
  work offline. Each browser/device has its own copy.
- **Firebase** (optional) is the shared source of truth across devices.

On load, the app shows your local copy right away, then — when Firebase connects — reconciles
to the cloud copy. Every change is written to localStorage instantly and pushed to Firebase
after a short delay; the live listener merges remote changes back in. So:

- No caching conflict between the two — localStorage is a cache, Firebase is the source of truth.
- Ion and Brit are stored under separate keys (`ion` / `brit`) inside one shared document, so
  normal use (each logging their own workout) never collides.

**The one honest edge case:** the cloud document is saved as a whole (last-write-wins). If the
*same account* is edited on two devices within the same few seconds, the later save wins and the
earlier one is overwritten. For two people logging their own sessions at the gym this basically
never happens. If you ever want it bulletproof (split into per-person documents so even
simultaneous edits can't overwrite each other), it's a small change — just ask.

Cloud sync is **off until you add a Firebase config** (see below). Until then it's local-only,
and the **Export / Import** buttons (header up-arrow + History tab) are your manual backup /
transfer between devices.

---

## Turn on cloud sync (Firebase) — one time, ~10 min

Free tier is far beyond what a training log needs. Full walk-through in
**`FIREBASE_SETUP.md`**. Short version:

1. Create a Firebase project → add a **Web app** → copy its config.
2. Enable **Authentication → Email/Password** and **Firestore Database**.
3. In `index.html`, replace the four `PASTE_…` values in this block near the top of the `<script>`:

   ```js
   var FIREBASE_CONFIG = {
     apiKey: "PASTE_API_KEY",
     authDomain: "PASTE.firebaseapp.com",
     projectId: "PASTE_PROJECT_ID",
     appId: "PASTE_APP_ID"
   };
   ```

4. Open the app → **Create account** (one shared login for both of you) → grab your User UID.
5. Paste the rules from `firestore.rules` (swap in your UID) → **Publish**.

The `apiKey` is meant to be public — security comes from the Firestore rules, not from hiding it.
Ion and Brit **share one login** because they share one log; switch between them with the ION/BRIT
toggle in the app, not by logging into different accounts.
