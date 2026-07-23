# Cloud setup guide — Gabriel's part (15 minutes, one time)

The app code for automatic cloud backup is **done and shipped**. What
remains is turning on two Firebase products in the console and pasting
the security rules. You already did the hard part (Google sign-in
provider + SHA fingerprints + google-services.json) — this finishes it.

Project: **homeperty-1659d** · App: **com.kingnerd.homeperty**

Until steps 1–2 below are done, sign-in works but every backup attempt
fails with a permission error (silently — the app just shows
"Temporary problem — retrying automatically" in Settings → Cloud backup).

---

## 1. Enable Cloud Firestore + paste its rules  ⬅ required

Firestore stores one tiny document per user: the list of their backup
versions (dates, sizes — no property data).

1. Open <https://console.firebase.google.com/project/homeperty-1659d/firestore>
2. Click **Create database**
   - Edition: **Standard** (if asked)
   - Location: **eur3 (europe-west)** — pick once, can't change later
   - Mode: **Production mode** (starts locked down — our rules open
     exactly what's needed)
   - → **Create**
3. Open the **Rules** tab → select everything in the editor → replace
   with:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid}/backupMeta/{doc} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```

4. Click **Publish**.

What this means: a signed-in user can read/write **only**
`users/<their-own-uid>/backupMeta/…`. Everything else in the database is
denied for everyone. No user can ever see another user's data.

## 2. Enable Storage + paste its rules  ⬅ required

Storage holds the actual backup files (compressed archives of the
database + photos + documents).

1. Open <https://console.firebase.google.com/project/homeperty-1659d/storage>
2. Click **Get started**
   - If it asks you to upgrade to the **Blaze (pay-as-you-go)** plan:
     accept — there is still a free monthly allowance (5 GB stored,
     1 GB/day download) and you only pay past it. With 10 versions per
     user and typical portfolios of a few MB each, one user ≈ tens of MB.
   - Location: same region it suggests (tied to the project).
   - Mode: **Production mode**.
3. Open the **Rules** tab → replace everything with:

   ```
   rules_version = '2';
   service firebase.storage {
     match /b/{bucket}/o {
       match /users/{uid}/backups/{allPaths=**} {
         allow get, list, delete: if request.auth != null && request.auth.uid == uid;
         allow create, update: if request.auth != null && request.auth.uid == uid
           && request.resource.size < 512 * 1024 * 1024;
       }
     }
   }
   ```

4. Click **Publish**.

What this means: each user can access only their own
`users/<uid>/backups/` folder (the `**` wildcard is required so the app
may also *list* the folder — used by "Delete cloud data & account"), and
no single backup can exceed 512 MB (abuse guard). Everything else is
denied.

## 3. Test on your phone (5 minutes)

1. Install the current build and redeem a Pro key (or use your already-Pro
   install).
2. The app offers **"Pro includes cloud backup — Connect Google
   account"** right after redeeming — accept, pick your Google account.
   (Or: Settings → Cloud backup → Sign in with Google.)
3. That's it — there is **no backup button**. Add or edit a property,
   then background the app (home button). The backup uploads silently.
4. Verify: Settings → Cloud backup should show **Status: ✓ Synced**,
   a "Last backup" time, and the storage used.
   You can also see the file in the console under **Storage →
   users/<uid>/backups/**.
5. Test restore: Settings → Cloud backup → **Restore backup** → pick the
   version → confirm → the app asks to restart → reopen → data is back.
6. Test the new-device flow: uninstall + reinstall (or use a second
   device/emulator with Play services), go through onboarding, connect
   the Google account → the app should say **"We found a HomePerty
   backup from …"** and offer Restore.

**If something fails:**

| Symptom | Cause |
|---|---|
| Status stuck on "Temporary problem — retrying automatically" | Rules not published (step 1 or 2), or no internet |
| Sign-in itself fails / `ApiException: 10` | Fingerprint/OAuth issue — see `FIREBASE_CONSOLE_CHECKLIST.md` §1–3 (already done; only relevant if you change signing keys) |
| Works in debug, fails from Play | Play App Signing SHA-1 missing in Firebase |
| Restore says there's no backup | The account never completed a backup — check Storage in the console |

## 4. How it behaves for users (so support questions make sense)

- **Free user**: nothing happens in the cloud, ever. They can still sign
  in (e.g. to restore a backup made when they were Pro on an old phone).
- **Pro via key**: right after redeeming, the app asks once to connect
  Google. After that, backups are fully automatic — after data changes
  (debounced ~5 min), when the app goes to background, and a daily
  catch-up. Nothing uploads when nothing changed.
- **Versions**: the last **10** backups are kept per account; older ones
  are deleted automatically. Users can restore any of the 10 from
  Settings → Cloud backup → Restore backup.
- **Offline**: changes queue; upload resumes when connectivity returns.
- **Deleting**: Settings → Cloud backup → "Delete cloud data & account"
  removes all backups and the Firebase account (Play/GDPR requirement).
- **Support access**: backups are NOT end-to-end encrypted (by design —
  no passwords for users to lose). As the project owner you can, with
  the user's permission, inspect or hand back their backup file from the
  console (Storage → users/<uid>/backups/). Follow the privacy policy:
  only with the user's authenticated request, only for support.

## 5. Costs at a glance

| Users | Firestore | Storage | Est. monthly |
|---|---|---|---|
| 100 | negligible (1 doc/user) | ~2–5 GB | free tier |
| 1 000 | negligible | ~20–50 GB | a few € |
| 10 000 | still tiny | ~200–500 GB | tens of € — revisit retention/photo caps then |

Reads/writes are minimal by design: one manifest read per status check,
one write per actual upload, and content-hash dedup means unchanged data
never re-uploads.

## 6. Later (not needed now)

- **Budget alert**: Firebase console → ⚙ → Usage and billing → set a
  budget alert (e.g. 10 €) so growth never surprises you.
- **Play Billing**: when subscriptions launch via `PurchaseGateway`,
  they activate the same Pro tier — cloud backup follows automatically,
  no console work needed.
- **Future sharing features** (family accounts, accountant access) build
  on the same `users/{uid}/…` namespace — the rules grow, they don't
  change shape.
