# Crack the Locker

A puzzle vault game in English and Hindi: 17 categories, 1,280 cases, coins and hints, a daily challenge, Blitz, stats and a shared leaderboard.

Created by Abhinav Prakash.

## What's in this folder

| File | What it does |
|---|---|
| `index.html` | The whole game |
| `firebase-config.js` | Your Firebase settings (paste them in, step 5) |
| `firestore.rules` | Security rules for the shared leaderboard (step 4) |
| `vendor/` | The Firebase library, so the site doesn't depend on another CDN |

If `firebase-config.js` is left as `null`, the game still works, but scores are saved on each device only.

## Setup (about 15 minutes)

### Firebase (free Spark plan, no card needed)

1. Go to https://console.firebase.google.com, click **Create a project**, name it (for example `crack-the-locker`), and turn Google Analytics off.
2. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable → Save.**
   Players are signed in invisibly in the background; nobody types a password.
3. **Build → Firestore Database → Create database.** Pick the location `asia-south1 (Mumbai)` and **Start in production mode**.
4. Open the **Rules** tab of Firestore, delete what's there, paste the whole of `firestore.rules`, and click **Publish**.
5. Click the gear icon → **Project settings → Your apps → Web (`</>`)**. Give it a nickname, leave Firebase Hosting unticked, and click **Register app**. Copy the `firebaseConfig = { … }` values into `firebase-config.js`, replacing `null`:
   ```js
   window.FIREBASE_CONFIG = {
     apiKey: "…",
     authDomain: "…",
     projectId: "…",
     storageBucket: "…",
     messagingSenderId: "…",
     appId: "…"
   };
   ```
   These values are meant to be public. The security rules protect the data.

### GitHub Pages

6. On GitHub, create a new **public** repository, for example `crack-the-locker`. Click **Add file → Upload files**, drag in everything from this folder (`index.html`, `firebase-config.js`, `firestore.rules`, `README.md` and the `vendor` folder), and click **Commit changes**.
7. In the repository: **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main`, folder `/ (root)` → Save.** After a minute or two the site is live at `https://<your-username>.github.io/crack-the-locker/`.
8. Back in Firebase: **Authentication → Settings → Authorized domains → Add domain** and enter `<your-username>.github.io`.

Open the site. The bar at the bottom of the categories page should say **"Live: everyone's scores show here."**

### Make yourself the admin (to clear scores)

9. On the site, open **Settings** (gear icon) and copy **Your player ID**.
10. In Firebase: **Firestore Database → Data → Start collection**, collection ID `admins`, document ID = your player ID, add any field (for example `role` = `owner`), and save.
11. Reload the site. **Clear leaderboard** (on the leaderboard page) now appears for you only.

Your player ID belongs to that browser. If you clear its data or use another device, repeat steps 9 to 11 for the new ID.

## How the shared data works

- `players/{id}`: one document per player with their runs. Only that player can add to it, one run at a time. Only an admin can delete it.
- Closed cases are personal. A case a player cracks or reveals closes only for them (kept in their browser); everyone else can still solve it. A player can replay their closed cases for practice, which never changes the leaderboard.
- The leaderboard counts each player's first solve of each case only, so replays and repeat attempts can't improve a score.
- `admins/{id}`: the admin list, edited only in the Firebase console.

Each visit reads one document per player, so the free plan (50,000 reads a day) is plenty for testing. The Spark plan has no billing, so it can't charge you; if a daily limit is ever reached, the leaderboard pauses until the next day and the game keeps working.

## Updating the game

Replace `index.html` in the repository with a new version (Add file → Upload files). Keep your `firebase-config.js`.
